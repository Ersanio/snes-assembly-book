# Little-endian

Inside the SNES memory, 16-bit and 24-bit values are always stored in "little-endian". Take for example the value $1234 which we store in the RAM; $1234 does not appear as $12 $34. It appears as $34 $12, instead. This is how the SNES works. When this number is read in 16-bit mode, it reads $1234, NOT $3412. The SNES reverses this automatically again.

24-bit values are no exception. Values, such as $123456, are stored in the memory as $56 $34 $12.

You can write everything in normal ASM without worrying about little-endian, because everything is dealt with automatically by the SNES and the assembler! You can worry about little-endian when you deal with 16-bit values in 8-bit mode. 

For example: if you ever store the value $1234 at address $7E0000, it is stored as $34 $12. Then, if you ever want to access the low byte of $1234 (which is $34), you would need to read $7E0000, NOT $7E0001.

The concept of little-endian is especially important when dealing with "pointers", which is explained later in this tutorial.