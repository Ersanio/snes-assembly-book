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

## Longer branches
invert branch + jmp

## Jump tables

