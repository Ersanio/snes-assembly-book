# Processor flags
As you saw in the "8-bit and 16-bit mode" chapter earlier, the SNES can switch between 8-bit and 16-bit mode by using the opcodes REP and SEP. These affect the processor flags, which affect the behaviour of the SNES. Two of the prcoessor flags are dedicated to A and X & Y being 8-bit or 16-bit mode. There are in total 9 processor flags stored in the processor flags register as a single byte:

```text
Processor flags
Bits: 7   6   5   4   3   2   1   0

                                 |e├─── Emulation: 0 = Native Mode
     |n| |v| |m| |x| |d| |i| |z| |c|
     └┼───┼───┼───┼───┼───┼───┼───┼┘
      │   │   │   │   │   │   │   └──────── Carry: 1 = Carry set
      │   │   │   │   │   │   └───────────── Zero: 1 = Result is zero
      │   │   │   │   │   └────────── IRQ Disable: 1 = Disabled
      │   │   │   │   └───────────── Decimal Mode: 1 = Decimal, 0 = Hexadecimal
      │   │   │   └──────── Index Register Select: 1 = 8-bit, 0 = 16-bit
      │   │   └─────────────── Accumulator Select: 1 = 8-bit, 0 = 16-bit
      │   └───────────────────────────── Overflow: 1 = Overflow set
      └───────────────────────────────── Negative: 1 = Negative set
```

You can memorize these processor flags by memorizing the 'word' `nvmxdizc`. This chapter will explain all the processor flags in detail.

## Negative flag (n)
Most opcodes modify the negative flag depending on the results of that opcode. These opcodes generally are opcodes which are handled in the mathematics chapter, but also loads, comparisons and pulls.

The negative flag doesn't affect the behaviour of the SNES. Rather, there are some branches which make use of the negative flag.

## Overflow flag (v)
There are 3 opcodes which affect the overflow flag after a calculation: [`ADC`, `SBC`](../math/arithmetic.md) and [`BIT`](../math/logic.md). For detailed explanations of when the overflow flag is set, see the links.

The overflow flag doesn't affect the behaviour of the SNES. Rather, there are some branches which make use of the overflow flag.

## Memory select (m)
The memory select processor flag determines whether the accumulator is 8-bit or 16-bit. 

When it's set to 1, the accumulator is 8-bit.
When it's set to 0, the accumulator is 16-bit.

## Index select (x)
The index select processor flag determines whether the X and Y registers are 8-bit or 16-bit. 

When it's set to 1, both X and Y are 8-bit.
When it's set to 0, both X and Y are 16-bit.

## Decimal mode flag (d)
Setting this flag to 1 means the SNES enters decimal mode. This *only* affects the `ADC`, `SBC` and `CMP` opcodes.

These opcodes adjust the accumulator on-the-fly. This means that, if you for example add `$03` to `$09`, the result is `$12` instead of `$0C`.

Although decimal-mode math properly affects the carry flag and negative flag, it doesn't do this with the overflow flag.

### Binary-coded decimal
Because these numbers are stored in decimal, they're stored in 'binary-coded decimal' (BCD). BCD is basically the same as hexadecimal, with the values $0A-0F, $1A-1F, et cetera 'cut out'. Here's a table showing how counting in BCD goes like:

|Binary|Hexadecimal|Decimal|BCD|
|-|-|-|-|
|%0000 0000|$00|#00|%0000 0000|
|%0000 0001|$01|#01|%0000 0001|
|%0000 0010|$02|#02|%0000 0010|
|%0000 0011|$03|#03|%0000 0011|
|%0000 0100|$04|#04|%0000 0100|
|%0000 0101|$05|#05|%0000 0101|
|%0000 0110|$06|#06|%0000 0110|
|%0000 0111|$07|#07|%0000 0111|
|%0000 1000|$08|#08|%0000 1000|
|%0000 1001|$09|#09|%0000 1001|
|%0000 1010|$0A|Not available|Not available|
|%0000 1011|$0B|Not available|Not available|
|%0000 1100|$0C|Not available|Not available|
|%0000 1101|$0D|Not available|Not available|
|%0000 1110|$0E|Not available|Not available|
|%0000 1111|$0F|Not available|Not available|
|%0001 0000|$10|#10|%0001 0000|
|%0001 0001|$11|#11|%0001 0001|
|%0001 0010|$12|#12|%0001 0010|
|...|...|...|...|
|%1001 1000|$98|#98|%1001 1000|
|%1001 1001|$99|#99|%1001 1001|
|%1001 1010|$9A|Not available|Not available|
|%1001 1011|$9B|Not available|Not available|
|...|...|...|...|

In this mode, the SNES supports calculation results from `$00` to `$99`. In 16-bit A mode, this would be from `$0000` to `$9999`.

## Interrupt disable flag (i)
This flag determines whether the IRQ of SNES is disabled or not.

When it's set to 1, IRQ is disabled.
When it's set to 0, IRQ is enabled.

## Zero flag (z)
Most opcodes modify the zero depending on the results of that opcode. These opcodes generally are opcodes which are handled in the mathematics chapter, but also loads, comparisons and pulls.

The zero flag doesn't affect the behaviour of the SNES. Rather, there are some branches which make use of the zero flag.

## Carry flag (c)
The SNES supports math in the form of adding and subtracting numbers. It also supports bitwise operations such as bitshifting. The "carry flag" is a processor flag used for most of these arithmetic and bitshifting operations. Additionally, the carry flag is also used for branching. The carry flag is the same concept as the "carry" you learn in elementary school. In a typical pencil-and-paper addition, you'd write it out like this:
```
  ¹
  27
+ 59
----
  86
 ```
7+9 equals 16, thus *carry* the 1 to the left.

Considering the carry is a "flag", when the carry flag is clear, the carry (C) will be 0. When it's set, the carry will be 1. You can safely assume that the carry flag is the "9th bit" of the A register when A is in 8-bit mode, and the "17th bit" when A is in 16-bit mode. Assuming A is in 8-bit mode, the carry flag will look like this:
```
BBBBBBBB C
```
Where C is the Carry Flag and B are the bits – in other words the A register's contents.

Depending on the carry flag, various mathematical and bit shifting instructions will behave differently.

## Emulation mode flag (e)
Setting this flag causes the 65c816 to behave as the 6502. When you enter emulation mode:

* The stack pointer register's high byte remains static as $01
* The A, X and Y registers are always 8-bit
* The program bank and data bank registers are set to $00
* The direct page register is initialized to $0000, and the high byte remains static as $00

The emulation mode of the 65c816 also fixes some of the bugs that the 6502 had. For example, indirect addressing mode `JMP` now wraps addresses properly. For example: `JMP ($10FF)` will now get the high byte from `$1100`, rather than `$1000`.

### Processor flags
The emulation mode has a different set of processor flags.

```text
Processor flags
Bits: 7   6   5   4   3   2   1   0

                                 |e├─── Emulation: 0 = Native Mode
     |n| |v| |1| |b| |d| |i| |z| |c|
     └┼───┼───┼───┼───┼───┼───┼───┼┘
      │   │   │   │   │   │   │   └──────── Carry: 1 = Carry set
      │   │   │   │   │   │   └───────────── Zero: 1 = Result is zero
      │   │   │   │   │   └────────── IRQ Disable: 1 = Disabled
      │   │   │   │   └───────────── Decimal Mode: 1 = Decimal, 0 = Hexadecimal
      │   │   │   └─────────────────── Break Flag: 1 = Break executed
      │   │   └─────────────────────────── Unused: 1
      │   └───────────────────────────── Overflow: 1 = Overflow set
      └───────────────────────────────── Negative: 1 = Negative set
```

As you can see, it's very similar to the processor flags of the SNES, with a few exceptions. The A and X & Y memory select bits are replaced.

### Unused flag
This processor flag is unused and is always set to 1.

### Break flag
This processor flag is set to 1 when the emulation mode comes across a `BRK` opcode, thus it only indicates that there was a break; the processor flag doesn't actually affect the SNES.