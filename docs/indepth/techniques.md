# Techniques
This chapter focuses on general techniques you can use in SNES ASM.

## Byte counting
Each instruction assembles into one or more bytes. This happens as follows: First, the opcode is guaranteed assembled into a byte. Then the byte is followed by 0-3 bytes which serves as the parameter of the opcode. Every 2 hex digits equals 1 byte. This means that the maximum amount of bytes an instruction in SNES can use, is four bytes: opcode + a 24-bit parameter. Here is an example on how LDA would look like, when it's assembled with different addressing modes:

```
LDA #$00           ; A9 00
LDA #$0011         ; A9 11 00
LDA $00            ; A5 00
LDA $0011          ; AD 11 00
LDA $001122        ; AF 22 11 00
```

An instruction without a hexadecimal parameter is only 1 byte, like `INC A` or `TAX`. An instruction with an 8-bit parameter is 2 bytes, like `LDA #$00`. An instruction with a 16-bit parameter is 3 bytes, like `LDA $0000`. An instruction with a 24-bit parameter is 4 bytes, like `LDA $000000`. It doesn’t matter if the addressing mode is indexed, direct indirect or something else. It all depends on the length of the `$`-value.

## Looping
Loops are certain type of code flow which allow you to execute code repeatedly. It is especially useful when you need to read out a table or a memory region value-by-value. A practical example is reading out level data from the SNES ROM, in order to build a playable level. Levels are essentially a long list of object and sprite data, therefore, this data can be read out in a repetetive way, until you reach the end of such data.

The examples within this section only loops through tables to copy them to RAM addresses, but of course, loops can be used to implement much more complex logic.

### Looping with a loop counter check
It's possible to loop from the beginning to the end of a table. The following code demonstrates this.

```
   LDX.b #$00      ; Initialize the loop counter
-  LDA Table,x     ; Reads out the values in the table
   STA $00,x       ; Store them to addresses $7E0000-$7E0003
   INX             ; Increase loop counter by one
   CPX.b #Table_end-Table ; Use the size of the table as the check
   BNE -           ; If the loop counter doesn't equal this, then continue looping.
   RTS

Table:   db $01,$02,$04,$08 ; The values are read out in order
.end
```
The code initializes the loop counter with the value `$00`. With every iteration, this loop counter increases by one, and is then compared with the size of the table. Therefore, the loop executes for every byte within this table.

### Looping with a negative check
While it's possible to loop from the beginning towards the end of a table, it's also possible to loop from the end towards the beginning of the table. This method has a "shorthand" check in order to see if the loop should be terminated, by making clever use of the "negative" processor flag. When using this method, your loop will essentially run backwards.

```
   LDX.b #Table_end-Table-1 ; Get the length of the table ($03). The -1 is necessary, 
-  LDA Table,x              ; else the loop will be off-by-one. Use that as a loop counter.
   STA $00,x                ; One by one store the values to addresses $7E0003 to $7E0000, in that order.
   DEX                      ; Decrease the loop counter by one.
   BPL -                    ; If the loop counter isn't negative, continue looping.
   RTS

Table:   db $01,$02,$04,$08 ; The values are read out backwards as well
.end
```
Because the loop counter serves as an index to address $7E0000, as well as the table, it reads and stores the values in a backwards order, as the counter starts with the value `$03`.

The `BPL` ensures that the loop breaks once the loop counter reaches the value `$FF`. That means the negative flag will be set, thus BPL will not branch to the beginning of the loop.

This loop method has a drawback however. When the loop counter is `$81-$FF`, the loop will execute once, then break immediately, as BPL will not branch. This is because after `DEX`, the loop counter is immediately negative. Remember that values `$80` to `$FF` are considered negative. However, if your initial loop counter is `$80`, it will first execute the code within the loop, decrease the loop counter by 1, *then* check if the loop counter is negative. Therefore with this loop, you can loop through 129 bytes of data at most.

It's also possible to have the loop counter at 16-bit mode while having the accumulator at 8-bit mode. The following example demonstrates this:

```
   REP #$10
   LDX.w #Table_end-Table-1 ; Get the length of the table ($03). Notice the ".w" to force
-  LDA Table,x              ; the assembler to use a 16-bit value.
   STA $00,x                ; One by one store the values into address $7E0000-$7E0003.
   DEX                      ; Decrease the loop counter by one.
   BPL -                    ; If the loop counter isn't negative, continue looping.
   SEP #$10
   RTS

Table:   db $01,$02,$04,$08
.end
```
In this case, it's possible to loop through ‭32769 bytes of data at most.

### Looping with an "end-of-data" check.
This method basically keeps looping and iterating through values, until it reaches some kind of an "end-of-data" marker. Generally speaking, this value is something that the code normally never uses as an actual value. In most cases, it is the value `$FF` or `$FFFF`, although the decision is entirely up to the programmer. Here's an example which uses such a marker.

This type of loop is especially handy when it needs to process multiple tables with the exact same logic, but with varying table sizes. One example of such implementation is loading levels; The logic to parse levels is always the same, but the levels vary in size. 

```
   LDX.b #$00      ; Initialize the index
-  LDA Table,x     ; Read the value from the data table
   CMP #$FF        ; If it's an end-of-data marker, then exit the loop.
   BEQ +
   STA $00,x       ; If it's not, then store the value.
   INX             ; Increase the index to the table
   BRA -           ; Continue the loop
+  RTS

Table:   db $01,$02,$04,$FF
.end
```
In the case of this example, the code loops through four bytes of data, three of which are actual data and one of which is the end-of-data marker. Once the loop encounters this marker, in this case the value `$FF`, the loop is immediately terminated.

## Bigger branch reach
In the [branches chapter](../programming/branches), it is mentioned that branches have a limited distance of -128 to 127 bytes. When you do exceed that limit, the assembler automatically detects that and throws some kind of error, such as the following:

```
            LDA $00
            CMP #$03
            BEQ SomeLabel ; Branch when address $7E0000 contains the value $03
            NOP #1000     ; 1000 times "NOP"
SomeLabel:  RTS
```
Would cause the following error:
```
file.asm:3: error: (E5037): Relative branch out of bounds. (Distance is 1000). [BEQ SomeLabel]
```
This means that the distance between the branch and the label is 1000 bytes, which definitely exceeds the 127 bytes limit. If you necessarily have to jump to that one label, you can invert the conditional and make use of the `JMP` opcode for a longer jump reach:

```
            LDA $00
            CMP #$03
            BNE +         ; Skip the jump when address $7E0000 doesn't contain the value $03
            JMP SomeLabel ; This runs when address $7E0000 *does* contain the value $03

+           NOP #1000     ; Shorthand for 1000 times "NOP"
SomeLabel:  RTS
```

This way, the logic still remains the same, namely, the thousand NOPs run when address $7E0000 doesn't contain the value $03. The out-of-bounds error is solved. Additionally, replacing the `JMP` with `JML` will allow you to jump *anywhere* instead of being restricted to the current bank.

## Pointer tables
A pointer table is a table with a list of pointers. Depending on the context, the pointers can either point to code or data. Pointer tables are especially useful if you need to run certain routines or access certain data for an exhaustive list of values. Without pointer tables, you would have to do a massive amount of manual comparisons instead. Here's an example of a manual version:

```
  LDA $14
  CMP #$00
  BNE +
  JMP FirstRoutine

+ CMP #$01
  BNE +
  JMP SecondRoutine

+ CMP #$02
  BNE +
  JMP ThirdRoutine

+ RTS

FirstRoutine:
  LDA #$95
  STA $15
  RTS

SecondRoutine:
  LDA #$95
  STA $15
  RTS

ThirdRoutine:
  LDA #$95
  STA $15
  RTS
```
You can imagine that with a lot of values that are associated with routines, this comparison logic can get huge pretty quickly. This is where pointer tables come in handy.

This here is a pointer table:
```
Pointers: dw Label1
          dw Label2
          dw Label3
          dw Label4
```
As you can see, it's nothing but a bunch of table entries pointing somewhere. You can use labels in order to point to ROM, or defines to point to RAM.

### Pointer tables for code
There are a few instructions designed for making use of pointer tables. They are as follows:

|Instruction|Example|Explanation|
|-|-|-|
|**JMP (*absolute address*)**|JMP ($0000)|Jumps to the absolute address located at address $7E0000|
|**JMP (*absolute address*,x)**|JMP ($0000,x)|Jumps to the absolute address located at address $7E0000, which is indexed by X|
|**JML [*absolute address*]**|JML [$0000]|Jumps to the long address located at address $7E0000|
|**JSR (*absolute address*,x)**|JSR ($0000,x)|Jumps to the absolute address located at address $7E0000, which is indexed by X, then returns|
With these opcodes, as well as a pointer table, it is possible to run a subroutine depending on the value of a certain RAM address. Here's an example which runs a routine depending on the value of RAM address $7E0014:

```
LDA $14            ; Load the value into A...
ASL A              ; ...Multiply it by two...
TAX                ; ...then transfer it into X
JSR (Pointers,x)   ; Execute routines.
RTS

Pointers: dw Label1 ; $7E0014 = $00
          dw Label2 ; $7E0014 = $01
          dw Label3 ; $7E0014 = $02
          dw Label4 ; $7E0014 = $03

Label1:   LDA #$01
          STA $09
          RTS

Label2:   LDA #$02
          STA $09
          RTS

Label3:   LDA #$03
          STA $99
          RTS

Label4:   LDA #$55
          STA $66
          RTS
```
The short explanation is that depending on the value of RAM address $14, the four routines are executed. For value `$00`, the routine at `Label1` is executed. For value `$01`, the routine at `Label2` is executed, and so on.

The long explanation is that we load the value into A and multiply it by two, because we use *words* for our pointer tables. Thus, we need to index every two bytes instead of every byte. This means that value `$00` stays as index value `$00` thus reading the `Label1` pointer. Value `$01` becomes index value `$02`, thus reading the `Label2` pointer. Value `$02` becomes index value `$04`, thus reading the `Label3` pointer. Value `$03` becomes index value `$06`, thus reading the `Label4` pointer. Because the JSR uses an "absolute, indirect" addressing mode, the labels are also absolute, thus they only run in the same bank as that JSR.

### Pointer tables for data
The same concept can be applied for data (i.e. tables). Imagine you want to read level data, depending on the level number. A pointer table would be a perfect solution for that. Here's an example:

```
  LDA $14            ; Load the level number into A...
  ASL A              ; ...Multiply it by three...
  CLC
  ADC $14
  TAY                ; ...then transfer it into Y
  LDA Pointers,y
  STA $00
  LDA Pointers+1,y
  STA $01
  LDA Pointers+2,y ; Store the pointed address into RAM
  STA $01          ; To use as an indirect pointer

  REP #$10
  LDY #$0000
- LDA [$00],Y      ; Read level data until you reach an end-of-data marker
  CMP #$FF
  BEQ Return

  INY
  BRA -
  ; Do something with level data here
  
Return:
  SEP #$10
  RTS

Pointers: dl Level1 ; $7E0014 = $00
          dl Level2 ; $7E0014 = $01
          dl Level3 ; $7E0014 = $02
          dl Level4 ; $7E0014 = $03

Level1:   db $01,$02,$91,$86,$01,$82,$06,$FF

Level2:   db $AB,$91,$06,$78,$75,$FF

Level3:   db $D9,$B0,$A0,$21,$FF

Level4:   db $C0,$92,$84,$81,$82,$99,$FF
```
In the first section, we use the same concept of multiplying a value to access a pointer table. Except this time, we multiply by three, because the pointer tables contain values that are *long*. We use this value as an index to the pointer table, and store the pointer in RAM $7E0000 to $7E0002, in little endian. After that, in the second section, we use RAM $7E0000 as an indirect pointer and start looping through its values, using Y as an index again. We keep looping indefinitely, until we hit an "end-of-data" marker, in this case the value `$FF`. We use this method because levels could be variable in length. We also use 16-bit Y because level data *could* be bigger than 256 bytes in size. Finally, we finish the routine by setting Y back to 8-bit and then returning.

This example also shows how to use 24-bit pointers rather than 16-bit pointers. The pointer table contains long values. We use this in combination with a "direct, indirect *long*" addressing mode (i.e. the square brackets).

## Pseudo 16-bit math

## Addition and subtraction on X and Y