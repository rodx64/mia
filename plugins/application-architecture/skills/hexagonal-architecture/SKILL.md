---
name: hexagonal-architecture
description: Use esta skill ao revisar, discutir ou decidir a estrutura de um sistema sob a ótica de arquitetura hexagonal (ports & adapters) — gatilhos como "isso está seguindo hexagonal?", "como estruturar essa aplicação com ports e adapters", "revisar se o domínio está vazando pra infraestrutura", "onde deveria ficar essa interface de repositório", "como organizar isolamento do domínio nesse projeto", ou qualquer menção a "hexagonal architecture", "ports and adapters", "driving/driven adapter". Cobre tanto revisão de código/design existente quanto guia de decisão ao estruturar algo novo.
version: 0.1.0
---

# Arquitetura Hexagonal (Ports & Adapters)

Isolar o núcleo de domínio/aplicação de detalhes de infraestrutura (banco, framework web, filas, APIs externas) através de interfaces (ports) definidas pelo núcleo e implementadas por fora (adapters). Serve para dois usos: revisar se um sistema existente respeita esse isolamento, e ajudar a decidir como estruturar algo novo dessa forma.

## Quando usar

- Revisão de código/design perguntando se a separação domínio/infraestrutura está correta ou está vazando.
- Decisão de estrutura ao começar um módulo/serviço novo: onde colocar cada peça, quais interfaces criar.
- Dúvida sobre onde colocar uma regra de negócio, uma integração externa, ou uma dependência específica.
- Design de uma porta nova (ex.: "como desenhar a interface do repositório de X").

**Não usar para**: só nomear pastas como `domain`/`infra` sem revisar a direção real das dependências — isso é cosmético, não é a substância do padrão. Se a dúvida é puramente "clean architecture ou hexagonal, qual usar", ver `clean-architecture/references/vs-hexagonal.md` no plugin.

## Processo

1. **Entenda o contexto.** Levante (perguntando ou lendo o código):
   - Quais integrações externas existem (banco, filas, APIs de terceiros, sistema de arquivos, outros serviços).
   - Quais pontos de entrada acionam o sistema (HTTP, consumidor de fila, CLI, job agendado).
   - Se já existe alguma separação em camadas/pastas, e se ela é reforçada por alguma regra (lint de arquitetura) ou é só convenção.
   - Se o objetivo é revisar algo existente ou estruturar algo novo — o processo seguinte se aplica aos dois, mas a ênfase muda (revisão busca violação; decisão busca desenhar a porta certa desde o início).

2. **Percorra as referências relevantes** (carregue só o necessário ao escopo — não precisa ler os três de uma vez se a dúvida é pontual):

   | Tema | Referência |
   |---|---|
   | O que é port/adapter, entrada vs. saída, vazamento de infraestrutura | `references/ports-adapters.md` |
   | Regra de dependência, inversão de dependência, como aparece em imports | `references/dependency-rule.md` |
   | Aggregates, value objects, domain events, repositories dentro do hexágono (resumo — processo completo na skill `domain-driven-design`) | `references/ddd-tatico.md` |

3. **Para revisão**: classifique cada achado com componente afetado, o que foi violado (vazamento de infraestrutura, direção de dependência errada, lógica de negócio no lugar errado) e uma correção concreta (extrair interface, mover regra, criar adapter).

4. **Para decisão de estrutura**: proponha as portas necessárias nomeadas pelo que o domínio precisa (não pela tecnologia do adapter), identifique que adapter concreto implementa cada uma, e onde fica a composição (injeção de dependência) que liga os dois.

5. **Entregue um relatório curto**, estruturado assim:

   ```
   ## Revisão/Decisão — Arquitetura Hexagonal — <escopo>

   ### Achados / Proposta
   - **[Vazamento de infra | Direção de dependência | Modelagem de aggregate] Título curto**
     Onde: <componente/arquivo>
     Descrição: <1-2 frases>
     Recomendação: <ação concreta — ex.: extrair interface X, mover regra Y para o domínio>

   ### Pontos sem problema / já corretos
   - <componente> — <por quê>
   ```

## Notas

- O objetivo não é pureza acadêmica ao padrão — é garantir que o domínio seja testável isoladamente e que trocar uma peça de infraestrutura não force reescrever regra de negócio. Se o código já atinge isso com um vocabulário diferente, não force renomeação.
- Se o sistema descrito for muito genérico, pergunte pelos pontos do passo 1 antes de propor estrutura ou apontar violação — sem saber quais integrações existem, qualquer porta proposta é chute.
