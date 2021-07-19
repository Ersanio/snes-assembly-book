# Common assembler syntax
In this chapter, you'll learn the art of proper assembler syntax, so that in theory, you'll can write "relative"-only code. This means that when your code shifts around (by inserting or removing new lines of code), opcodes that make use of addresses and relative addresses, such as branches or jumps, will keep their correct values.

Note that everything discussed here can also be found in Asar's documentation. However, they're important enough topics to be mentioned in this tutorial.

## Defines
Defines are basically variable definitions you can use, so your code suffers less from so-called "magic numbers". Here's an example which defines an immediate value:

```
!Value = $03

LDA #!Value
STA $01
```
Here's an example which defines an address:
```
!Address = $01

LDA #$03
STA !Address
```
Be aware that Asar actually does a simple text search and replace, rather than evaluating the expression in a define. In other words, Asar isn't smart enough to figure out that a define is an "address", "immediate value" or anything else. Here's an example of improper define usage:

```
!Value = #$03      ; Note the #

LDA #!Value        ; A search and replace turns this into "LDA ##$03"
STA $01            ; Therefore, it will throw an error!
```

## Labels
As discussed in the [branches](../programming/branches.md) and [subroutines](../programming/subroutine.md) chapters, the SNES processor can make use of labels to determine locations it can jump to. The labels used by the opcodes are replaced by actual values which denote the locations to jump to, either relative or absolute addresses.

### Sublabels
Sublabels are special type of labels which have a parent label, and are prefixed with a dot ("`.`"), and aren't suffixed with a colon ("`:`"). Sublabels are useful if you tend to use labels which aren't unique (e.g. "return"). Here's an example of a recurring "return" sublabel:

```
JSR Main
JSR Sub
RTS

Main:
LDA $10
BEQ .return
STA $11
.return
RTS

Sub:
LDA $20
BEQ .return
STA $21
.return
RTS
```
Sublabels don't have any rules in terms of writing style. You could capitalize or keep it all lowercase. In this example, it's all lowercase.

### Relative labels
Relative labels are an alternate solution to sublabels and are often used when the code is already self-documenting enough, for example, when the code needs to skip a single store depending on a branch. It saves you from thinking up a label name, such as "skipstorewhenplayerisbig". Relative labels are written using `+` and `-`. The plus is ahead of the branch instruction, while the minus is behind the branch instruction. They can be repeated as often as needed to denote different levels of depth.

Here's an example of relative labels:
```
LDA $10
BEQ +
STA $11
+
RTS
```
This code skips a single store to `$11` when address `$10` has the value `$00`.

Here's another example, demonstrating a backwards branch, causing an infinite loop:

```
- BRA -
```

Here's another example, demonstrating different levels of relative label depth:
```
LDA $10
BEQ ++
LDA $11
BEQ +
STZ $12
+
STZ $13
++
STZ $14
RTS
```
- If address `$7E0010` has the value `$00`, address `$7E0014` is cleared.
- If address `$7E0011` has the value `$00`, addresses `$7E0013` and `$7E0014` are cleared
- Else, addresses `$7E0012`, `$7E0013` and `$7E0014` all are cleared

## The art of relativity
It's possible to write programs completely devoid of fixed values and addresses (also known as "magic numbers"), by making smart use of labels and defines outside of branches and jumps. When you use labels with loading instructions, for example, it'll grab the address of the label and use it as a parameter. This was seen in the [indexing](../collections/indexing.md) chapter. However, you can also use labels as values, rather than addresses. This is especially useful when setting up indirect pointers, which is why it's important to be able to grab certain *parts* of an address rather than the full address. This is also demonstrated in the [moves](../collections/moves.md) chapter, in the "Easy notation" section.

In the following example, an `LDA` loading the address of a label as a value would look like this:
```
LDA #somelabel
STA $00
```
This is problematic, because the label assembles into a 24-bit value which is the address, and there's no LDA which accepts a 24-bit value. Instead, LDA tries to grab the largest possible supported value, thus grabs the high and low byte of the value instead (because it's 16-bit). But what if you're writing 8-bit code at that moment? The code won't run as expected, and will crash.

### Opcode length specifiers
In order to read a value at a well-defined, fixed width, you can make use of "opcode length specifiers". These are special notations appended to opcodes:
|Syntax|Definition|Description|
|-|-|-|
|.b|byte (8-bit)|Forces the parameter to be 8-bit|
|.w|word (16-bit)|Forces the parameter to be 16-bit|
|.l|long (24-bit)|Forces the parameter to be 24-bit|

In the previous example, you can force the assembler to use the low bytes of the label only, by using `.b`:
```
LDA.b #somelabel
STA $00
```

The same applies to defines. You can use defines as values, and by using an opcode length specification, you can only grab certain parts of the defines rather than the full value. For example:

```
!Size = $7FFF

LDA #!Size
STA $00
```
This would assemble as:
```
LDA #$7FFF
STA $00
```
This would be problematic in 8-bit mode, as this assembles in 16-bit mode. To fix this problem, you can use `.b`:
```
!Size = $7FFF

LDA.b #!Size
STA $00
```
This would assemble as:
```
LDA #$FF
STA $00
```

### Bitshifts
Expanding upon the previous example:
```
!Size = $7FFF

LDA.b #!Size
STA $00
```
If you wanted to store the high byte of this define in address `$7E0001`, instead of the low byte, you'd need a way to grab *only* the high byte of the definition. In order to do that, you'll have to use bitshifts:
|Syntax|Definition|Description|
|-|-|-|
|>>|Shift right|Shifts bits right n times|
|<<|Shift left|Shifts bits left n times|

Remember that a byte consists of 8 bits, thus you need to "skip" 8 bits to grab the next 8 bits we need. By bitshifting 8 times to the right, you discard the low byte of the value:

```
!Size = $7FFF

LDA.b #!Size>>8    ; Only $7F remains
STA $01
```
Bitshifts are incredibly valuable when grabbing certain portions of addresses or values. They can also be used on labels, and thus, you can also grab bank bytes:

```
LDA.b #somelabel>>16 ; Grab the bank byte of a label
STA $01
```
The same goes for defines:
```
!Address = $7E8000

LDA.b #!Address>>16 ; Grab $7E of the define
STA $02
```

### Constructing addresses
By making use of bitshifts and opcode length specifiers, it's possible to supply addresses to certain subroutines, or as indirect addresses. Here's an example which constructs an indirect address:

```
LDA.b #Sometable
STA $00
LDA.b #Sometable>>8
STA $01
LDA.b #Sometable>>16
STA $02
LDA [$00]          ; This loads the value $01 into A
RTS

Sometable: db $01,$02,$04,$08
```
### Table sizes
There are situations where it's handy to know the size of tables, such as for [moves](../collections/moves.md). To get the size of a table, you put a label at both begin and end of a table, such as this:

```
Sometable: db $01,$02,$04,$08
.end
```

Then, by using the subtract operator, "`-`", you subtract the starting and the ending address of the table, effectively getting the size of the table. For example:

```
LDA.b #Sometable_end-Sometable ; #$04
STA $00
RTS

Sometable: db $01,$02,$04,$08
.end
```
Note that it's important to use opcode length specifiers, as we're still dealing with labels, thus, 24-bit values.