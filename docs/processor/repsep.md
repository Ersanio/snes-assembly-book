# Processor flags
As you saw in the "8-bit and 16-bit mode" chapter earlier, the SNES can switch between 8-bit and 16-bit mode by using the opcodes REP and SEP. These affect the processor flags, which affect the behaviour of the SNES. Two of the prcoessor flags are dedicated to A and X & Y being 8-bit or 16-bit mode. There are in total 8 processor flags stored in the processor flags register as a single byte:

```
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

In above diagram, `m` and `x` are described as "Index Register/Accumulator select". The index register is in fact the X and Y 8-bit or 16-bit mode register, while the accumulator select is the same, just for the A register.

## SEP
|Opcode|Full name|Explanation|
|-|-|-|
|**SEP**|Set processor flags|Sets specified processor flags to 1|

SEP sets the selected processor flag bits to 1.

SEP is used as following:
```
;nvmxdizc = 0000 0000
SEP #$80 ;= 1000 0000
;Nvmxdizc = 1000 0000
```
The uppercased letters are the activated processor flags. This code sets the negative flag.

## REP
|Opcode|Full name|Explanation|
|-|-|-|
|**REP**|Reset processor flags|Sets specified processor flags to 0|

REP resets the selected processor flag bits to 0.

REP is used as following:
```
;NvmxDizc = 1000 1000
REP #$08 ;= 0000 1000
;Nvmxdizc = 1000 0000
```
In the beginning, the decimal mode was enabled, and the negative flag was set, but after `REP #$08`, the decimal mode flag got disabled and the negative flag is still set.

## XCE and the emulation mode
You might be wondering why that `e` in the diagram is at such a peculiar position.

It is the ‘hidden’ emulation mode of the SNES. When it is set, SNES basically acts like 6502 (NES CPU), which is far more limited. While in emulation mode, the accumulator, X and Y register are forced to be 8-bit and one of the processor flag is used to indicate BRK. It also uses different vectors for interrupt. You can’t set the emulation mode using REP and SEP. Emulation mode is basically an ‘improved’ 6502 processor emulation.

|Opcode|Full name|Explanation|
|-|-|-|
|**XCE**|Exchange carry and emulation|Swaps the values of carry flag and emulation mode flag|
You can access the Emulation Mode by using `SEC` then `XCE`, and leave it using `CLC` then `XCE`. By default, SNES starts up in emulation mode. This is why you often see the opcodes `CLC` and `XCE` among the very first opcodes in disassemblies.

## Other opcodes
There are other opcodes which immediately affect a single processor flag.

|Opcode|Full name|Explanation|
|-|-|-|
|**SEI**|Set interrupt disable flag|Sets the interrupt disable flag, setting it to 1, thus disabling IRQ|
|**CLI**|Clear interrupt disable flag|Clears the interrupt disable flag, setting it to 0, thus enabling IRQ|
|**SED**|Set decimal flag|Sets the decimal mode flag, setting it to 1, changing the SNES into decimal mode|
|**CLD**|Clear decimal flag|Clears the decimal flag, setting it to 0, changing the SNES into hexadecimal mode|
|**SEC**|Set carry flag|Sets the carry flag, setting itt o 1|
|**CLC**|Clear carry flag|Clears the carry flag, setting it to 0|
|**CLV**|Clear overflow flag|Clears the overflow flag, setting it to 0|