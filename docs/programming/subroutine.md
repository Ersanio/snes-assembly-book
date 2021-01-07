# Saltando para sub-rotinas

E se você precisar usar o mesmo código duas vezes ou mais, mas não quiser escrever, por exemplo, as mesmas 200 linhas de códigos novamente? Você pode criar "sub-rotinas" e usar opcodes de salto para executar essas sub-rotinas. Existem quatro opcodes de salto.

Há uma diferença entre "sub-rotina" e "código" neste tutorial, como verá no decorrer da explanação.

## JSR e JSL
Há dois opcodes que agem de forma similar a uma chamada de função de linguagens de alto nível.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**JSR**|Jump to subroutine|Salta para uma sub-rotina usando um endereço absoluto.|
|**JSL**|Jump to subroutine long|Salta para uma sub-rotina usando um endereço longo.|

Estes opcodes saltam para um trecho de código, após o executarem, continuam o código logo abaixo deles. Eis aqui um exemplo de uso de `JSR`:

```
LDA #$01
STA $01
JSR Label1         ; Salta para executar a “sub-rotina” localizada em Label1 (neste banco)
LDA #$03           ; A instrução RTS em Label1 retornará para esta linha
STA $00
RTS

Label1:
LDA #$02
STA $02
RTS
```
O código acima armazenará $01 em $7E0001, armazenará $03 em $7E0000 E executará os códigos em Label1 no banco atual, assim, também armazenará $02 em $7E0002. 

`JSL` tem a mesma finalidade do `JSR`, a diferença é que pode saltar para qualquer lugar. Neste exemplo, também podemos aplicar a `JSL`, mas o `RTS` deverá ser substituído por ` RTL`.

O opcode `JSR` será montado como `JSR $XXXX` pelo assembler (pois rótulos são alterados para endereços), não se preocupe quanto a isso. Além disso, `JSR` usa um endereço absoluto como parâmetro, sendo assim, ele é limitado ao banco atual. Alterar o registrador data bank não afeta `JSR`. 

O opcode `JSL`, também será montado como `JSL $XXXXXX`, pelos mesmos motivos.

## RTS e RTL
Quando você chama uma sub-rotina, você precisa de um meio para retornar ao código original. Para isso, há dois opcodes de retorno.

|Opcode| Nome completo               |Explicação|
|-|-|-|
|**RTS**|Return from subroutine|Retorna de um JSR.|
|**RTL**|Return from subroutine long|Retorna de um JSL.|

Códigos chamados por `JSR` e `JSL`, devem terminar com `RTS` e `RTL`, respectivamente.

## JMP e JML
Se você precisa controlar o fluxo do código em vez de fazer uma chamada de função, você usará saltos comuns.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**JMP**|Jump|Salta o código usando um endereço absoluto.|
|**JML**|Jump long|Salta o código usando um endereço longo.|

`JMP` e `JML` saltam para um outro local e executam o código, ignorando tudo que vier após eles. O `RTS` em `Label1` NÃO salta de volta para `LDA #$03`. Em vez disso, ele apenas termina a sub-rotina atual em que estava.

```
JML Label1

LDA #$03           ; Ignorado
STA $00            ; Ignorado
RTS                ; Ignorado

Label1:
STZ $00
RTS
```

`JMP` é limitado ao banco atual, assim como `JSR`, `JML` pode pular para qualquer trecho sem restrições, como `JSL`. `JMP` e `JML` não tem uma instrução para retorno, mas você ainda pode usar um `RTS` ou `RTL` para  retornar de um `JSR`/`JMP` dependendo da situação. Veja o exemplo:

```
LDA #$01
STA $00
JSR Label1         ; Salta para a sub-rotina "Label1".
RTS                ; Label2 retorna para cá. O código se encerra aqui.

Label1:
JMP Label2         ; Salta para Label2.

Label2:
RTS                ; Não retorna para "Label1". Retornará para o RTS acima.
```

É uma boa prática colocar uma linha em branco após um `JMP` ou `JML`, assim o leitor sabe que o fluxo do programa muda a partir daquele ponto em diante.

## Notas adicionais

O registrador data bank não é atualizado quando você usa `JSL` ou `JML`. Você deverá fazer isso manualmente.

Lembre-se que o `JSL`/`JML` podem saltar sem restrições para qualquer lugar? Isso quer dizer que eles podem saltar até mesmo para a RAM, isso quer dizer que você pode executar código na RAM. Você pode escrever valores na RAM que pode ser interpretados como código e, assim, executados.
