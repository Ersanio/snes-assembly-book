# The stack
The stack is a special type of 'table' which is located inside the SNES RAM. Imagine the stack as a stack of plates. You can only add (push) or remove (pull/pop) a plate from the top. You can visualize it as something like this:

```
|   |
|[ ]|
|[ ]|
|[ ]|
|[ ]|
‾‾‾‾‾
```
The empty 'boxes' in above example are basically the values inside the stack, and they can hold any value. The collection of boxes are closed off from all sides, except for the top.

{% hint style="info" %}
Technically speaking, because the stack is an area inside the RAM, you can access any value you want, using special stack-relative addressing modes rather than using only push and pull opcodes. For the purposes of this chapter, we'll assume the stack indeed is a perfect stack of plates. 
{% endhint %}


## Push
'Pushing' is the act of adding a value on top of the stack. Here's an example:

```
 [$32]
  | push
  v
|     |   |[$32]|
|[$B0]|   |[$B0]|
|[$A9]| > |[$A9]|
|[$82]|   |[$B2]|
|[$14]|   |[$14]|
‾‾‾‾‾‾‾   ‾‾‾‾‾‾‾
```
As you can see, the value $32 is added on top of the stack. The stack now has five values instead of four.

## Pull/Pop
'Pulling' (or 'popping') is the act of reading a value from the top of the stack. Here's an example:

```
           [$32]
   ^ pull
   |
|[$32]|   |     |
|[$B0]|   |[$B0]|
|[$A9]| > |[$A9]|
|[$82]|   |[$B2]|
|[$14]|   |[$14]|
‾‾‾‾‾‾‾   ‾‾‾‾‾‾‾
```
As you can see, the value $32 is retrieved from the top of the stack.

{% hint style="info" %}
*All* pulling instructions affect the Negative and the Zero processor flags.
{% endhint %}

## Push A, X, Y
There are opcodes to push the current value inside the A, X or Y registers onto the stack:
|Opcode|Full name|Explanation|
|-|-|-|
|**PHA**|Push A onto stack|Pushes the current value of A onto the stack|
|**PHX**|Push X onto stack|Pushes the current value of X onto the stack|
|**PHY**|Push Y onto stack|Pushes the current value of Y onto the stack|

## Pull A, X, Y
There are also opcodes to pull the current value from the stack into the A, X or Y registers:
|Opcode|Full name|Explanation|
|-|-|-|
|**PLA**|Pull into A|Pulls a value from the stack into the A register|
|**PLX**|Pull into X|Pulls a value from the stack into the X register|
|**PLY**|Pull into Y|Pulls a value from the stack into the Y register|

## Example
Imagine the following scenario: The X register has to stay at the value $19, no matter what, but you really have to use X for something else. How would one do that? You use PHX to preserve the value in X in the stack, before you use an instruction which modifies the contents of X:
```
                   ; Imagine X has the value $19 in the stack
PHX                ; Push X ($19) onto stack. Result: Stack 1st value = #$19
LDX $91            ; Load $7E0091's value into X
LDA $1000,x        ; \ X is now modified, and we use it to index RAM
STA $0100          ; /
PLX                ; Restore X. X is now $19 again
```

{% hint style="info" %}
The A, X and Y registers do not have a separate stack. There is only one stack, specified by the 'stack pointer register'. For detailed explanations about the stack pointer register, see: [Stack pointer register](../processor/stackpointer.md)
{% endhint %}

## 16-bit mode stack operations
All the previous explanations and behaviours apply to 16-bit stack operations as well. It's just that you're pushing and pulling 16-bit values, not 8-bit values.

This also means that if you push two 8-bit values onto the stack, that you can pull a single 16-bit value later.

## Other push and pull operations
There are also other push and pull commands, which are not affected by 8-bit or 16-bit mode A, X and Y:

|Opcode|Full name|Explanation|Value size|
|-|-|-|-|
|**PHB**|Push data bank register|Pushes the data bank register's current value onto the stack|8-bit|
|**PLB**|Pull data bank register|Pulls a value from the stack into the data bank register|8-bit|
|**PHD**|Push direct page register|Pushes the direct page register's current value onto the stack|16-bit|
|**PLD**|Pull direct page register|Pulls a value from the stack into the direct page register|16-bit|
|**PHP**|Push processor flags|Pushes the processor flags register's current value onto the stack|8-bit|
|**PLP**|Pull processor flags|Pulls a value from the stack into the processor flag register|8-bit|
|**PHK**|Push program bank|Pushes the bank value of the currently executed opcode (the PHK opcode) onto the stack. There's no pull version of this|8-bit|
|**PEA $XXXX**|Push effective address|Pushes the specified 16-bit value onto the stack. Don't let the name of the opcode mislead you. This doesn't read out any address|16-bit|
|**PEI ($XX)**|Push effective indirect address|Pushes the value at the given direct page address onto the stack. The direct page register can affect the given address|16-bit|
|**PER *label***|Push program counter relative|A rather complicated push. Simply said, it pushes the absolute address of the given label onto the stack. What happens is, when you supply a label, the assembler calculates the relative distance between the PER opcode and the given label. This relative distance is a signed 16-bit value, thus the opcode finally assembles into `PER $XXXX`.|16-bit|