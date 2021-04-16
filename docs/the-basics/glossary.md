# Glossary
Here are some definitions of the most commonly used terms throughout the tutorial. For the sake of context and continuity, it's best read from top to bottom.

| Terminology | Definition |
|-|-|
|SNES|Super Nintendo Entertainment System|
|Memory|The working space in the SNES in which the ROM, RAM and SRAM are present|
|ROM|Read-only memory; the .smc/.sfc/.fig/*.etc files|
|(W)RAM|(Work) Random-access memory|
|SRAM|Static random-access memory; the .srm files|
|Register|A variable in the SNES not part of the standard SNES memory.|
|Opcode|A three-letter instruction; a command|
|Addressing mode|An optional parameter for an opcode denoting a value or an address|
|Instruction/Operation|The combination of an opcode and optionally an addressing mode|
|Machine code|An instruction assembled into bytes, which can be understood by processors|
|Value|A magnitude, quantity, or number; a number representing information|
|Signed|A value that semantically allows itself to be negative as well|
|Unsigned|A value that semantically allows itself to be positive-only, allowing for greater positive numbers|
|Address|A location in the memory of the SNES. Ranges from $000000 to $FFFFFF|
|Long address|An address represented by a 6-digit hexadecimal notation (e.g. $001200)|
|Absolute address|An address represented by a 4-digit hexadecimal notation of the final 4 digits (e.g. $1200)|
|Direct page|An address represented by a 2-digit hexadecimal notation of the final 2 digits (e.g. $00)|
|Byte|An 8-bit value|
|Word|A 16-bit value|
|Long|A 24-bit value|
|Double|A 32-bit value|
|Bank byte|The first two digits of a long address or a long value (e.g. '$12' in $123456)|
|High byte|The middle two digits of a long address, absolute address, long value or word value (e.g. '$34' in $123456 or $3456)|
|Low byte|The final two digits of a long address, absolute address, long value or word value (e.g. '$56' in $123456 or $3456)|