# Processor flags
Como visto no [capítulo anterior](../processor/flags.md) o SNES suporta 9 flags do processador. Há formas de afetar essas flags, que serão explicadas nesse capítulo.

## SEP
|Opcode|Nome completo|Explicação|
|-|-|-|
|**SEP**|Set processor flags|Define as flags de processador especificadas para 1.|

`SEP` define os bits das flags de processador selecionadas para 1.

`SEP` é usada da seguinte forma:

```
;nvmxdizc = 0000 0000
SEP #$80 ;= 1000 0000
;Nvmxdizc = 1000 0000
```
As letras destacadas em maiúscula são das flags do processador que estão ativadas. Este código ativa a flag negative.

## REP
|Opcode|Nome completo|Explicação|
|-|-|-|
|**REP**|Reset processor flags|Define as flags de processador especificadas para 0.|

`REP` define os bits das flags de processador selecionadas para 0.

`REP`é usada da seguinte forma:

```
;NvmxDizc = 1000 1000
REP #$08 ;= 0000 1000
;Nvmxdizc = 1000 0000
```
Inicialmente, os flags decimal mode e negative estavam habilitados, após a instrução `REP #$08`, o flag decimal mode foi desabilitado, porém, o flag negative continuou inalterado.

## XCE e o emulation mode
Como o bit do emulation mode 'escondido' acima do flag de carry, há um opcode que troca o flag de carry pelo bit do emulation mode.

|Opcode|Full name|Explanation|
|-|-|-|
|**XCE**|Exchange carry and emulation|Troca os valores do flag carry com o flag emulation mode.|
Você pode ter acesso ao emulation mode usando `SEC` e então `XCE`, e sair do mesmo usando `CLC` e então `XCE`. Por padrão, o SNES inicializa no emulation mode. Esta é a razão para que os opcodes `CLC` e `XCE` estejam entre os primeiros quando é realizado um disassembly.

## Outros opcodes
Há outros opcodes que afetam imediatamente uma única flag de processador.

|Opcode|Full name|Explanation|
|-|-|-|
|**SEI**|Set interrupt disable flag|Habilita o flag interrupt disable, tornando-o 1, desativando assim o IRQ.|
|**CLI**|Clear interrupt disable flag|Desabilita o flag interrupt disable, tornando-o 0, ativando assim o IRQ.|
|**SED**|Set decimal flag|Habilita o flag de decimal mode, tornando-o 1, mudando o SNES para decimal mode.|
|**CLD**|Clear decimal flag|Desabilita o flag de decimal mode, tornando-o 0, mudando o SNES para hexadecimal mode.|
|**SEC**|Set carry flag|Habilita o flag carry, tornando-o 1.|
|**CLC**|Clear carry flag|Desabilita o flag carry, tornando-o 0.|
|**CLV**|Clear overflow flag|Desabilita o flag overflow, tornando-o 0.|