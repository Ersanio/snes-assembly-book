# Programming cautions

When you're coding, you will have to keep an eye out on (common) mistakes.

## Confusing 8-bit values with 16-bit values
Don't try to load an 8-bit value into AXY when AXY is in 16-bit mode, and the other way around. For example, don't write `LDA #$0000` when A is in 8-bit mode, as the third byte of this opcode will be interpreted as an opcode rather than a value.

Consequence(s): The game will most likely crash by interpreting instructions you never wrote.

Fixing the issue: Use the correct value size.

## Looping conditions
When creating loops, don't make a small mistake which results in an infinite loop (a loop which doesn't exit).

Consequence(s): The game will lock up; the only way to exit is to reset the SNES.

Fixing the issue: Check at the end of the loop (often a comparison) to see why it doesn't allow the loop to exit. You might need a debugger for this.

## Bank boundaries and edge cases
Make sure your code doesn't cross bank-boundaries ($XX:FFFF → $XX:0000) inside the ROM.

Consequence(s): The SNES would read bogus instructions and most likely crash.

Fixing the issue: The code should remain within a bank. If your code doesn't fit inside a bank, you should split up your code across banks and make use of the `JSL` and `JML` instructions.

## Pushes and pulls
Make sure you pull the same amount of bytes as you have pushed, before a return instruction (RTS, RTL).

Consequence(s): If you don't, the SNES won't get the right return address from the stack and most likely crash.

Fixing the issue: Keep the amounts of pushes and pulls at an equilibrium. Especially keep an eye out on different A, X and Y modes (8-bit and 16-bit), as pushing in 16-bit mode means pulling twice in 8-bit mode. The other way around is also true.
