# Jumping to subroutines

What if you want to use the same code twice, but you don’t want to write, for example, the exact same 200 lines of codes again? You can turn code into "subroutines" and use jumping opcodes to run these subroutines. There are four jumping opcodes in total.

There's a difference between "subroutine" and "code" in this tutorial, as you'll see in the coming paragraphs.

## JSR and JSL
There are two opcodes which pretty much act like a function call in higher-level programming languages.

|Opcode|Full name|Explanation|
|-|-|-|
|**JSR**|Jump to subroutine|Jumps to a subroutine using an absolute address|
|**JSL**|Jump to subroutine long|Jumps to a subroutine using a long address|

These opcodes call a piece of code, then continue executing code below said opcode. Here is an example of the usage of JSR:
```
LDA #$01
STA $01
JSR Label1	;Execute the “subroutine” located at Label1 (current bank)
LDA #$03	;The RTS in Label1 will return to this line
STA $00
RTS

Label1:
LDA #$02
STA $02
RTS
```
This code will store $01 into $7E0001, will store $03 into $7E0000 AND execute the codes at Label1 at the current bank, so it also stores $02 into $7E0002.

JSL has the same purpose as JSR, except it can jump everywhere. Above example can also be applied to JSL, except that the `RTS` is now an `RTL`.

The opcode JSR will get assembled as `JSR $XXXX` by the assembler (because labels get turned into addresses), but you shouldn’t worry about that. Furthermore, because JSR uses an absolute address as its parameter, it is limited to its current bank. Changing the data bank register doesn't affect JSR.

The opcode JSR will get assembled as `JSL $XXXXXX`, instead.

## RTS and RTL
When you call a subroutine, you also need a way to return back to your original code. For that, there are two return opcodes.
|Opcode|Full name|Explanation|
|-|-|-|
|**RTS**|Return from subroutine|Returns from a JSR|
|**RTL**|Return from subroutine long|Returns from a JSL|
Code called by JSR and JSL should end with RTS and RTL, respectively.

## JMP and JML
If you'd like to control the code flow rather than doing a function call, you will use regular jumps instead.
|Opcode|Full name|Explanation|
|-|-|-|
|**JMP**|Jump|Jumps to code using an absolute address|
|**JML**|Jump long|Jumps to code using a long address|

What JMP and JML do is jumping to another location and executing the code there, ignoring everything after the JMP/JML opcode. The RTS in Label1 does NOT jump back to LDA #$03. Instead, it just finishes the current subroutine it was in.
```
JML Label1

LDA #$03    ;Ignored
STA $00     ;Ignored
RTS         ;Ignored

Label1:
STZ $00
RTS
```

JMP is limited to the current bank, like JSR. JML can jump anywhere like JSL. JMPs and JMLs don’t have a return instruction, but you can still use an RTS or RTL to return from a JMP/JMP depending on the current situation. Example:
```
LDA #$01
STA $00
JSR Label1  ;Jump to subroutine "Label1"
RTS         ;Label2 returns here. The code ends here.

Label1:
JMP Label2	;Jump to Label2

Label2:
RTS	        ;This DOESN’T return to Label1. Instead, it returns to the RTS above.
```

It is good practice to put a blank line after a JMP or JML, so the reader knows that the program flow changes from that point on.

## Additional notes
The Data Bank does NOT get updated when you use a JSL or JML. You will have to do that yourself.

Remember how JSL/JML can jump everywhere? They can even jump to RAM, which implies code can be executed in RAM. You can write values to RAM which can be interpreted as code which can be executed.