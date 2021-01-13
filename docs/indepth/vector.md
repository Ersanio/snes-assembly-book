# Vetores de Hardware
Vetores de Hardware são um grupo de ponteiros que definem como o SNES deve lidar com certas interrupções dentro da ROM. Cada vetor tem dois bytes de tamanho e aponta para instruções localizadas no bank `$00`. Os vetores de hardware são agrupados pelos [dois modos do SNES](../processor/flags.md): vetores de "modo emulação" e vetores de "modo nativo".

## Vetores de modo nativo
Estes vetores entram em ação quando o SNES é executado em modo nativo (E=0).
|Endereço na ROM|Vetor|Notas adicionais|
|-|-|-|
|`$00:FFE0`|Não usado||
|`$00:FFE2`|Não usado||
|`$00:FFE4`|vetor COP|Disparado pelo [opcode COP](../indepth/misc.md)|
|`$00:FFE6`|vetor BRK|Disparado pelo [opcode BRK](../indepth/misc.md)|
|`$00:FFE8`|vetor ABORT|Não usado pelo SNES|
|`$00:FFEA`|vetor NMI|Disparado pela Interrupção V-Blank do SNES|
|`$00:FFEC`|Não usado||
|`$00:FFEE`|vetor IRQ|Disparado pelo SNES H/V-Timer ou interrupção externa|

## Emulation mode vectors
Estes vetores entram em ação quando o SNES é executado em modo emulação (E=1).
|Endereço na ROM|Vetor|Notas adicionais|
|-|-|-|
|`$00:FFF0`|Não usado||
|`$00:FFF2`|Não usado||
|`$00:FFF4`|vetor COP||
|`$00:FFF6`|Não usado||
|`$00:FFF8`|vetor ABORT|Não usado pelo SNES|
|`$00:FFFA`|vetor NMI||
|`$00:FFFC`|vetor RESET|Disparado soft/hard reset do SNES|
|`$00:FFFE`|vetor IRQ/BRK|É possível diferenciar ambos usando a [flag BRK](../processor/flags.md)|


## Exemplo de utilização
Aqui segue um exemplo de como utilizar os vetores mais comumente usados, que podem ser usados em ROMs homebrew de SNES. Observe que os rótulos devem estar localizados no bank `$00` (que está localizado no mesmo banco desta utilização).

```
ORG $00FFE0

; Modo nativo
dw $FFFF           ; Não usado
dw $FFFF           ; Não usado
dw $FFFF           ; COP
dw $FFFF           ; BRK
dw $FFFF           ; ABORT
dw NativeNMI       ; NMI
dw $FFFF           ; Não usado
dw NativeIRQ       ; IRQ

; Modo emulação
dw $FFFF           ; Não usado
dw $FFFF           ; Não usado
dw $FFFF           ; COP
dw $FFFF           ; Não usado
dw $FFFF           ; ABORT
dw $FFFF           ; NMI
dw Reset           ; RESET
dw $FFFF           ; IRQ/BRK
```

Como os programadores de homebrews geralmente desejam executar seu programa em modo nativo, todos os vetores do modo emulação são ignorados, exceto para reset. Além disso, nada é feito com os vetores `BRK` e `COP`, pois estes são opcodes que geralmente não são utilizados.
