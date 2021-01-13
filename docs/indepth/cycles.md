# Machine cycles

O SNES processa instruções, mas cada instrução leva uma quantidade predeterminada de tempo para ser executada. O tempo que uma instrução leva para ser executada é chamado de “ciclo de máquina” (ou "ciclo" resumido).

Cada instrução tem seu próprio ciclo. [Esta página](https://wiki.superfamicom.org/65816-reference) possui uma referência completa de quantos ciclos cada instrução leva para ser executada. Dê uma olhada nas notas de rodapé, pois a quantidade de ciclos usados pode variar dependendo do contexto do código. Por exemplo, um desvio tomado (taken branch) leva 1 ciclo a mais em comparação com um desvio que não foi tomado (not taken branch).

Quanto menos ciclos, menos lentidão o código sofrerá. A lentidão é frequentemente perceptível em jogos com muitos sprites na tela. Para evitar tal lentidão, você precisa escrever um código eficiente. Aqui segue um exemplo de código ineficiente VS. eficiente:

```
; Ineficiente
LDA #$00           ; 2 ciclos
STA $7E0000        ; 5 ciclos
                   ; = 7 ciclos
; Eficiente
LDA #$00           ; 2 ciclos
STA $00            ; 3 ciclos
                   ; = 5 ciclos

; Muito eficiente
STZ $00            ; 3 ciclos
                   ; = 3 ciclos
```
Primeiramente, usamos uma notação completa ao escrever o valor $00 para endereçar $7E0000. Mas então, no exemplo seguinte, encurtamos o endereço economizando 2 ciclos. E finalmente, descobrimos ser possível usar `STZ`, já que armazenamos `zero` em um endereço de qualquer maneira.

Ter uma contagem de ciclos baixa é especialmente importante ao executar o código durante um NMI, devido aos ciclos de máquina limitados naquele momento. Exceder esse limite faz com que linhas de varredura pretas trêmulas apareçam na parte superior da tela.