# Transfers
Imagine the following situation: You're using the index register X and you'd like to increase it by #$40. There's `INX` which increases the value in X by one, but what about increasing it by $40? There's no opcode which increases the value in X by $40, [but there is one](../math/arithmetic.md) which increases the value in A by $40. How do you get the value in X into A?

There are a series of opcodes for exactly that purpose, and they're called "transfers". There are a bunch of transfer opcodes, all starting with the letter T.

## Transferring A, X and Y
There are opcodes which transfers the values between A, X and Y registers. All the combinations are possible:
|Opcode|Full name|Explanation|
|-|-|-|
|**TAX**|Transfer A to X|Copies over the value of A to X|
|**TAY**|Transfer A to Y|Copies over the value of A to Y|
|**TXA**|Transfer X to A|Copies over the value of X to A|
|**TXY**|Transfer X to Y|Copies over the value of X to Y|
|**TYA**|Transfer Y to A|Copies over the value of Y to A|
|**TYX**|Transfer Y to X|Copies over the value of Y to X|
The opcodes are self-explanatory. Here's an example of a transfer:
```
LDA #$45           ; A = $45
LDY #$99           ; Y = $99
TAY                ; A = $45, Y = $45
```
As you can see, the values are *copied* over to the target register.

## 8-bit and 16-bit mode
Transfers work as you'd expect when the source and target registers are both 16-bit mode. You transfer a 16-bit value, like so:
```
LDA #$4512         ; A = $4512
LDY #$9900         ; Y = $9900
TAY                ; A = $4512, Y = $4512
```

If you want to transfer an 8-bit register's value to a 16-bit register, the SNES looks at the target register's size, and transfers the value's low byte accordingly. Here's an example involving an 8-bit transfer to the 16-bit A register:

```
LDA #$4512         ; A = $4512
LDY #$99           ; Y = $0099
TYA                ; A = $4599, Y = $0099
```
Here's an example involving a 16-bit transfer to the 8-bit A register:
```
LDA #$45           ; A = $xx45
LDY #$99AA         ; Y = $99AA
TYA                ; A = $xxAA, Y = $99AA
```
In the second example, the `xx` could be anything. Remember that [in the introduction of the A, X and Y registers](../the-basics/register.md), it's mentioned that the A register can actually be treated to be always 16-bit. The high byte isn't touched during the transfer, even if A is in 8-bit mode. If A was `$1245` before the transfer, it'll be `$12AA` after the transfer. This doesn't apply to X and Y, as their high bytes are immediately cleared when they exit 16-bit mode.

## Transferring general registers
There are other opcodes which involve the general SNES registers.

|Opcode|Full name|Explanation|
|-|-|-|
|**TCD**|Transfer A to direct page register|Transfers the 16-bit value in A, to the direct page register, regardless of A being in 16-bit mode or not|
|**TDC**|Transfer direct page register to A|Transfer the 16-bit value in the direct page register to the A register, regardless of A being in 16-bit mode or not|
|**TCS**|Transfer A to stack pointer register|Transfers the 16-bit value in A to the stack pointer register, regardless of A being in 16-bit mode or not|
|**TSC**|Transfer stack pointer register to A|Transfers the 16-bit value in the stack pointer register to A, regardless of A being in 16-bit mode or not|
|**TXS**|Transfer X to stack pointer register|Transfers the 16-bit value in X to the stack pointer register, regardless of X being in 16-bit mode or not. Note that when X is in 8-bit mode, the high byte is always $00|
|**TSX**|Transfer stack pointer register to X|Transfers the 16-bit value in the stack pointer register to X. Note that when X is in 8-bit mode, the high byte stays $00 after the transfer|
