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


# Push
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

# Pull/Pop
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

