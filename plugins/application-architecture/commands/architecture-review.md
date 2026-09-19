---
description: Executa uma revisão de arquitetura de aplicação e/ou design de código no projeto atual ou em um escopo específico informado como argumento, acionando as skills relevantes (estilo arquitetural, design patterns, princípios de design).
argument-hint: [escopo opcional, ex. "módulo de pagamentos" ou "src/domain"]
---

# /architecture-review

Escopo informado: `$ARGUMENTS` (se vazio, revise o projeto/repositório atual como um todo).

1. Se o escopo não foi informado ou está vago demais, explore o projeto atual (estrutura de pastas, como as camadas se comunicam, se há um "domínio" isolado de infraestrutura) antes de decidir quais skills acionar.
2. Decida quais skills deste plugin são relevantes para o escopo e acione-as — não é preciso cobrir todas sempre:
   - Estilo arquitetural em uso ou pretendido → `hexagonal-architecture`, `clean-architecture` e/ou `event-driven-cqrs`.
   - Revisão de código orientado a objetos (classes, dependências, condicionais complexas) → `creational-patterns`, `structural-patterns`, `behavioral-patterns` conforme o problema observado.
   - Qualidade geral do design (acoplamento, duplicação, complexidade, getters/setters expostos) → `design-principles`.
3. Para cada skill acionada, siga o processo definido no respectivo `SKILL.md` e produza os achados/recomendações no formato que a skill define.
4. Consolide um único relatório ao final, agrupado por skill, evitando repetir o mesmo achado sob ângulos diferentes.
5. Se o escopo for muito genérico ou faltar contexto de negócio relevante (ex.: por que uma regra de negócio existe, se há múltiplos bounded contexts), pergunte antes de gerar o relatório final.
