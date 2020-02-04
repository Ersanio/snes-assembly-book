# Processor flags
As seen in the [previous chapter](../processor/flags.md), the SNES supports 9 processor flags. There are ways to affect these processor flags, which are explained in this chapter.

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
Because the emulation mode bit is 'hidden' above the carry flag, there's an opcode which exchanges the carry flag with the emulation mode bit.

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