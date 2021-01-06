# Tabelas e indexação
Indexar é a ação de acessar uma *tabela de dados* a partir de um certo offset, sendo tal offset definido por um indexador. Os registradores X e Y são indispensáveis quando se trata de indexação. Não obstante, são chamados registradores 'indexadores'.

## Tabelas
'Tabelas' são simplesmente uma sequência de valores. Elas são usadas em pelo menos dois cenários:
* Casos condicionais (ex.: uma coleção de valores de aceleração, dependendo da direção do móvel)
* Valores que representam um 'recurso' (ex.: dados gráficos, dados de música, dados da fase)

Tabelas têm aplicações muito práticas em jogos de SNES. Você consegue, por exemplo, armazenar dados de texto em tabelas. Ou dados de fases. Ou ainda dados da paleta. Tabelas podem conter qualquer coisa e ter qualquer tamanho, desde que caibam na ROM.

Existem quatro tipos de valores que você pode usar para escrever tabelas:
| Instrução | Nome completo | Explicação                                                   |
| - | - | - |
|**db**|direct byte| Um valor que indica um byte (8-bit value, e.g. $XX) |
|**dw**|direct word| Um valor que indica um word (16-bit value, e.g. $XXXX) |
|**dl**|direct long| Um valor que indica um long (24-bit value, e.g. $XXXXXX) |
|**dd**|direct double| Um valor que indica um double (32-bit value, e.g. $XXXXXXXX) |

Neste caso, os bytes são um “byte”, não “word” ou “long”, então “db”: direct byte. A tabela neste exemplo serve apenas para demonstrar a indexação. Neste caso, a tabela está localizada em algum lugar dentro da ROM. Tabelas são precedidas por um rótulo para que você possa consultá-las facilmente em seu código.

Aqui está um exemplo de definição de tabela:
```
ValuesTableExample: db $03,$86,$91,$38,$22
```
Como você pode ver, primeiro definimos o tipo de valor como 'byte' escrevendo 'db'. E então, seguimos com os valores de byte separados por uma vírgula (,).

Para acessar uma tabela, sempre usamos um rótulo. Nesse caso usamos o rótulo 'ValuesTableExample'.

## Indexação
Tabelas podem ser acessadas usando um rótulo. Rótulos são traduzidos em endereços de memória ROM. Expandindo o exemplo acima, o código a seguir leria um valor da tabel:

```
LDA ValuesTableExample
STA $00
RTS

ValuesTableExample: db $03,$86,$91,$38,$22
```
Entretanto, o que esse código faz é ler sempre o primeiro valor da tabela, o valor $03, e armazená-lo em  $7E0000 da RAM. Isso acontece porque não usamos nenhum indexador.

```
LDY #$03           ; Y agora está carregado com o número $03
LDA ValuesTableExample,y ;Carrega um número da tabela em A, usando Y como índice
STA $00
RTS

ValuesTableExample: db $03,$86,$91,$38,$22
```
O código carregará um valor da tabela em A. Basicamente, isso faz `LDA ValuesTableExample`, e o valor em Y, que é $03, serem adicionados ao endereço. Em outras palavras, em linguagens de programação de alto nível seria algo como `ValuesTableExample[3]`. Você começa a contar o índice do **zero**. O índice 0 dessa tabela tem o valor $03, o índice 1 tem o valor $86, e assim por diante. O valor do índice 3 é $38 neste caso, então este código de exemplo realmente carrega $38 em A e o armazena em $7E0000 na RAM.

A indexação é bastante útil quando você deseja escrever instruções muito repetitivas o tempo todo. A indexação também pode ser realizada com o registro X. Afinal, X e Y se comportam exatamente da mesma forma.

A indexação é, na verdade, um dos muitos modos de endereçamento. Os modos básicos de endereçamento estão cobertos [aqui](https://github.com/Ersanio/snes-assembly-book/blob/master/docs/the-basics/addressing.md) e os modos mais avançados [aqui](https://github.com/Ersanio/snes-assembly-book/blob/master/docs/indepth/addressing.md).

## Indexando com RAM
Até agora os exemplos acima eram sobre ROM. Também é possível indexar valores na RAM. Você consegue tratar a RAM como uma mesa gigante que permite indexação. Simplesmente substituindo a etiqueta carregada por um endereço. Por exemplo:

```
LDX #$03
LDA $7E1000,x
STA $00
```
Neste caso, o LDA resolveria no endereço `$7E1003`. O código carrega o valor de `$7E1003` em A e o armazena em `$7E1000`.