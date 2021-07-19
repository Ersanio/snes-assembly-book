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

An instruction without a hexadecimal parameter is only 1 byte, like `INC A` or `TAX`. An instruction with an 8-bit parameter is 2 bytes, like `LDA #$00`. An instruction with a 16-bit parameter is 3 bytes, like `LDA $0000`. An instruction with a 24-bit parameter is 4 bytes, like `LDA $000000`. It doesn't matter if the addressing mode is indexed, direct indirect or something else. It all depends on the length of the `$`-value.

## Shorthand zero comparison
Faster comparison makes use of processor flags effectively, because branches actually depend on the processor flags. As mentioned in this tutorial earlier, `BEQ` branches if the zero flag is set, `BNE` branches if the zero flag is clear, `BCC` branches if the carry flag is clear, and so on. 

Often, if the result of ​any​ operation is zero (`$00`, or `$0000` in 16-bit mode), the zero flag gets set. For example, if you do `LDA #$00`, the zero flag is set. Nearly all opcodes which modify an address or register affect the zero flag.

By making use of the zero flag, it's possible to check if a certain instruction has zero as its result (including loads). This way, you can write shorthand methods to check if an address actually contains the value `$00` or `$0000`. Here's an example:

```
LDA $59
BEQ IsZero

LDA #$01
STA $02

IsZero:
RTS
```
This code checks if address $7E0059 contains the value `$00`. If it does contain this value, then it immediately returns. If it does not contain the value `$00`, then it stores the value `$01` into address $7E0002.

The reverse is also possible: checking if an address does *not* contain zero. here's an example:
```
LDA $59
BNE IsNotZero

LDA #$01
STA $02

IsNotZero:
RTS
```
This code checks if address $7E0059 does *not* contain the value `$00`. If it indeed does not contain this value, then it immediately returns. If it does contain the value `$00`, then it stores the value `$01` into address $7E0002, instead.

## Looping
Loops are certain type of code flow which allow you to execute code repeatedly. It is especially useful when you need to read out a table or a memory region value-by-value. A practical example is reading out level data from the SNES ROM, in order to build a playable level. Levels are essentially a long list of object and sprite data, therefore, this data can be read out in a repetetive way, until you reach the end of such data.

The examples within this section only loops through tables to copy them to RAM addresses, but of course, loops can be used to implement much more complex logic.

### Looping with a loop counter check
It's possible to loop from the beginning to the end of a table. The following code demonstrates this.

```
   LDX.b #$00               ; Initialize the loop counter
-  LDA Table,x              ; Reads out the values in the table
   STA $00,x                ; Store them to addresses $7E0000-$7E0003
   INX                      ; Increase loop counter by one
   CPX.b #Table_end-Table   ; Use the size of the table as the check
   BNE -                    ; If the loop counter doesn't equal this, then continue looping.
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
This method basically keeps looping and iterating through values, until it reaches some kind of an "end-of-data" marker. Generally speaking, this value is something that the code normally never uses as an actual value. The general consensus for this value is `$FF` or `$FFFF`, although the decision is entirely up to the programmer. Here's an example which uses such a marker.

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
Would cause the following error in Asar, during assembly:
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
As you can see, it's nothing but a bunch of table entries pointing to addresses. You can use labels in order to point to ROM, or defines to point to RAM.

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
The same concept can be applied for data instead of just subroutines. Imagine you want to read level data, depending on the level number. A pointer table would be a perfect solution for that. Here's an example:

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
  STA $02          ; To use as an indirect pointer

  REP #$10
  LDY #$0000
- LDA [$00],y      ; Read level data until you reach an end-of-data marker
  CMP #$FF
  BEQ Return

  ; Do something with the loaded level data here

  INY
  BRA -
  
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
In the first section, we use the same concept of multiplying a value to access a pointer table. Except this time, we multiply by three, because the pointer tables contain values that are *long*. We use this value as an index to the pointer table, and store the pointer in `RAM $7E0000` to `$7E0002`, in little endian. After that, in the second section, we use `RAM $7E0000` as an indirect pointer and start looping through its values, using Y as an index again. We keep looping indefinitely, until we hit an "end-of-data" marker, in this case the value `$FF`. We use this method because levels could be variable in length. We also use 16-bit Y because level data *could* be bigger than 256 bytes in size. Finally, we finish the routine by setting Y back to 8-bit and then returning.

This example also shows how to use 24-bit pointers rather than 16-bit pointers. The pointer table contains long values. We use this in combination with a "direct, indirect *long*" addressing mode (i.e. the square brackets).

## Pseudo 16-bit math
It is possible to perform 16-­bit `ADC` and `SBC` without actually switching to 16-­bit mode. This is actually quite useful in cases a 16-bit value is stored across two separate addresses as two 8-bit values. This is possible with the help of the carry flag as well as the behaviour of the opcodes `ADC` and `SBC`.

Pseudo 16-bit math also works with `INC` and `DEC`, although you'd have to use them on the addresses instead of the A, X and Y registers. By making clever usage of the negative flag, it's possible to perform pseudo 16-bit math with this opcode also.

### ADC
Here's an example of a pseudo 16-bit `ADC`:

```
LDA #$F0
STA $00            ; Initialize address $7E0000 to value $F0 for this example
LDA #$05
STA $59            ; Initialize address $7E0059 to value $05 for this example
                   ; These would make the 16-bit value $05F0

LDA $00            ; Load the value $F0 into A
CLC                ; Clear Carry flag for addition. C = 0
ADC #$20           ; $F0+$20 = $10, C = 1
STA $00            ; $7E0000 has now the value $10

LDA $59            ; Load the value $05 into A
ADC #$00           ; Add $00 to $7E0005. BUT because C = 1, this adds $01 to A instead
STA $59            ; A is now $06, and we store it into $7E0059
                   ; These would now make the 16-bit value $0610
                   ; across two addresses
```
The carry flag is set after the first `ADC`. This means that the value has wrapped back to `$00` and increased from there. Because the carry flag is set, the second `ADC` adds $00 + carry, thus `$01`, thus increasing the second address by one.

### SBC
Here's an example of a pseudo 16-bit `SBC`:

```
LDA #$10
STA $00            ; Initialize address $7E0000 to value $10 for this example
LDA #$05
STA $59            ; Initialize address $7E0059 to value $05 for this example
                   ; These would make the 16-bit value $0510

LDA $00            ; Load the value $10 into A
SEC                ; Set Carry flag for subtraction. C = 1
SBC #$20           ; $10-$20 = $F0, C = 0
STA $00            ; $7E0000 has now the value $10

LDA $59            ; Load the value $05 into A
ADC #$00           ; Subtract $00 from $7E0005. BUT because C = 0, this subtracts $01 from A instead
STA $59            ; A is now $04, and we store it into $7E0059
                   ; These would now make the 16-bit value $04F0
                   ; across two addresses
```
The carry flag is cleared after the first `SBC`. This means that the value has wrapped back to `$FF` and decreased from there. Because the carry flag is cleared, the second `SBC` subtracts $00 + carry, thus `$01`, thus decreasing the second address by one.

### INC
Here's an example of a pseudo 16-bit `INC`:

```
   LDA #$FF
   STA $00          ; Initialize address $7E0000 to value $FF for this example
   LDA #$03
   STA $59          ; Initialize address $7E0059 to value $03 for this example
                    ; These would make the 16-bit value $$03FF

   INC $00          ; The value in $7E0000 is increased by 1, making it have the value $00
   BNE +            ; This sets the zero flag, thus the branch is not taken
   INC $59          ; Thus, the value in $7E0059 is also increased by 1
+  RTS              ; These would now make the 16-bit value $0400
                    ; across two addresses
```
By making clever usage of the zero flag, we know that the result of `INC $00` is actually the value `$00`, because that's the only time the zero flag is set. If the result is indeed the value `$00`, then the other address needs to be increased also.

### DEC
Here's an example of a pseudo 16-bit `DEC`:

```
   LDA #$00
   STA $00          ; Initialize address $7E0000 to value $00 for this example
   LDA #$03
   STA $59          ; Initialize address $7E0059 to value $03 for this example
                    ; These would make the 16-bit value $$0300

   DEC $00          ; Decrease the value in $7E0000 by 1
   LDA $00          ; If it results in $FF, then we wrapped from the value $00 to $FF
   CMP #$FF         ; Thus, we need to decrease the value in $7E0059 also
   BNE +
   DEC $59
+  RTS              ; These would now make the 16-bit value $02FF
                    ; across two addresses
```
As you can see, there's an extra check for the value `$FF`, because there's no shorthand way to check if the result of a `DEC` is exactly the value `$FF`. If the result indeed is the value `$FF`, then the other address needs to be decreased also.

## ADC and SBC on X and Y
Increasing and decreasing A by a certain amount is easy because of `ADC` and `SBC`. However, these kind of instructions do not exist for X and Y. If you want to increase or decrease X and Y by a small amount, you would have to use `INX`, `DEX`, `INY` and `DEY`. This quickly gets impractical if you have to increase or decrease X and Y by great numbers (5 or more) though. In order to do that, you can temporarily transfer X or Y to A, then perform an `ADC` or `SBC`, then transfer it back to X or Y. 

### Addition
Here's an example using `ADC`:
```
TXA                ; Transfer X to A. A = X
CLC                ; 
ADC #$42           ; Add $42 to A
TAX                ; Transfer A to X. X has now increased by $42
```
By temporarily transferring X to A and back, the `ADC` practically is used on the X register, instead.

### Subtraction
Here's an example using `SBC`:
```
TXA                ; Transfer X to A. A = X
SEC                ; 
SBC #$42           ; Subtract $42 from A
TAX                ; Transfer A to X. X has now decreased by $42
```
By temporarily transferring X to A and back, the `SBC` practically is used on the X register, instead.

## Checking flags
[Flags](../the-basics/binary.md), are bits which serve as some sort of an "on/off" switch on a certain property. One byte contains 8 bits, but how do you actually *check* if a flag is on or off? In ASM, when you usually use comparison and branching opcodes, you check for whole bytes rather than a single bit within that byte. There are two opcodes which are suitable for this. We will use the example from the binary chapter:
```text
10100000
││└───── "Is daytime" flag
│└───── "Is horizontal level" flag
└───── "Is raining" flag
```
Imagine this byte is stored in address $7E0095 for the examples to follow.

### The "AND" opcode
`AND` is handled in the [Bitwise Operations](../math/logic.md) chapter. By using AND, you can basically "isolate" bits and check if any of them are set or cleared. For example, if we want to check if the "daytime" flag is set, you would load the address into A, then `AND` just that one bit:

```
LDA $95
AND #%00100000     ; can also be written as #$20
BNE DayTimeIsSet   ; branches when the daytime flag is set
...
```
If that one bit in address $7E0095 is set, then the result of that `AND` will also be that A gets the value `$20`. Thus, the zero flag is cleared, and the branch is taken.

If you want to check if either of the two flags are set (e.g. the daytime flag *or* the raining flag), you'd use `AND` to check two bits, rather than one:
```
LDA $95
AND #%10100000         ; can also be written as #$A0
BNE IsRainingOrDaytime ; branches when both rain OR daytime flags are set
...
```

If you want to check if both of the two flags are set (e.g. the daytime flag *and* the raining flag), you'd have to use `AND` and then a `CMP`. Then, you'd branch if the value resulting from the `AND` is equal to the flags you want set:
```
LDA $95
AND #%10100000         ; First filter the bits
CMP #%10100000         ; Then check if both bits are set
BEQ IsRainingOrDaytime ; branches when both rain AND daytime flags are set
...
```

### The "BIT" opcode
The `BIT` opcode, which is also handled in the [Bitwise Operations](../math/logic.md) chapter, is special because it can actually check if bits 7 and 6 (bits 15 and 14 in 16-bit mode) of an address' value are set, without having to modify A. Here's an example:

```
BIT $95
BMI IsRaining          ; Branches when bit 7 (negative flag) is set
BVS IsHorizontalLevel  ; Branches when bit 6 (overflow flag) is set
...
```