# Hardware math
The SNES processor is capable of [basic multiplication and division by 2ⁿ](../math/shift.md), but if you'd like to multiply or divide by other numbers, you'll have to make use of certain SNES hardware registers.

## Hardware Unsigned Multiplication
The SNES has a set of hardware registers used for unsigned multiplication:
|Register|Access|Description|
|-|-|-|
|$4202|Write|Multiplicand, 8-bit, unsigned.|
|$4203|Write|Multiplier, 8-bit, unsigned. Writing to this also starts the multiplication process.|
|$4216|Read|Unsigned multiply 16-bit product, low byte|
|$4217|Read|Unsigned multiply 16-bit product, high byte|
After you write to `$4203` to start the multiplication process, you will need to wait 8 [machine cycles](../indepth/cycles.md), which is typically done by adding four `NOP` instructions to the code. If you don't wait 8 machine cycles, the results are unpredictable.

Here's an example of `42 * 129 = 5418` (in hexadecimal: `$2A * $81 = $152A`):
```
LDA #$2A           ; 42
STA $4202
LDA #$81           ; 129
STA $4203
NOP                ; Wait 8 machine cycles
NOP
NOP
NOP
LDA $4216          ; A = $2A (result low byte)
LDA $4217          ; A = $15 (result high byte)
```

## Hardware Signed Multiplication
There's a set of hardware registers which can be used for fast, signed multiplication:
|Register|Access|Description|
|-|-|-|
|$211B|Write twice|Multiplicand, 16-bit, signed. First write: Low byte of multiplicand. Second write: High byte of multiplicand|
|$211C|Write|Multiplier, 8-bit.|
|$2134|Read|Signed multiply 24-bit product, low byte|
|$2134|Read|Signed multiply 24-bit product, middle byte|
|$2134|Read|Signed multiply 24-bit product, high byte|
There's a catch to using these hardware registers, however, as they double as certain Mode 7 registers as well:

- You can only use them for **signed** multiplication
  - The result is signed 24-bit, meaning the results range from `-8,388,608` to `8,388,607`.
- The results are instant. That means you don't have to use `NOP` to wait for the results.
- You cannot use them when Mode 7 graphics are being rendered on the screen.
  - This means that when Mode 7 is enabled, you can only use them inside NMI (v-blank).
  - This also means that you can use them without any restrictions, outside of Mode 7.

Note that register `$211B` is "write twice". This means that you have to write an 8-bit value twice to this same register which in total makes up a 16-bit value. First, you write the low byte, then the high byte of the 16-bit value.

Here's an example of `-30000 * 9 = -270000` (in hexadecimal: `$8AD0 * $09 = $FBE150`):

```
LDA #$D0           ; Low byte of $8AD0
STA $211B
LDA #$8A           ; High byte of $8AD0
STA $211B          ; This sets up the multiplicand

LDA #$09           ; $09
STA $211C          ; This sets up multiplier

LDA $2134          ; A = $50 (result low byte)
LDA $2135          ; A = $E1 (result middle byte)
LDA $2136          ; A = $FB (result high byte)
                   ; (= $FBE150)
```
## Hardware Unsigned Division
The SNES has a set of hardware registers used for unsigned division. They are laid out as follows:
|Register|Access|Description|
|-|-|-|
|$4204|Write|Dividend, 16-bit, unsigned, low byte.|
|$4205|Write|Dividend, 16-bit, unsigned, high byte.|
|$4206|Write|Divisor, 8-bit, unsigned. Writing to this also starts the division process.|
|$4214|Read|Unsigned division 16-bit quotient, low byte|
|$4215|Read|Unsigned division 16-bit quotient, high byte|
|$4216|Read|Unsigned division remainder, low byte|
|$4217|Read|Unsigned division remainder, high byte|

Quotient means how many times the dividend can "fit" in the divisor. For example: `6 / 3 = 2`. Thus, the quotient is 2. Another way you can read this is: You can extract 3 **two** times from 6 and end up with exactly 0 as leftover.

Modulo is an operation that determines the remainder of the dividend that couldn't "fit" into the divisor. For example: `8 / 3 = 2`. You can subtract 3 two times from 8, but at the end, you have a 2 as a remainder. Thus, the remainder for this equation is `2`. Because there are hardware registers that support remainders, the SNES also supports the modulo operation.

After you write to `$4206` to start the division process, you will need to wait 16 [machine cycles](../indepth/cycles.md), which is typically done by adding eight `NOP` instructions to the code. If you don't wait 16 machine cycles, the results are unpredictable.

Here's an example of `256 / 2 = 128` (in hexadecimal: `$0100 / $02 = $0080`):
```
LDA #$00
STA $4204
LDA #$01           ; Write $0100 to dividend
STA $4205
LDA #$02           ; Write $02 to divisor
STA $4206
NOP                ; Wait 16 machine cycles
NOP
NOP
NOP
NOP
NOP
NOP
NOP
LDA $4214          ; A = $80 (result low byte)
LDA $4215          ; A = $00 (result high byte)
LDA $4216          ; A = $00, as there are no remainders
LDA $4217          ; A = $00, as there are no remainders
```

Here's an example demonstrating modulo: `257 / 2 = 128, remainder 1` (in hexadecimal: `$0101 / $02 = $0080, remainder $0001`)
```
LDA #$01
STA $4204
LDA #$01           ; Write $0101 to dividend
STA $4205
LDA #$02           ; Write $02 to divisor
STA $4206
NOP                ; Wait 16 machine cycles
NOP
NOP
NOP
NOP
NOP
NOP
NOP
LDA $4214          ; A = $80 (result low byte)
LDA $4215          ; A = $00 (result high byte)
LDA $4216          ; A = $01, as there is a remainder (remainder low byte)
LDA $4217          ; A = $00 (remainder high byte)
```

There is no hardware signed division.