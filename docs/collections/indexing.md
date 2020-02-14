# Tables and indexing
Indexing is the act of accessing a *table of data* from a certain offset, that offset being determined by an indexer. The registers X and Y are unmissable when it comes to indexing. They're called the 'indexer' registers, after all.

## Tables
'Tables' are simply a sequence of values. They are used in at least two cases:
* Conditional cases (e.g. a collection of speed values, depending on the entity's direction)
* Values that represent an 'asset' (e.g. graphics data, music data, level data)

Tables have very practical applications in SNES games. You can, for example, store text data in tables. Or level data. Or palette data. Tables can contain anything and can have any size, as long as they fit within the ROM.

There are four types of values which you can use to write tables:
|Instruction|Full name|Explanation|
|-|-|-|
|**db**|direct byte|A value denoting a byte (8-bit value, e.g. $XX)|
|**dw**|direct word|A value denoting a word (16-bit value, e.g. $XXXX)|
|**dl**|direct long|A value denoting a long (24-bit value, e.g. $XXXXXX)|
|**dd**|direct double|A value denoting a double (32-bit value, e.g. $XXXXXXXX)|

In this case, the bytes are a “byte”, not “word” or “long”, hence “db”: direct byte. The table in this example serves no other purpose than demonstrating indexing. In this case, the table is located somewhere inside the ROM. Tables are preceded by a label so that you can refer to it easily within your code.

Here's an example of a table definition:
```
ValuesTableExample: db $03,$86,$91,$38,$22
```
As you can see, we first define the value type as 'byte' by writing 'db'. Then, we follow with byte-values, seperated by a comma (,).

In order to access a table, we always use a label. In this case, we used the label 'ValuesTableExample'.

## Indexing
Tables can be accessed using a label. Labels are translated into ROM memory addresses, after all. Expanding on above table example, the following code would read out a value from the table:

```
LDA ValuesTableExample
STA $00
RTS

ValuesTableExample: db $03,$86,$91,$38,$22
```
However, what this code does is always reading the first value in that table, the value $03, and storing it into RAM $7E0000. This is because we haven't used any indexer.

```
LDY #$03                    ;Y is now loaded with the number $03
LDA ValuesTableExample,y    ;Load a number from the table into A, using Y as index
STA $00
RTS

ValuesTableExample: db $03,$86,$91,$38,$22
```
The code will load a value from the table into A. Basically, this does `LDA ValuesTableExample`, and the value in Y, which is $03, is added to the address. In other words, in higher-level programming languages it'd be something like `ValuesTableExample[3]`. You start counting index from **zero**. Index 0 of that table has the value $03, index 1 has the value $86, and so on. The 3rd index value is $38 in this case, so this code example actually loads $38 in A, and stores it in RAM $7E0000.

Indexing is quite useful when you don’t want to write very repetitive instructions all the time. Indexing can be performed with the X register too. X and Y behave exactly the same, after all.

Indexing is actually one of the many addressing modes. The basic addressing modes are covered [here](../the-basics/addressing.md) and the more advanced addressing modes [here](../indepth/addressing.md).

## Indexing with RAM
So far, the above examples were about ROM. You can also index values in RAM. You can treat the RAM as one giant table you could index. You simply replace loaded label with an address. For example:

```
LDX #$03
LDA $7E1000,x
STA $00
```
In this case, the LDA would resolve into address `$7E1003`. The code loads $7E1003's value into A, and stores it into $7E0000.