# Mais sobre modos de endereçamento
No capítulo [Modos de endereçamento](../the-basics/address.md), vimos brevemente os modos de endereçamento mais comumente usados: 'Imediato de 8 e 16-bit', 'direct page', 'absoluto' e 'longo'. Neste capítulo, veremos os modos de endereçamento mais avançados: 'indireto', 'indexado' e 'relativo à pilha'. Esses modos de endereçamento avançados complementa os modos de endereçamento introduzidos anteriormente. Este capítulo também apresenta o conceito de ponteiros.

## Ponteiros
É aqui que os 'ponteiros' entram em jogo. Ponteiros são valores que 'apontam' para um determinado local da memória. Imagine a memória do SNES da seguinte forma::
```
         ; $7E0000: 12 80 00 55 55 55 ..
```
Neste exemplo, o endereço $7E0000 da RAM contém os valores $12, $80 e $00, que estão na ordem little-endian, então inverta os valores e você terá $00, $80, $12. Trate isso como um endereço 'longo' de 24-bit e você terá a valor $008012. Isso significa que o endereço $7E0000 da RAM contém um ponteiro de 24-bit que aponta para o endereço $008012. Isso é o que chamamos de 'indireto', ao acessar o endereço $008012 'indiretamente' pelo endereço $7E0000.

Você pode acessar ponteiros indiretos de duas formas: 16-bit e 24-bit. Eles possuem uma sintaxe especial:
|Sintaxe|Terminologia|Tamanho do ponteiro|
|-|-|-|
|( )|Indireto|16-bit|
|[ ]|Indireto longo|24-bit|

O byte do banco do ponteiro de 16-bit depende do tipo de instrução. Quando se trata de um opcode `JSR`, que só pode saltar dentro do mesmo banco, o byte do banco não é determinado e nem usado. No entanto, quando se trata de uma instrução de carregamento, como `LDA`, o byte do banco é determinado pelo *registrador databank*.

Os ponteiros podem apontar para *código* e *dados*. Dependendo do tipo de instrução, os ponteiros são acessados ​​de forma diferente. Por exemplo, um `JSR` que utiliza um ponteiro de 16-bit acessa o local apontado como código, enquanto um` LDA` acessa o local apontado como um banco.


## Indireto
Os modos de endereçamento indireto são basicamente acessar endereços de forma que você acesse o endereço para o qual eles apontam, em vez de acessar diretamente o conteúdo do endereço especificado.

### Direto, indireto
Por mais paradoxal que possa parecer, a nomenclatura realmente faz sentido. 'Direto' significa modo de endereçamento direct page, enquanto 'indireto' significa que estamos acessando um ponteiro no endereço direct page, em vez de um valor. veja o exemplo a seguir:

```
; Configura o ponteiro indireto
REP #$20
LDA #$1FFF         ; $1FFF para $7E0000
STA $00
SEP #$20
                   ; $7E0000: FF 1F .. .. .. .. ..

; Acessa o ponteiro indireto
LDA ($00)          ; Carrega o valor de $1FFF em A
```
LDA acessa um ponteiro de 16-bit no endereço $000000, devido à natureza do direct page sempre acessar o banco $00. Devido aos efeitos de espelhamento no espelhamento do SNES, praticamente falando, ele acessa um ponteiro de 16-bit em $7E0000 da RAM.

Você pode pensar que `LDA ($00)` carrega o `valor $1FFF` em A. No entanto, não funciona dessa forma. Ele carrega o `valor do endereço $1FFF` em A, porque ele está usando um modo de endereçamento indireto.

Conforme estabelecido anteriormente, os parênteses  denotam ponteiros de 16-bit. O banco do endereço indireto, no caso de um LDA, depende do registador data bank. Como resultado, o `LDA ($00)` se comporta como `LDA $1FFF`.

### Indexado direto com X, indireto
Como no caso do modo de endereçamento anterior, a nomenclatura pode parecer contraditória. 'Direto' significa modo de endereçamento de direct page. 'Indexado com X' significa que este endereço de direct page é indexado com X. 'Indireto' significa que os elementos anteriores são tratados como um endereço indireto. Aqui está um exemplo de uso:

```
; Configura o ponteiro indireto
REP #$20
LDA #$1FFF         ; $1FFF para $7E0000
STA $00
LDA #$0FFF         ; $0FFF para $7E00020
STA $02
SEP #$20
                   ; $7E0000: FF 1F FF 0F .. .. ..

; Acessa o ponteiro indireto
LDX #$02           ; Carrega $02 em X
LDA ($00, x)       ; Carrega o valor de $0FFF em A
```
- O valor de 16-bit no endereço $7E0000 + $7E0001 é `$1FFF`.
- O valor de 16-bit no endereço $7E0002 + $7E0003 é `$0FFF`.

Graças ao uso de X como um indexador para o endereço de direct page, `LDA ($00, x)` é tratado como `LDA ($02)`. Então se transforma em `LDA $0FFF` porque $7E0002 aponta para o` endereço $0FFF`.

### Direto, Indireto indexado com Y
É praticamente o mesmo que `Direto, Indireto`, mas o ponteiro é indexado com o registrador Y:
```
; Configurar ponteiro indireto
REP #$20
LDA #$1FF0         ; $1FF0 para $7E0000
STA $00
SEP #$20
                   ; $7E0000: F0 1F .. .. .. .. ..

; Acessa o ponteiro indireto
LDY #$01; Defina Y como $01
LDA ($00), y; Carregue o valor de $1FF1 em A
```
Como resultado, `LDA ($00), y` se transforma em ` LDA $1FF0, y`, que por sua vez se transforma em `LDA $1FF1`, por causa do Y que contém o valor $01.

### Absoluto, Indireto
Exatamente igual ao `Direto, Indireto`, exceto que o endereço especificado agora é de 16-bit em vez de 8-bit. Este modo de endereçamento é usado apenas em instruções de salto. Exemplo:
```
; Configurar ponteiro indireto
REP #$20
LDA #$8000         ; $8000 para RAM $7E0000
STA $00
SEP #$20
                   ; $7E0000: 00 80 .. .. .. .. ..

; Acessa o ponteiro indireto
JMP ($0000)        ; Salta para $8.000.
```
Tem exatamente o mesmo efeito que o exemplo em `Direto, Indireto`. Como resultado, o `JMP ($0000)` se transforma em `JMP $8000` e salta para o `endereço $8000` no banco atual.

### Absoluto indexado com X, indireto
Exatamente o mesmo que `Direct Indexed with X, Indirect`, exceto que o endereço especificado agora é de 16-bit em vez de 8-bit. Este modo de endereçamento é usado apenas em instruções de salto. Exemplo:

```
REP #$20
LDA #$8000         ; $8000 para $7E0000
STA $00
LDA #$9000         ; $9000 para $7E0002
STA $02
SEP #$20
                   ; $7E0000: 00 80 00 90 .. .. ..

; Acessar ponteiro indireto
LDX #$02           ; Carrega $02 em X
JMP ($0000, x)     ; Salta para $9000
```
- O valor de 16-bit no endereço $7E0000 + $7E0001 é `$8000`.
- O valor de 16-bit no endereço $7E0002 + $7E0003 é `$9000`.

Graças ao uso de X como um indexador para o endereço de direct page, `JMP ($0000, x)` é convertido em `JMP ($0002)`, que então se resolve em `JMP $9000` porque $7E0002 aponta para o `endereço $9000`.

### Direto, Indireto Longo
Exatamente igual a `Direto, Indireto`, exceto que o ponteiro localizado em um endereço agora é de 24-bit em vez de 16-bit, o que significa que o 'byte do banco' de um ponteiro também é especificado. Exemplo:
```
; Configura o ponteiro
REP #$20
LDA #$1FFF         ; $1FFF para $7E0000
STA $00
SEP #$20
LDX #$7F           ; $7F para $7E0002
STX $02            ; Agora $7E0000 contém o ponteiro $7F1FFF de 24-bit
                   ; $7E0000: FF 1F 7F .. .. .. ..
                   
; Acessa o ponteiro indireto
LDA [$00]          ; Carrega o valor de $7F1FFF em A
```
`LDA [$00]` é convertido em `LDA $7F1FFF`.

### Direto, Indireto Indexado Longo com Y
Exatamente igual a `Direto, indireto indexado com Y`, exceto que o ponteiro localizado em um endereço agora é de 24-bit em vez de 16-bit, o que significa que o byte do banco de um ponteiro também é especificado. Exemplo:
```
; Configura o ponteiro
REP #$20
LDA #$1FF0         ; $1FF0 para $7E0000
STA $00
SEP #$20
LDX #$7F           ; $7F para $7E0002(RAM)
STX $02            ; Agora $7E0000 contém o ponteiro $7F1FF0 (24-bit)
                   ; $7E0000: F0 1F 7F .. .. .. ..

; Acessa o ponteiro indireto
LDY #$01           ; Carrega $01 em Y
LDA [$00],y        ; Carrega o valor de $7F1FF1 em A
```

Como resultado, `LDA [$00], y` é convertido em` LDA $7F1FF0, y` (praticamente falando), que então é convertido em `LDA $7F1FF1` por causa de Y que contém o valor $01.

## Indexado
O modo de endereçamento indexado foi brevemente abordado no [capítulo anterior](../collections /indexing.md). Este capítulo discutirá todos os modos de endereçamento indexados possíveis.

### Direto, indexado com X
Este modo de endereçamento indexa um endereço de direct page com X. Exemplo:
```
LDX #$02
LDA $00, x         ; Carrega o valor de $7E0002 em A
```

### Direto, indexado com Y
Este modo de endereçamento indexa um endereço de direct page com Y. Este modo de endereçamento só é possível nos opcodes `LDX` e` STX`. Exemplo:
```
LDY #$02
LDX $00, y         ; Carrega o valor do endereço $7E0002 em X
```

### Absoluto, indexado com X
Este modo de endereçamento indexa um endereço absoluto com X. Exemplo:
```
LDX #$02
LDA $0000, x       ; Carrega o valor do endereço $7E0002 em A
```

### Absoluto, indexado com Y
Este modo de endereçamento indexa um endereço absoluto com Y. Exemplo:
```
LDY #$02
LDA $0000, y       ; Carrega o valor de $7E0002 em A
```

### Absoluto, Long indexado com X
Este modo de endereçamento indexa um endereço longo com X. Exemplo:
```
LDX #$02
LDA $7E0000, x     ; Carrega o valor de $7E0002 em A
```

## Stack Relative
O stack relative é um tipo especial de modo de endereçamento do indexador. Ele usa o registrador de ponteiro de pilha como um índice de 16-bit, em vez de usar o registrador X ou Y.

### Stack Relative
Carrega um valor da RAM, relativo ao ponteiro da pilha. O byte do banco é sempre $00. Exemplo:
```
; Registrador stack pointer = $1FF0
LDA $00,s          ; ($001FF0) Carrega em A o valor no slot livre atual da pilha.
LDA $01,s          ; ($001FF1) Carrega em A o último valor inserido na pilha.
LDA $02,s          ; ($001FF2) Carrega em A o penultimo valor inserido na pilha.
LDA $03,s          ; ($001FF3) ...
```
No modo 16-bit de A, os incrementos de endereço seriam 2, em vez de 1, como no exemplo acima.

### Pilha relativa, indireta indexada com Y
É praticamente igual a `Direto, indireto indexado com Y`, exceto que o valor é carregado de um endereço relativo da pilha. Exemplo:

```
; Registrador stack pointer = $01FD
REP #$20
LDA #$0100
PHA
SEP #$20
LDY #$03
LDA ($01,s),y      ; → LDA ($01FE),y → LDA $0100,y → LDA $0103
```
`$01, s` refere-se ao carregamento de A com o último valor empilhado, que é $0100 no caso deste exemplo. Os parênteses se aplicam a este endereço relativo da pilha, convertendo a instrução para `LDA $0100, y`, que por sua vez, é convertido para  `LDA $0103` por causa do indexador.

Este modo de endereçamento será útil se você quiser tratar certos valores empilhados como um endereço de memória indexado.