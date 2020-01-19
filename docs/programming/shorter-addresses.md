# Shorter addresses
It’s possible to shorten addresses, but there are prerequisites. 

In order to shorten a long RAM address into an absolute (4-digit) address, the address has to be between $7E0000-$7E1FFF. $7E1234 can be shortened to $1234 for example. If you shorten address $7E2000 or higher into a 4-digit address, you’ll write to areas other than the RAM. It has to do with the data bank register and the SNES memory map.

If you want to shorten long RAM addresses to a direct page (2-digit) address, the high and low bytes of the long address must never exceed the value $00FF. The address you want to store to must be in bank $00 or $7E. So you can shorten `LDA $7E0001` to `LDA $01` and `STA $000001` to `STA $01`. 

It's often necessary to write shorter addresses as parameters, as certain opcodes don't support certain addressing modes. For example, the STZ opcode does not support long addresses, so you can't write `STZ $7E1234`. You'll have to write `STZ $1234` instead.

Keep in mind that when you use 2 digits for loading and storing, the bank is always $00 by default, regardless of the data bank! You can use 2-digit addresses for RAM addresses $7E0000-$7E00FF, because RAM $7E0000-$7E1FFF is mirrored in banks $00-$3F by default.