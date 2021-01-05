# A memória do SNES

Escrever em assembly envolve escrever um monte de instruções em que você carrega um "valor" e o armazena em um "endereço" para obter o efeito desejado, como alterar o powerup do jogador por exemplo. Ao gravar dados em assembly, você trabalhará com a memória do SNES na maior parte do tempo.

A memória do SNES é basicamente uma região de bytes, e cada byte está localizado em um "endereço". Pense nisso como um tabuleiro de xadrez:

![](../.gitbook/assets/chessboard.png)

Você pode ver que para se referir a uma determinada casa, a imagem faz uso de nomes de colunas e casas. Na imagem acima o "endereço" da rainha \(o "valor" \) seria o endereço D8, por exemplo. Além disso, uma única casa não pode conter duas unidades. Este mesmo conceito se aplica à memória do SNES.

A memória do SNES é mapeada do endereço $000000 a $FFFFFF, embora apenas os endereços de $00000 a $7FFFFF seja usado na maioria dos casos. O formato de um endereço é o seguinte: $BBHHDD.

* BB é o "byte do banco"
* HH é o "High byte"
* DD é o "Low byte"

Os endereços podem ser escritos de 3 maneiras: $BBHHDD, $HHDD e $DD, como $7E0003, $0003 e $03.

* $DD são os “endereços de páginação direta”
* $HHDD são os “endereços absolutos”
* $BBHHDD são os “endereços longos”

Conforme estabelecido anteriormente, um endereço pode conter apenas um byte. Se você acessar um determinado endereço no modo 16 bits, significa que você realmente está acessando o "endereço" e "endereço+1", porque um número de 16 bits consiste em dois bytes.

A figura a seguir temos uma visão geral da memória básica do SNES \(também conhecido como mapa de memória\):

![The &#x201C;LoROM&#x201D; Memory Map](../.gitbook/assets/memory.png)

Este mapa de memória está no formato "LoROM". Se você é um hacker de SMW, não precisa se preocupar com o que isso significa; apenas considere este mapa de memória por garantia.

## ROM

ROM significa "Read-Only Memory" e é exatamente isso: uma memória que só pode ser lida. Isso significa que você não pode alterar a ROM armazenando valores nela com o ASM. Você pode dizer que é o próprio jogo ou programa, que contém todo o código ASM e tabelas de dados, bem como recursos como gráficos, música e assim por diante. Alternativamente: é o arquivo .smc/.sfc/.fig/etc. que você carrega em emuladores.

## RAM

RAM significa "Random-Access Memory". Esta é a memória dinâmica que permite que qualquer coisa seja escrita nela a qualquer momento. Você poderia dizer que este é o lugar onde você tem as variáveis ​​que são importantes e têm significado. A RAM pode ser gravada para obter um certo efeito. Por exemplo, se você escrever $04 nas vidas extras do jogador, e ele terá exatamente 4 vidas extras.

O RAM do SNES tem 128kB de tamanho e está localizado nos endereços de $7E0000 a $7FFFFF. O RAM é totalmente genérica. Não existe uma regra como “o endereço $7E0120 é usado para vidas em todos os jogos SNES.” Você mesmo define a finalidade da RAM, escrevendo seu próprio código ASM.

O mapa de memória mostra que os bancos $00-3F contêm um "espelho" da RAM. Os endereços de RAM espelhados são endereços que contêm o mesmo valor em todos os bancos. Isso significa que o endereço de RAM $001234 contém exatamente o mesmo valor de $0F1234 em todos os momentos. Ter a RAM espelhada significa que o código em execução na ROM nesses bancos pode acessar a RAM de $7E0000 a $7E1FFF mais "facilidade". Por outro lado, o código executado nos bancos $40-6F tem mais problemas para acessar a RAM porque a RAM não é espelhada nesse local.

Por razões de simplicidade, você **sempre** pode assumir que o banco $00 é igual ao banco $7E.

## SRAM

SRAM significa "Static Random-Access Memory". Também tem 128kB de tamanho e está localizado em blocos de 32kB entre $700000 a $707FFF, $710000 a $717FFF, $720000 a $727FFF e $730000 a $737FFF, embora o tamanho final da SRAM dependa das próprias especificações de ROM, graças a algo chamado "cabeçalho interno da ROM. A SRAM não é espelhada em outros bancos.

SRAM se comporta exatamente como RAM; você pode armazenar e carregar qualquer coisa nela, mas os valores não são apagados quando o SNES é reiniciado. A memória SRAM é mantida viva por uma bateria que está presente em um cartucho de SNES. Quando a bateria acaba ou é removida, a SRAM não funcionará corretamente e possivelmente perderá os dados após cada reinicialização. Nos emuladores, a SRAM é armazenada nos arquivos ".srm".

SRAM é geralmente usado para salvar arquivos, embora também possa ser usada como uma memória RAM extra.
