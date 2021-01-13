# Operações bitwise

As operações bitwise são fundamentais quando se trata de programação assembly. O 65c816 suporta várias instruções bitwise, que serão abordadas neste capítulo. Os Operadores bitwise trabalham principalmente com bits dos bytes, portanto, neste capítulo, usaremos os termos `x` e `y` quando nos referirmos ao bits.

Ás vezes nos referiremos ao valor `1` em como `verdadeiro` e ao valor `0` como `falso`.

## AND (E)

AND é um operador que afeta o acumulador, aplicando um "E" lógico em todos os bits do registrador.

| Opcode  | Nome completo | Explicação                                                   |
| ------- | ------------- | ------------------------------------------------------------ |
| **AND** | Logical AND   | E lógico, também conhecido como "` & `" em algumas linguagens de programação |

O resultado de `x AND y` será `verdadeiro` se `x` for `verdadeiro` e ` y` também for `verdadeiro`. Caso contrário, o resultado será `falso`.

Veja este exemplo de um AND lógico.

```
LDA #$F0           ; A = 1111 0000
AND #$98           ; AND 1001 1000
                   ; A = 1001 0000 = $90
```

Para entender este código, você terá que ler os comentários de cima para baixo. Como você pode ver, a primeira linha contém o valor binário do acumulador (`1111 0000`), enquanto a segunda linha contém o valor binário do parâmetro do operador AND (`1001 1000`). Quando dois `1` são avaliados, o resultado também será 1. O quer dizer que tanto x quanto y forem`verdadeiros`. Se x = 1 e y = 1, o resultado também será 1.

Esta regra pode ser escrita em uma "tabela verdade".

| Bit comparado | Operação AND | Resultado |
| ------------- | ------------ | --------- |
| 1             | 1            | 1         |
| 0             | 1            | 0         |
| 1             | 0            | 0         |
| 0             | 0            | 0         |

Em suma, sempre que houver um 0 em A ou no bit do valor do AND, o bit resultante também será 0.

## ORA

ORA é uma instrução que afeta o acumulador, aplicando um "OU" lógico em todos os bits.

| Opcode  | Nome completo | Explicação                                                   |
| ------- | ------------- | ------------------------------------------------------------ |
| **ORA** | Logical OR    | OU lógico, também conhecido como "`|`" em algumas linguagens de programação |

O resultado de `x OR y` será `verdadeiro` se  ` x` for ` verdadeiro` ou `y` for ` verdadeiro`. Caso contrário, o resultado será `falso`.

A seguir temos um exemplo de um ORA:

```
LDA #$F0           ; A = 1111 0000
ORA #$87           ; ORA 1000 0111
                   ; A = 1111 0111 = $ F7
```

Se um dos bits for 1, o bit resultante será 1. Após a avaliação de ORA, o resultado será armazenado em A. Aqui está a tabela verdade para ORA:

| Bit comparado | Operação ORA | Resultado |
| ------------- | ------------ | --------- |
| 1             | 1            | 1         |
| 0             | 1            | 1         |
| 1             | 0            | 1         |
| 0             | 0            | 0         |

Então, basicamente, sempre que A ou o bit do valor de ORA for 1, o bit resultante também será 1.

## EOR

EOR é um operador que afeta o acumulador, aplicando um OR exclusivo lógico (XOR) em todos os bits.

| Opcode  | Nome completo | Explicação                                                   |
| ------- | ------------- | ------------------------------------------------------------ |
| **EOR** | Logical XOR   | OU-EXCLUSIVO lógico, também conhecido como "` ^ `" em algumas linguagens de programação |

O resultado de `x XOR y` será `verdadeiro` se `x` for  `verdadeiro` e `y` for `falso`, ou `x`  for `falso` e `y` for `verdadeiro`. Caso contrário, o resultado será `falso`.

Segue um exemplo de um XOR:

```
LDA #$99           ; A = 1001 1001
EOR #$F0           ; EOR 1111 0000
                   ; A = 0110 1001 = $ 69
```

Basicamente, se os bits do acumulador e os bits do valor fornecido são 1, o resultado será 0. E se são 0, o resultado também será 0. Se eles não são iguais, o resultado será 1. Aqui está a tabela verdade para EOR:

| Bit comparado | Operação XOR | Resultado |
| ------------- | ------------ | --------- |
| 1             | 1            | 0         |
| 0             | 1            | 1         |
| 1             | 0            | 1         |
| 0             | 0            | 0         |

## BIT

BIT é uma instrução que praticamente faz a mesma coisa que o AND lógico, mas o resultado NÃO é armazenado no acumulador. Em vez disso, afeta apenas as flags do processador.

| Opcode  | Nome completo | Explicação                                |
| ------- | ------------- | ----------------------------------------- |
| **BIT** | Bit test      | Testa os bits do acumulador ou da memória |

Aqui está um exemplo de BIT em uso:

```
LDA #$04           ; A = 0000 0100 = $04
BIT #$00           ; AND 0000 0000. Nenhum dos bits estão habilitatos
                   ; então, normalmente, A deveria ser $00, mas ainda é $04
                   ; No entanto, o zero flag foi habilitado porque
                   ; o resultado *deveria* ser $00.
```

O BIT tem um recurso que o distingue de um AND, envolvendo flags do processador diferentes do zero flag. Quando você usa o BIT em um endereço, ao invés do acumulador, ele também pode afetar o 'negative flag' e o 'overflow flag'. Se o bit 7 do valor do endereço for 1, o negative flag será habilitado. Se o bit 6 do valor do endereço for 1, o overflow flag será habilitado. Aqui está um exemplo de uso de BIT em um endereço:

```
BIT $04            ; Testa os bits do valor do endereço $7E0004
```

Se o valor de $7E0004 fosse $80 (1000 0000), o negative flag seria habilitado e o overflow flag seria limpo. É útil para checar de forma mais rápida se o valor de um endereço é negativo.

Se o valor de $7E0004 fosse $40 (0100 0000), o negative flag  negativo seria limpo e o overflow flag seria habilitado. Útil para checar se o bit 6 está habilitado.

Se o valor de $7E0004 fosse $C0 (1100 0000), tanto o negative flag quanto o overflow flag seriam habilitados.

Coincidentemente, os bits para negativo (bit 7)  e overflow (bit 6)  correspondem aos mesmos bits no status register de processador: `nvmxdizc`.

| bits       | Resultado BIT                                    |
| ---------- | ------------------------------------------------ |
| Bit 7      | Negative flag habilitado                         |
| Bit 6      | Overflow flag habilitado                         |
| Bits 7 e 6 | Ambos negative flag e overflow flag habilitados. |

Quando você está realizando uma operação BIT em um endereço de RAM, as flags N e V serão habilitados ou limpos, independentemente do valor no acumulador. O zero flag depende do valor do acumulador e do valor do endereço da RAM. Portanto, o BIT com um endereço da RAM executa o AND e uma inevitável checagem dos bits 7 e 6 do endereço de RAM.