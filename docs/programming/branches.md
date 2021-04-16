# Comparing, branching, labels

You can run certain pieces of code depending on certain conditions. For this, you'll have to make use of comparison and branching opcodes. Comparison opcodes compare the contents of A, X or Y with another value. A branching opcode then controls the program flow, depending on the comparison.

## Branches
Branches are opcodes which control the flow of the code depending on the outcome of comparisons. Branches jump to a **label**.

The branching opcodes have a range of -128 to 127 bytes. This means they can either jump 128 bytes backwards, or they can jump 127 bytes forward, relative to the program counter. One exception is BRL (Branch Long). BRL has a range of 32768 bytes (8000 in hex), which is a whole bank. If the branch goes out of range, the assembler gives an error. You’ll have to find a way to put the destination label into the branch’s reach. The "tips and tricks" chapter covers this.

## Labels
Labels are basically text placed in code to locate an entry point of a jump or a "table". Labels are no opcodes or anything. It’s basically an easier way to specify an offset/address, because the labels get turned into numbers by the assembler. It is good practice to give labels meaningful names, for your own sake. Examples codes in this chapter will make use of labels.

## CMP
To make comparisons, you usually compare the contents of A with something else. The primary way for that is the opcode `CMP`.

|Opcode|Full name|Explanation|
|-|-|-|
|**CMP**|Compare A|Compares A with something else|

CMP takes whatever is in A, and compares it with a specified parameter. After using a CMP instruction, you need to use an opcode that will perform the type of "branch" that you wish to occur.

It’s also possible to compare 16-bit values. Just change `CMP #$xx` to `CMP #$xxxx`.

## BEQ and BNE
There are branch opcodes which branch depending on if a value equals or doesn't equal.
|Opcode|Full name|Explanation|
|-|-|-|
|**BEQ**|Branch if equals|Branches if the comparison equals with the compared value|
|**BNE**|Branch if not equals|Branches if the comparison doesn't equal with the compared value|

BEQ branches if the comparison is equal with the compared value. Here's an example:

```
LDA $00            ; Loads the current value of RAM address $7E0000 into A
CMP #$02           ; Compares A with the immediate value $02
BEQ Label1         ; If A = $02, go to the codes in Label1. NOTE: Case-Sensitive
LDA #$01           ; \ Else
STA $1245          ; / Store value $01 into RAM $7E1245.
RTS                ; This instruction is used to end a routine.

Label1:           
STZ $19            ; Store zero in $7E0019
RTS                ; End.
```
This code will store zero ($00) in $7E0019 when $7E0000 contains the value $02. If it doesn't have $02 as its value, the code will then store value $01 in $7E1245. As you can see, BEQ will "jump" to a portion of the code when compared values are equal, skipping certain code. In this case, the code jumps to the code located at the label "Label1".

BNE branches if the comparison doesn't equal with the compared value. Here's an example:
```
LDA $00            ; Loads the current value of RAM address $7E0000 into A
CMP #$02           ; Compares A with $02
BNE Label1         ; A = NOT $02, finish the code, do nothing.
LDA #$01           ; \ Else
STA $1245          ; / Store something in RAM $7E1245
Label1:            ;
RTS                ; End.
```
This code will store $01 to $7E1245, if $7E0000 has the value $02. If RAM address $7E0000 doesn’t have the value $02, the code will instead do nothing and simply return.

## Comparing addresses
You can also compare RAM addresses with each other. For example:
```
LDA $00            ; Load $7E0000's value into A
CMP $02            ; Compare A with $7E0002
BEQ Equal          ; Branch if equal
```
When RAM addresses $7E0000 and $7E0002 have the same values, the branch will be taken.

## CPX and CPY
You can also compare values by using the registers X and Y.

|Opcode|Full name|Explanation|
|-|-|-|
|**CPX**|Compare X|Compares X with something else|
|**CPY**|Compare Y|Compares Y with something else|
It's not just A which is capable of doing comparisons. For example, you can load a value into X or Y and compare it with something else. Here's an example using X:

```
LDX $00            ; Load $7E0000's value into X
CPX $02            ; Compare X with $7E0002
BEQ Equal          ; Branch if equal
```
It will have the same result as the example with comparing addresses. You can compare Y too by using CPY. However, you cannot mix registers. The the following is wrong:
```
LDX $00
CMP $02
BEQ Equal
```
CMP $02 would try to compare address $7E0002 with the register A, instead of X. This will cause unexpected results.

## BMI and BPL
These are branch opcodes which branch depending on if a value is signed or unsigned.
|Opcode|Full name|Explanation|
|-|-|-|
|**BMI**|Branch if minus|Branches if the last operation resulted in a negative value (thus, negative flag set)|
|**BPL**|Branch if plus|Branches if the last operation resulted in a positive value (thus, negative flag clear)|

BMI branches if the last operation is a minus/negative value. Minus values are the values $80-$FF. BPL branches if the last operation is not a minus value; it branches when the value is $00-$7F.

## BCS and BCC
These are branch opcodes which branch depending on if a value is greater than or less than.
|Opcode|Full name|Explanation|
|-|-|-|
|**BCS**|Branch if carry set|Basically branches if the loaded value is greater than or equal to the compared value (thus, carry flag set)|
|**BCC**|Branch if carry clear|Basically branches if the loaded value is less than the compared value (thus, carry flag clear)|

BCS branches if the loaded value is equal or greater than the compared value. Alternatively, this also branches when the carry flag is set.

BCC branches if the loaded value is lesser than the compared value. Alternatively, this also branches when the carry flag is clear. Please note that this BCC doesn’t get taken if the compared value is equal, unlike BCS.

## BVS and BVC
These are branch opcodes which branch depending on if a value results in a mathematical overflow or not, thus, when the overflow flag is set or clear.
|Opcode|Full name|Explanation|
|-|-|-|
|**BVS**|Branch if overflow set|Branches if the comparison causes a mathematical overflow (thus, overflow flag set)|
|**BVC**|Branch if overflow clear|Branches if the comparison doesn't cause a mathematical overflow (thus, overflow flag clear)|

The "overflow" flag is a processor flag, explained later in the tutorial.

## BRA and BRL
These are unconditional branches which are always taken.
|Opcode|Full name|Explanation|
|-|-|-|
|**BRA**|Branch always|Always branches|
|**BRL**|Branch always long|Always branches, but with greater reach|

BRA will ALWAYS branch; it doesn’t even check for conditions.
BRL does the same, but it has a longer reach, enough to cover half a bank for each direction.