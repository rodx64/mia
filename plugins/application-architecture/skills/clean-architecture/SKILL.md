---
name: clean-architecture
description: Use esta skill ao revisar, discutir ou decidir a estrutura de um sistema sob a ótica de Clean Architecture (Uncle Bob) — gatilhos como "isso segue clean architecture?", "como separar em entities, use cases e adapters", "onde fica a regra de negócio nessa camada", "revisar se essa dependência está na direção certa", "clean architecture ou hexagonal, qual usar aqui", ou qualquer menção a "clean architecture", "use case", "interface adapters", "frameworks and drivers". Cobre tanto revisão de código/design existente quanto guia de decisão ao estruturar algo novo.
version: 0.1.0
---

# Clean Architecture

Organizar o sistema em camadas concêntricas (entities, use cases, interface adapters, frameworks & drivers) com dependência sempre apontando para dentro. Serve tanto para revisar se um sistema existente respeita essa separação quanto para decidir como estruturar código novo.

## Quando usar

- Revisão de código/design perguntando se as camadas estão corretamente separadas e a dependência aponta na direção certa.
- Decisão de estrutura ao desenhar um módulo/serviço novo, definindo onde entram entities, use cases e adapters.
- Dúvida sobre onde colocar uma regra de negócio, uma orquestração de caso de uso, ou um DTO cruzando uma fronteira.
- Comparação entre Clean Architecture e Hexagonal quando o time está decidindo (ou debatendo) qual vocabulário adotar.

**Não usar para**: debate de nomenclatura sem substância prática — se a dúvida é só "hexagonal ou clean, qual é a certa", vá direto para `references/vs-hexagonal.md`, que trata exatamente disso.

## Processo

1. **Entenda o contexto.** Levante (perguntando ou lendo o código):
   - Existe hoje alguma separação em camadas (mesmo informal)? Quais nomes/pastas já são usados?
   - Onde estão as regras de negócio mais estáveis (entities) versus a orquestração específica de cada operação (use cases)?
   - O que cruza a fronteira entre a camada de aplicação e a camada externa (controllers, banco): DTOs simples ou objetos de framework/ORM?
   - Objetivo é revisar código existente ou estruturar algo novo?

2. **Percorra as referências relevantes** (carregue só o necessário ao escopo):

   | Tema | Referência |
   |---|---|
   | Camadas concêntricas, regra de dependência, boundaries, DTOs | `references/camadas-e-dependencias.md` |
   | Comparação prática com arquitetura hexagonal (convergências e diferenças de vocabulário) | `references/vs-hexagonal.md` |

3. **Para revisão**: classifique cada achado com a camada afetada, o tipo de violação (dependência na direção errada, objeto de infraestrutura vazando pela fronteira, regra de negócio no lugar errado) e correção concreta.

4. **Para decisão de estrutura**: proponha as camadas e interfaces necessárias, identifique quais use cases existem e o que cada um orquestra, e defina o formato do DTO que cruza cada fronteira relevante.

5. **Entregue um relatório curto**, estruturado assim:

   ```
   ## Revisão/Decisão — Clean Architecture — <escopo>

   ### Achados / Proposta
   - **[Dependência invertida | Objeto de infra vazando | Regra no lugar errado] Título curto**
     Onde: <camada/arquivo>
     Descrição: <1-2 frases>
     Recomendação: <ação concreta>

   ### Pontos sem problema / já corretos
   - <componente> — <por quê>
   ```

## Notas

- O critério real de sucesso é testabilidade e substituibilidade (trocar framework/banco sem reescrever regra de negócio) — não a fidelidade estrita aos nomes originais das quatro camadas. Um sistema com três camadas bem separadas pode estar mais "clean" que um com quatro camadas nominais mas dependência vazando entre elas.
- Se o time já usa vocabulário de hexagonal (ports/adapters) de forma consistente, não force a nomenclatura de Clean Architecture por cima — use `references/vs-hexagonal.md` para mapear os conceitos em vez de renomear tudo.
- Se o sistema descrito for muito genérico, pergunte pelos pontos do passo 1 antes de propor estrutura ou apontar violação.
