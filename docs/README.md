# Introdução

Este tutorial é uma versão online do meu tutorial de assembly 65c816 que está hospedado em [SMW Central] (https://www.smwcentral.net/). Originalmente, eu escrevi este tutorial para ensinar à comunidade SMW Central a linguagem assembly 65c816 em inglês simples. Hoje em dia, ele é lido por várias pessoas na cena de ROM hacking em geral. Portanto, decidi abrir o código deste tutorial no GitHub, para que as pessoas possam fazer melhorias ou traduções.

Embora eu seja um membro do SMW Central, este tutorial não está associado ao Super Mario World, portanto, este tutorial não foi feito para esse jogo. Em vez disso, este tutorial pode ser aplicado em todo o contexto SNES.

## A linguagem

O assembly 65c816 é a linguagem usada pelo chip \(SNES\) Ricoh 5A22 do Super Nintendo Entertainment System. Dividindo as diferentes partes do acrônimo 65c816: 816 significa que o processador pode assumir o modo de 8 bits ou no modo de 16 bits. O c significa CMOS, 65 significa que este processador pertence à família de CPUs 65xx. O processador devia ser bastante revolucionário para a época. Este tutorial explica mnemônicos/instruções \(opcodes\) e como usá-los corretamente. Este tutorial não se concentra em tópicos específicos do SNES, como os registradores do hardware.

Com ASM 65c816 você pode codificar coisas para os jogos de SNES \(como recursos personalizados para Super Mario World\). ASM é uma linguagem de programação de 2ª geração, de baixo nível em comparação com C\#, por exemplo. É um código de máquina legível, que eventualmente é traduzido em código de máquina hexadecimal. Todos os opcodes consistem em 3 letras, acompanhado de vários parâmetros.

## Agradecimentos especiais

Meus agradecimentos especiais a essas pessoas que revisaram o tutorial original no SMW Central: **[spigmike](https://www.smwcentral.net/?p=profile&id=132), [Roy](https://www.smwcentral.net/?p=profile&id=845), [smkdan](https://www.smwcentral.net/?p=profile&id=411), [S.N.N](https://www.smwcentral.net/?p=profile&id=23), [andy\_k\_250](https://www.smwcentral.net/?p=profile&id=67), [Domiok](https://www.smwcentral.net/?p=profile&id=7211), [reghrhre](https://www.smwcentral.net/?p=profile&id=4176), [ChaoticFox](https://www.smwcentral.net/?p=profile&id=3462), [Tails\_155](https://www.smwcentral.net/?p=profile&id=6151), [GreenHammerBro](https://www.smwcentral.net/?p=profile&id=18802), [Vitor Vilela](https://www.smwcentral.net/?p=profile&id=8251)**

E também agradeço muito aos [contribuintes](https://github.com/Ersanio/snes-assembly-book/graphs/contributors) deste repositório!
