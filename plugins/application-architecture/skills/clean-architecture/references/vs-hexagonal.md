# Clean Architecture vs. Hexagonal: guerra de nomenclatura, não de substância

Na prática, as duas resolvem o mesmo problema com a mesma técnica central. A diferença é vocabulário e ênfase, não um princípio arquitetural distinto. Vale entender isso para não perder tempo debatendo qual "é a certa" quando o time já pratica uma variante informal de ambas.

## Onde convergem

- **Mesma técnica**: inversão de dependência para isolar a lógica de negócio de detalhes de infraestrutura. Em ambas, o núcleo define interfaces e a infraestrutura as implementa.
- **Mesma direção de dependência**: de fora para dentro, sempre. Domínio/entities nunca importam framework, banco ou UI.
- **Mesmo motivo de existir**: testabilidade (testar regra de negócio sem subir infraestrutura) e substituibilidade (trocar banco, fila, gateway de pagamento sem tocar na lógica central).
- **Mapeamento direto de conceitos**: port de entrada ≈ boundary/use case interface; port de saída ≈ gateway/output port; adapter ≈ implementação concreta em interface adapters/frameworks & drivers; domínio hexagonal ≈ entities + use cases.

## Onde diferem em vocabulário/ênfase

| Hexagonal | Clean Architecture |
|---|---|
| Port (entrada/saída) | Boundary / gateway interface |
| Adapter | Interface adapter / frameworks & drivers |
| "Dentro" do hexágono | Entities + Use Cases |
| Não distingue explicitamente domínio de orquestração de caso de uso | Separa explicitamente Entities (regra de negócio) de Use Cases (regra de aplicação) |

- Clean Architecture é mais explícita sobre a existência de uma camada de **use cases** separada das entities — isso ajuda times que têm muita lógica de orquestração/aplicação que não é regra de negócio "pura" (validação de fluxo, coordenação entre agregados, chamadas a múltiplos gateways).
- Hexagonal historicamente enfatiza mais a simetria entre entrada e saída (o sistema visto de fora, de qualquer lado, sempre passa por uma porta) — útil quando o sistema tem múltiplos "lados" simétricos (API HTTP, consumidor de fila, CLI, todos acionando o mesmo núcleo).

## Por que, na prática, são quase intercambiáveis

A maioria dos codebases reais que se dizem "hexagonal" ou "clean" acaba com a mesma estrutura de pastas: algo como `domain/`, `application/` (ou `usecases/`), `adapters/` (ou `infrastructure/`). O nome da pasta importa menos que a disciplina de sempre manter a dependência apontando para dentro.

## Como usar isso em revisão

- Não force o vocabulário de uma escola sobre um código que já usa a outra de forma consistente — revise pela regra de dependência e pela separação de responsabilidade, não pelo nome das camadas.
- Sinal real de problema não é "isso não é hexagonal/clean de verdade", é "o domínio depende de infraestrutura" ou "não há uma interface entre a regra de negócio e o mundo externo" — isso sim é violação, independente do rótulo.
- Ao propor uma estrutura nova para um time, escolha o vocabulário que já é familiar a ele (se já falam em "ports" não introduza "boundaries" à toa, e vice-versa) — a consistência de nomenclatura interna importa mais que aderência estrita a um dos dois textos originais.

## Perguntas para levantar contexto

- O time já usa algum vocabulário (hexagonal, clean, "camadas") de forma estabelecida, ou está decidindo do zero?
- O sistema tem múltiplos pontos de entrada simétricos (HTTP, fila, CLI) que se beneficiariam da ênfase hexagonal em ports de entrada, ou é majoritariamente um único fluxo com bastante orquestração (mais próximo da separação explícita de use cases da Clean Architecture)?
