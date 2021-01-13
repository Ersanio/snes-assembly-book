# Matemática usando hardware

O processador do SNES é capaz de realizar [multiplicação básica por 2ⁿ](../math/shift.md), mas se você precisar multiplicar ou dividir por outros números, você precisa usar certos registradores de hardware do SNES.

## Multiplicação usando hardware sem sinal
O SNES tem um conjunto de registradores de hardware para multiplicação sem sinal:

|Registrador|Acesso|Descrição|
|-|-|-|
|$4202|Escrita|Multiplicando, 8-bit, sem sinal.|
|$4203|Escrita|Multiplicador, 8-bit, sem sinal. Escrever neste registrador também inicia o processo de multiplicação.|
|$4216|Leitura|Produto 16-bit da multiplicação sem sinal, low byte.|
|$4217|Leitura|Produto 16-bit da multiplicação sem sinal, high byte.|

Após escrever em `$4203` para iniciar o processo de multiplicação, você deverá esperar 8 [ciclos de máquina](../indepth/cycles.md), o que é feito tradicionalmente adicionando 4 instruções `NOP` ao código. Se você não aguardar 8 ciclos de máquina, os resultados serão imprevisíveis.

Eis um exemplo `42 * 129 = 5418` (em hexadecimal: `$2A * $81 = $152A`):

```
LDA #$2A           ; 42
STA $4202
LDA #$81           ; 129
STA $4203
NOP                ; Esperar 8 ciclos de máquina
NOP
NOP
NOP
LDA $4216          ; A = $2A (low byte do resultado)
LDA $4217          ; A = $15 (high byte do resultado)
```

## Multiplicação usando hardware com sinal
Há um conjunto de registradores de hardware que podem ser usados para multiplicação com sinal rápida:

|Registrador|Acesso|Descrição|
|-|-|-|
|$211B|Escrita, duas vezes|Multiplicando, 16-bit, com sinal. Primeira escrita: Low byte do multiplicando. Segunda escrita: High byte do multiplicando.|
|$211C|Escrita|Multiplicador, 8-bit.|
|$2134|Leitura|Produto 24-bit da multiplicação com sinal, low byte.|
|$2134|Leitura|Produto 24-bit da multiplicação com sina, middle byte.|
|$2134|Leitura|Produto 24-bit da multiplicação com sina, high byte.|

Há um problema em usar estes registradores de hardware, pois eles dobram como certos registradores do Mode 7:

- Você só pode utilizá-los para multiplicação **com sinal**
  - O resultado é um 24-but com sinal, o que significa que os resultados estão entre `-8,388,608` a `8,388,607`.
- Os resultados são instantâneos. Você não precisa usar `NOP` para esperar os resultados.
- Você não pode usar quando gŕaficos em Mode 7 estão sendo renderizados na tela.
  - Quando o Mode 7 está habilitado, você só pode usar dentro de um NMI (v-blank).
  - Isso significa também que você pode usar sem restrições, fora do Mode 7.

Note that register `$211B` is "write twice". This means that you have to write an 8-bit value twice to this same register which in total makes up a 16-bit value. First, you write the low byte, then the high byte of the 16-bit value.

Here's an example of `-30000 * 9 = -270000` (in hexadecimal: `$8AD0 * $09 = $FBE150`):

Aqui está um exemplo `-30000 * 9 = -270000` (em hexadecimal: `$8AD0 * $09 = $FBE150`):

```
LDA #$D0           ; Low byte de $8AD0
STA $211B
LDA #$8A           ; High byte de $8AD0
STA $211B          ; Isso configura o multiplicando

LDA #$09           ; $09
STA $211C          ; Isso configura o multiplicador

LDA $2134          ; A = $50 (result low byte)
LDA $2135          ; A = $E1 (result middle byte)
LDA $2136          ; A = $FB (result high byte)
                   ; (= $FBE150)
```
## Divisão usando hardware sem sinal
O SNES tem um conjunto de registradores de hardware para divisão sem sinal. Abaixo você encontra uma lista deles:

|Registrador|Acesso|Descrição|
|-|-|-|
|$4204|Escrita|Dividendo, 16-bit, sem sinal, low byte.|
|$4205|Escrita|Dividendo, 16-bit, sem sinal, high byte.|
|$4206|Escrita|Divisor, 8-bit, sem sinal. Escrever neste registrador também inicia o processo de divisão.|
|$4214|Leitura|Quociente da divisão16-bit, low byte|
|$4215|Leitura|Quociente da divisão16-bit, high byte|
|$4216|Leitura|Resto da divisão, sem sinal, low byte|
|$4217|Leitura|Resto da divisão, sem sinal, high byte|

Quociente significa quantas vezes o dividendo pode "caber" no divisor. Por exemplo: `6/3 = 2`. Portanto, o quociente é 2. Outra maneira de ler isso é: Você pode extrair 3 **duas** vezes de 6 e terminar com exatamente 0 como resto.

Módulo é uma operação que determina o reso do dividendo que não poderia "caber" no divisor. Por exemplo: `8/3 = 2`. Você pode subtrair 3 duas vezes de 8, mas ao fim, você tem um 2 como resto. Assim, o resto desta expressão é `2`. Como há registros de hardware que suportam restos, o SNES também oferece suporte à operação de módulo.

Após escrever em `$4206` para iniciar o processo de divisão, você deverá esperar 16 [ciclos de máquina](../indepth/cycles.md), o que é feito tradicionalmente adicionando oito instruções `NOP` ao código. Se você não aguardar 16 ciclos de máquina, os resultados serão imprevisíveis.

Aqui está um exemplo `256 / 2 = 128` (em hexadecimal: `$0100 / $02 = $0080`):

```
LDA #$00
STA $4204
LDA #$01           ; Escreve $0100 como dividendo
STA $4205
LDA #$02           ; Escreve $02 como divisor
STA $4206
NOP                ; Aguarda 16 ciclos de máquina
NOP
NOP
NOP
NOP
NOP
NOP
NOP
LDA $4214          ; A = $80 (resultado low byte)
LDA $4215          ; A = $00 (resultado high byte)
LDA $4216          ; A = $00, pois não há resto
LDA $4217          ; A = $00, pois não há resto
```

Um exemplo demonstrando uma operação de módulo: `257 / 2 = 128, resto 1` (em hexadecimal: `$0101 / $02 = $0080, resto $0001`)
```
LDA #$01
STA $4204
LDA #$01           ; Escreve $0101 como dividendo
STA $4205
LDA #$02           ; Escreve $02 como divisor
STA $4206
NOP                ; Aguarda 16 ciclos de máquina
NOP
NOP
NOP
NOP
NOP
NOP
NOP
LDA $4214          ; A = $80 (resultado low byte)
LDA $4215          ; A = $00 (resultado high byte)
LDA $4216          ; A = $01, pois há resto (low byte do resto)
LDA $4217          ; A = $00 (high byte do resto)
```

Não há divisão usando hardware com sinal.