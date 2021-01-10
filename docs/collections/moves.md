# Copiando dados
O assembly de 65c816 tem dois opcodes dedicados a mover grandes blocos de dados de um local de memória para outro.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**MVN**|Move block negative|Move um bloco de dados byte a byte, começando do início e trabalhando em direção ao fim|
|**MVP**|Move block positive|Move um bloco de dados byte a byte, começando do final e trabalhando em direção ao início|

`MVP` e `MVN` praticamente faz uma grande quantidade de `LDA` e `STA` em massa para alguns endereços na RAM. Você não pode mover dados para a ROM, porque a ROM é somente leitura.

É altamente recomendável ter os registradores `A`, `X` e `Y` em modo 16-bit. Também é altamente recomendável preservar o data bank usando a pilha, pois o opcode `MVN` o altera implicitamente. Aqui está um exemplo de como configurar o movimento do bloco adequadamente:
```
PHB                ; Preserva o data bank
REP #$30           ; 16-bit AXY
                   ; ← Instruções de movimentação estão localizadas aqui
SEP #$30           ; 8-bit AXY
PLB                ; Recupera o data bank
```

## MVN
Ao usar o `MVN`, todos os três registradores principais têm um propósito especial.

|Registrador|Objetivo|
|-|-|
|A|Especifica a quantidade de bytes a transferir, *menos 1*|
|X|Especifica os bytes high e low do endereço de memória da origem|
|Y|Especifica os bytes high e low do endereço de memória de destino|

O registrador `A` é 'menos 1'. Isso significa que se você quiser mover 4 bytes de dados, carregue $0004-1, equivalente a $0003, em `A`.

`MVN` pode ser escrito de duas maneiras: 

```
MVN $xxyy
; or
MVN $yy, $xx
```
Onde `xx` é o banco de origem, e `yy` é o banco de destino.

> Observe que quando usar a segunda forma, os parâmetros ficam invertidos

Ao executar o opcode `MVN`, o SNES repete aquele mesmo opcode para cada byte transferido. Quando um byte é transferido, acontece o seguinte:
|Registrador|Evento|
|-|-|
|A|Decrementa em 1|
|X|Incrementa em 1|
|Y|Incrementa em 1|
|Data bank|É definido como o bank do endereço de destino|

Vendo que `A` decrementa em 1, eventualmente ele atingirá o valor $0000, então retornará para $FFFF. Quando isso ocorre, a movimentação do bloco termina e o SNES continua a executar os opcodes seguintes.

Aqui está um exemplo de movimento de bloco:
```
PHB                ; Preserva o data bank
REP #$30           ; 16-bit AXY
LDA #$0004         ; \
LDX #$8908         ;  |
LDY #$A000         ;  | Move 5 bytes de dados de $1F8908 para $7FA000
MVN $7F, $1F       ; /
SEP #$30           ; 8-bit AXY
PLB                ; Recupera o data bank
```
Este exemplo moverá 5 bytes de dados do endereço $1F8098 para $7FA000.

## MVP
Ao usar MVP, os três registradores principais têm uma finalidade especial.

|Registrador|Objetivo|
|-|-|
|A|Especifica a quantidade de bytes a transferir, *menos 1*|
|X|Especifica os bytes high e low do endereço de memória da origem|
|Y|Especifica os bytes high e low do endereço de memória de destino|

O registrador `A` é 'menos 1'. Isso significa que se você quiser mover 4 bytes de dados, carregue $0004-1, equivalente a $0003, no `A`.

MVP pode ser escrito de duas maneiras: 
```
MVP $xxyy
;or
MVP $yy, $xx
```
Onde `xx` é o banco de origem, e `yy` é o banco de destino.

> Observe que quando usar a segunda forma, os parâmetros ficam invertidos.

Ao executar o opcode `MVP`, o SNES repete aquele mesmo opcode para cada byte transferido. Deste ponto em diante, é aqui é o `MVP` difere do `MVN`. Quando um byte é transferido, acontece o seguinte:
|Registrador|Evento|
|-|-|
|A|Decrementa em 1|
|X|Decrementa em 1|
|Y|Decrementa em 1|
|Data bank|É definido como o bank do endereço de destino|

Considerando que `X` e `Y` decrementam, ao invés de incrementar, isso significa que `MVP` move os blocos de dados do final para o início.

Aqui está um exemplo de movimento de bloco:
```
PHB                ; Preserva o data bank
REP #$30           ; 16-bit AXY
LDA #$0004         ; \
LDX #$8908         ;  |
LDY #$A000         ;  | Move 5 bytes de dados de ($1F8908-$0004) para ($7FA000-$0004)
MVP $7F, $1F       ; /
SEP #$30           ; 8-bit AXY
PLB                ; Recupera o data bank
```
Este exemplo moverá 5 bytes de dados do endereço $1F8904 para $7F9FFC. Embora a transferência aconteça invertida, os dados transferidos não serão invertidos. Ainda copiará como esperado.

## Casos extremos
* Quando você define o registrador `A` para $0000, significa que você moverá 1 byte.
* Quando você define o registrador A para $FFFF, significa que você moverá 65536 bytes.
* Quando os endereços de origem e destino cruzam o limite do bank, os bytes high e low são redefinidos para $0000, enquanto o data bank continua inalterado.

## Notação rápida
Asar suporta rótulos como parâmetros para `LDA`, então você pode escrever movimentos de bloco sem ter que calcular o tamanho da tabela de origem ou localizações de endereço. Os exemplos a seguir permitem tabelas de todos os tamanhos e assumem que o destino seja o endereço de memória $7FA000.

Um exemplo para MVN:
```
PHB
REP #$30
LDA.w #SomeTable_end-SomeTable-$01
LDX.w #SomeTable
LDY #$A000
MVN $7F, SomeTable>>16
SEP #$30
PLB
RTS

SomeTable: db $00,$01,$02,$03,$04
.end
```
Um exemplo para `MVP`:
```
PHB
REP #$30
LDA.w #SomeTable_end-SomeTable-$01
LDX.w #SomeTable+SomeTable_end-SomeTable-$01
LDY.w #$A000+SomeTable_end-SomeTable-$01
MVP $7F, SomeTable>>16
SEP #$30
PLB
RTS

SomeTable: db $00,$01,$02,$03,$04
.end
```
Como você pode perceber, o `MVP` é consideravelmente mais complicado de ajustar.
