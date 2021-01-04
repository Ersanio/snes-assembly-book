# Contribuindo
Este capítulo é destinado aos que contribuem com este tutorial. Ao escrever,  há alguns padrões que devem ser seguidos para maximizar a sua consistência ao longo do tutorial.

## Estilo
Essas diretrizes se referem ao estilo do documento.

### Tabelas
- Sentenças e frases dentro das células da tabela geralmente não devem terminar com um ponto final.
- As tabelas que apresentam os opcodes devem ter os opcodes em ** negrito ** e ter pelo menos as três colunas, conforme mostrado no exemplo a seguir:

| Opcode | Nome completo | Explicação |
| - | - | - |
| **LDA** | Carrega no acumulador | Carrega um valor em A |

## Terminologia
| Regra | Exemplo |
| - | - |
| Ao referir-se ao processador `Ricoh 5A22`, use` o SNES` em vez disso | `O SNES` é capaz de entrar no modo de 8 ou 16 bits |
| Ao referir-se a uma área específica na memória SNES, sempre prefixe `endereço` a ela, de preferência com a área de memória (ou seja,`RAM`) | \(O\) `endereço da RAM` $7E0000 contém [...] |
| Ao referir-se aos registradores A, X e Y, basta usar `A`,`X` ou `Y` | Agora `A` está no modo de 8 bits. `X` é usado para indexar endereços |
| Ao referir-se a valores, sempre acrescente um `valor` | A contém o `valor` $00 |

## Códigos de exemplo
- O código deve usar indentação quando houver labels, sub-labels ou labels de mais/menos na mesma linha que uma instrução. A quantidade de indentação é igual ao comprimento do tipo de rótulo mais longo mencionado anteriormente no bloco de código, incluindo dois pontos ("`:`"), mais dois espaços adicionais.
- O código deve usar espaços em branco para indentação, sem tabulação.
- Os opcodes são escritos inteiramente em maiúsculas (ex: `LDA`).
- As labels são escritas em PascalCase (ex: `Label1:`).
- As sub-labels são escritos inteiramente em minúsculas, sublinhados e o nome deve se adequar semanticamente ao rótulo pai, sem redundância (ex: `.return`).
- Os defines são escritos em PascalCase (ex: `!SomeDefine`).
- Dados diretos (`db`, `dw`, `dl`, `dd`) e especificadores de comprimento de opcode (`.b`,`.w`, `.l`) são escritos inteiramente em minúsculas.
- Os indicadores de comentários (`;`) devem começar na coluna 20 e preenchidos à esquerda por espaços em branco, não por tabulações.
    - Se não houver espaço para um comentário na coluna 20, ele começará na mesma linha.
- Os indicadores de comentários devem ser seguidos por um espaço em branco, antes do próprio comentário.
- Haverá uma nova linha extra após os opcodes `RTS`, `RTL`, `RTI`, `JMP`,  `JML`, `BRA`, `BRL`.
- Estas são orientações que devem ser seguidas o mais estritamente possível, mas pode haver casos excepcionais.

Exemplo:
```
SomeLabel:         ; Este label está em sua própria linha
LDA.b #$42
STA $00            ; Isto é um comentário
RTS

.table:
db $01,$02,$03,$04 ; Outro comentário

.second_table:
db $ 01,$02,$03,$04,$01,$02,$03,$04 ; Um comentário excepcional

TestLabel: LDA #$02 ; Este rótulo está na mesma linha que uma instrução
           STA $01
           BNE +
           NOP
+          RTS      ; O código está indentado de acordo com "TestLabel"
