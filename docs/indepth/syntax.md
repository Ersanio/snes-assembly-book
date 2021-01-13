# Sintaxe comum para assembler
Neste capítulo, você aprenderá a arte da sintaxe apropriada do assembler, de forma que, em teoria, você poderá escrever código "relativo" apenas. Isso significa que quando seu código muda (inserindo ou removendo novas linhas de código), opcodes que fazem uso de endereços e endereços relativos, como branches ou saltos, manterão seus valores corretos.

Perceba que tudo discutido aqui, pode ser encontrado, também, na documentação do Asar. Todavia, são tópicos importantes para serem mencionados neste tutorial.

## Defines
Defines são basicamente definições de variáveis que você pode usar, então seu código sofre menos com os chamados "números mágicos". Aqui está um exemplo que define um valor imediato:

```
!Value = $03

LDA #!Value
STA $01
```
Abaixo, um exemplo que define um endereço:

```
!Address = $01

LDA #$03
STA !Address
```
Esteja ciente de que Asar realmente faz uma pesquisa e substituição de texto simples, em vez de avaliar a expressão em um definir. Em outras palavras, Asar não é inteligente o suficiente para descobrir que o define é um "endereço", "valor imediato" ou qualquer outra coisa. Aqui está um exemplo de uso de define impróprio:

```
!Value = #$03      ; Perceba o #

LDA #!Value        ; Uma procura e substituição transformará isso em "LDA ##$03"
STA $01            ; Dessa forma, isso causará um erro!
```

## Labels
Como discutido nos capítulos [branches](../ programming / branches.md) e [subrotinas](../ programming / subroutine.md), o processador SNES pode fazer uso de labels para determinar os locais para os quais ele pode saltar. Os rótulos usados pelos opcodes são substituídos por valores reais que denotam os locais para os quais saltar, sejam endereços relativos ou absolutos.

### Sublabels
Sublabels are special type of labels which have a parent label, and are prefixed with a dot ("`.`"), and aren't suffixed with a colon ("`:`"). Sublabels are useful if you tend to use labels which aren't unique (e.g. "return"). Here's an example of a recurring "return" sublabel:

Sublabels são tipos especiais de labels que têm um label pai e são prefixados com um ponto ("` .`"), e não são sufixados com dois pontos (" `:` "). Os sublabels são úteis se você tende a usar marcadores que não são exclusivos (por exemplo, "retorno"). Aqui está um exemplo de sublabel de "retorno" recorrente:

```
JSR Main
JSR Sub
RTS

Main:
LDA $10
BEQ .return
STA $11
.return
RTS

Sub:
LDA $20
BEQ .return
STA $21
.return
RTS
```
Os sublabels não têm regras em termos de estilo de escrita. Você pode usar letras maiúsculas ou mantê-las todas em letras minúsculas. Neste exemplo, está tudo em minúsculas.

### Labels relativos
Relative labels are an alternate solution to sublabels and are often used when the code is already self-documenting enough, for example, when the code needs to skip a single store depending on a branch. It saves you from thinking up a label name, such as "skipstorewhenplayerisbig". Relative labels are written using `+` and `-`. The plus is ahead of the branch instruction, while the minus is behind the branch instruction. They can be repeated as often as needed to denote different levels of depth.

Labels relativos são uma solução alternativa para sublabels e costumam ser usados quando o código já é documentado o suficiente, por exemplo, quando o código precisa saltar um único armazenamento dependendo de um desvio. Isso evita que você pense em um nome de rótulo, como "skipstorewhenplayerisbig". Rótulos relativos são escritos usando `+` e `-`. O mais está à frente da instrução de desvio, enquanto o menos está atrás da instrução de desvio. Eles podem ser repetidos quantas vezes forem necessárias para denotar diferentes níveis de profundidade.

Abaixo um exemplo de labels relativos:
```
LDA $10
BEQ +
STA $11
+
RTS
```
Esse código ignora um único armazenamento de $11 em `A` quando o endereço $10 tem o valor $00.

Aqui está um outro exemplo, demonstrando um desvio para trás, causando um loop infinito:

```
- BRA -
```

Um outro exemplo, demonstrado diferentes níveis de labels relativos:
```
LDA $10
BEQ ++
LDA $11
BEQ +
STZ $12
+
STZ $13
++
STZ $14
RTS
```
- Se o endereço `$7E0010` tem o valor `$00`, o endereço `$7E0014` recebe $00.
- Se o endereço `$7E0011` tem o valor `$00`, os endereços `$7E0013` e `$7E0014` recebem $00.
- Caso contrário, os endereços `$7E0012`, `$7E0013` e `$7E0014` recebem $00.

## A arte da relatividade
It's possible to write programs completely devoid of fixed values and addresses, by making smart use of labels and defines outside of branches and jumps. When you use labels with loading instructions, for example, it'll grab the address of the label and use it as a parameter. This was seen in the [indexing](../collections/indexing.md) chapter. However, you can also use labels as values, rather than addresses. This is especially useful when setting up indirect pointers, which is why it's important to be able to grab certain *parts* of an address rather than the full address. This is also demonstrated in the [moves](../collections/moves.md) chapter, in the "Easy notation" section.

É possível escrever programas completamente desprovidos de valores e endereços fixos, fazendo uso inteligente de labels e defines fora de desvios e saltos. Quando você usa labels com instruções de carregamento, por exemplo, ele pegará o endereço do label e o usará como parâmetro. Isso foi visto no capítulo [Tabelas e indexação](../collections/indexing.md). No entanto, você também pode usar labels com valores, em vez de endereços. Isso é especialmente útil ao configurar ponteiros indiretos, por isso é importante poder pegar certas *partes* de um endereço em vez do endereço completo. Isso também é demonstrado no capítulo [Copiando dados](../collections/moves.md), na seção "Notação rápida".

No exemplo a seguir, um `LDA` usando um endereço de um labal sendo usado como valor:
```
LDA #somelabel
STA $00
```
This is problematic, because the label assembles into a 24-bit value which is the address, and there's no LDA which accepts a 24-bit value. Instead, LDA tries to grab the largest possible supported value, thus grabs the high and low byte of the value instead (because it's 16-bit). But what if you're writing 8-bit code at that moment? The code won't run as expected, and will crash.

Isso é problemático, pois o label é montado em um valor de 24-bit que é o endereço, e não há LDA que aceite um valor 24-bit. Em vez disso, o LDA tenta obter o maior valor suportado possível, portanto, obtém o high byte e o low byte do valor (pois é de 16-bit). Mas e se você estiver escrevendo um código 8-bit naquele momento? O código não será executado conforme o esperado e irá travar.

### Especificadores do comprimento do opcode
In order to read a value at a well-defined, fixed width, you can make use of "opcode length specifiers". These are special notations appended to opcodes:

Para ler um valor em uma largura fixa e bem definida, você pode usar "especificadores de comprimento de opcode". Estas são notações especiais anexadas aos opcodes:

|Syntax|Definição|Descrição|
|-|-|-|
|.b|byte (8-bit)|Força o parâmetro a ser 8-bit|
|.w|word (16-bit)|Força o parâmetro a ser 16-bit|
|.l|long (24-bit)|Força o parâmetro a ser 24-bit|

No exemplo anterior, você pode poderia forçar o assembler a usar apenas o low byte do label, usando `.b`:

```
LDA.b #somelabel
STA $00
```

O mesmo se aplica a defines. Você pode usar defines como valores e, usando o especificador de comprimento do opcode, você pode usar certas partes do define ao invés do valor completo, por exemplo:

```
!Size = $7FFF

LDA #!Size
STA $00
```
Será montado como:
```
LDA #$7FFF
STA $00
```
Isso seria problemático no modo 8-bit, já que é montado em modo 16-bit. Para consertar o problema, você pode usar `.b`:
```
!Size = $7FFF

LDA.b #!Size
STA $00
```
Será montando da seguinte forma:
```
LDA #$FF
STA $00
```

### Deslocamento de bits
Expanding upon the previous example:
```
!Size = $7FFF

LDA.b #!Size
STA $00
```
Se você quiser armazenar o high byte deste define no endereço `$7E0001`, em vez do low byte, você precisa de uma forma de pegar apenas do high byte deste define. Para conseguir isto, você precisará usar deslocamento de bits:

|Sintaxe|Definição|Descrição|
|-|-|-|
|>>|Shift right|Desloca os bits para a direita *n* vezes|
|<<|Shift left|Desloca os bits para a esquerda *n* vezes|
Remember that a byte consists of 8 bits, thus you need to "skip" 8 bits to grab the next 8 bits we need. By bitshifting 8 times to the right, you discard the low byte of the value:

Lembre-se que um byte consiste de 8 bits, logo, você precisa "passar" 8 bits para poder pegar 8 bits de que precisa. Deslocando 8 bits para a direita, você descarta o low byte do valor:

```
!Size = $7FFF

LDA.b #!Size>>8    ; Resta somente $7F
STA $01
```
Os deslocamentos de bits são incrivelmente valiosos ao capturar certas partes de endereços ou valores. Eles também podem ser usados em labels e, portanto, você também pode pegar bytes de banco:

```
LDA.b #somelabel>>16 ; Pega o byte do banco a partir de um label
STA $01
```
O mesmo vale para defines:
```
!Address = $7E8000

LDA.b #!Address>>16 ; Pega $7E do define
STA $02
```

### Construindo endereços
Fazendo uso de deslocamento de bits e especificadores de comprimento do opcode, é possível fornecer endereços para certas subrotines ou como endereços indiretos. Aqui está um exemplo que constrói um endereço indireto:

```
LDA.b #Sometable
STA $00
LDA.b #Sometable>>8
STA $01
LDA.b #Sometable>>16
STA $02
LDA [$00]          ; Isso carrega o valor $01 em A
RTS

Sometable: db $01,$02,$04,$08
```
### Tamanhos de tabela
Existem situações em que é útil saber o tamanho das tabelas, como em [Copiando dados](../collections/moves.md). Para obter o tamanho de uma tabela, você coloca um label no início e no final da tabela, como no exemplo:

```
Sometable: db $01,$02,$04,$08
.end
```

Então, usando o operador subtração, "` -` ", você subtrai o endereço final da tabela do inicial, obtendo o tamanho da mesma. Por exemplo:

```
LDA.b #Sometable_end-Sometable ; #$04
STA $00
RTS

Sometable: db $01,$02,$04,$08
.end
```
Perceba que é importante usar especificadores de comprimento do opcode, já que ainda estamos lidando com labels, portanto, valores 24-bit.