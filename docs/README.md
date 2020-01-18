# Introduction

This tutorial is an online version of my 65c816 assembly tutorial which is hosted on [SMW Central](https://www.smwcentral.net/). I originally wrote this tutorial in order to teach the SMW Central community the 65c816 assembly language, although nowadays it's read by various people in the ROM hacking scene in general. I decided to open source this tutorial on GitHub, so that people can make improvements or translations.

Although I'm a member of SMW Central, this tutorial is not associated with Super Mario World, thus this tutorial is not tailored towards that game. Instead, this tutorial can be applied in all SNES context.

### The language

65c816 assembly is the language used by the Super Nintendo Entertainment System's \(SNES\) Ricoh 5A22 chip. Breaking down the different parts of the acronym 65c816: 816 means that the processor can be either 8-bit mode or 16-bit mode. The c stands for CMOS, 65 means that this processor is from the 65xx CPU family. The processor is supposed to be pretty revolutionary for its time. This tutorial explains mnemonics/instructions \(i.e. opcodes\) and how to use them properly. This tutorial does not focus on SNES-specific topics, such as hardware registers.

With 65c816 ASM you can code things for SNES games \(such as custom features for Super Mario World\). ASM is a 2nd generation programming language, which is low-level compared to C\# for example. It is readable machine code, which eventually gets translated into hexadecimal machine code. All the opcodes consist of 3 letters, along with various parameters.

### Special thanks

Many special thanks go to certain members of SMW Central for teaching me assembly: **Bio,  Killozapit, MiOr, schwa, Smallhacker, smkdan, Roy**

Many special thanks go to the following members of SMW Central for reviewing this tutorial: **spigmike, Roy, smkdan, S.N.N, andy\_k\_250, Domiok, reghrhre, Chaoticfox, Tails\_155, GreenHammerBro, VitorVilela**

