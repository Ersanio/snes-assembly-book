# Loading and storing

The first thing you definitely should know when starting off with assembly, is how to load and store data using various the SNES registers. The basic opcodes for loading and storing data are `LDA` and `STA`. 

As mentioned earlier, there are 3 main registers: 
* A (the accumulator)
* X (index)
* Y (index)

Although these registers can be either in 8-bit or 16-bit mode, in this tutorial we will consider them 8-bit by default.

## LDA and STA

|Opcode|Full name|Explanation|
|-|-|-|
|**LDA**|Load into accumulator|Load a value into A|
|**STA**|Store from accumulator|Store A's value into an address|

We will use RAM addresses for the sake of simplicity. Here is an example for loading and storing values.

```
LDA #$03           ; A = $03
STA $7E0001
```

We will look at this code line by line.
```
LDA #$03
```
This loads the value `$03` into A. The "#" means that we're loading an actual value, not an address. After this instruction, the content of the A register is now `$03`. LDA can load values into A, ranging from `$00-$FF` in 8-bit mode and `$0000-$FFFF` in 16-bit mode.
```
STA $7E0001
```
This stores A's value into the RAM address $7E0001. Because A's value was $03, RAM address $7E0001's value also is now $03. The contents of the A register is *not* cleared. This means you can chain multiple stores, like this:

```
LDA #$03
STA $7E0001
STA $7E0053
```

A common beginner's mistake is writing `STA #$7E0001` or any form of "STA #$". This instruction doesn't exist. It also doesn't make sense; there's no logic behind storing the value of A into another value.

{% hint style="info" %}
Remember, using $ instead of #$ after an opcode means that the parameter is an address, not an immediate value.
{% endhint %}

Putting a semicolon (;) will allow everything beyond that to be ignored by the assembler, during the assembly of the code. In other words, ; is used to place comments. Example:

```
LDA #$03           ; This is a comment!
```
### Loading and storing addresses

Of course, what would be the use to store things to a RAM address when you don’t know how to access the address again? You can load a RAM address’ contents into the A register by using LDA with a different addressing mode. Here is an example.

```
LDA $7E0013
STA $7E0042
```
Again, we will look at this example line by line.
```
LDA $7E0013
```
This will load the contents of the RAM address `$7E0013` into A. Let’s assume that the contents were `$33`. So now, A has the value `$33`. The content of `$7E0013` remains unchanged, because LDA copies the number rather than extracting it from the address. Note that this time we have used $ instead of #$. This is because we wanted to access a RAM address. In the end, A has `$33` and RAM `$7E0013` has the value `$33` also.

```
STA $7E0042
```
This instruction will store the contents of the A register into the RAM address `$7E0042`. Of course, A will remain unchanged. RAM address `$7E0042` is now `$33`. In short: the full example will copy the contents of `$7E0013` over to `$7E0042`.

## LDY, STY, LDX, STX

Now that we have learned the basics of loading and store values into addresses, let’s introduce the same loading opcodes, but for the index registers:
|Opcode|Full name|Explanation|
|-|-|-|
|**LDY**|Load into Y|Load a value into Y|
|**STY**|Store from Y|Store Y's value into an address|
|**LDX**|Load into X|Load a value into X|
|**STX**|Store from X|Store X's value into an address|

The above opcodes behave exactly like LDA and STA. The only difference is that these make use of the X and the Y registers instead of the accumulator. For example:
```
LDY #$03
STY $0001
```
Would store the number $03 into RAM address $7E0001, by utilizing the Y register. To use the X register, use LDX and STX. As for why the address is $0001 instead of $7E0001, please refer to this chapter: [Shorter addresses](./shorter-addresses.md)

## STZ
There is another opcode which stores the number $00 into addresses directly.

|Opcode|Full name|Explanation|
|-|-|-|
|**STZ**|Store zero to memory|Sets the value of an address to 0|

This opcode stores the number $00 into an address. It doesn’t even need the A, X or Y registers to load $00 first.

If you want to make a code that directly stores $00 in a RAM address, you could make it use 1 line:
```
STZ $01            ; $7E0001 = $00. The A register is unaffected.
```
STZ will store zero to a specified RAM address. After this opcode, RAM address $7E0001 will now contain the number $00. Using STZ when A is in 16-bit mode **will** store $0000 to both RAM addresses $7E0001 and $7E0002.
