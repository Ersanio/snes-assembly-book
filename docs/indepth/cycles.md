# Machine cycles

The SNES processes instructions, but each opcode takes up a predetermined amount of time to execute. The time an instruction takes to execute is called “machine cycle” (or "cycle" in short).

Each opcode has its own cycle. [This page](https://wiki.superfamicom.org/65816-reference) has a full reference of how many cycles each opcode takes. Pay attention to the footnotes, as the amount of used cycles can differ depending on the context of the code. For example, a taken branch takes 1 cycle longer.

The less cycles, the less slowdown the code suffers from. This is often noticeable in the form of slowdown in the SNES game itself. To avoid slowdown, you need to write efficient code. Here is an example of inefficient vs. efficient code:

```
; Inefficient
LDA #$00	   ; 2 cycles
STA $7E0000	   ; 5 cycles
               ; = 7 cycles
; Efficient
LDA #$00       ; 2 cycles
STA $00        ; 3 cycles
               ; = 5 cycles

; Very efficient
STZ $00        ; 3 cycles
               ; = 3 cycles
```
At first, we use a full notation to write the value $00 to address $7E0000. But then, in the next example, we shorten the address, saving 2 cycles. Finally, we figure that we can use `STZ`, as we store zero to an address anyway.

Having a low cycle count is especially important when executing code during an NMI, because there are limited machine cycles there. Exceeding that limit causes flickering black scanlines to appear from the top of the screen.