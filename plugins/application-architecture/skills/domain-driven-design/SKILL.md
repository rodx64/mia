---
name: domain-driven-design
description: Modelagem da camada de domínio com Domain-Driven Design tático — entities vs. value objects, aggregates e invariantes, domain services, domain events, repositories, factories, specifications e linguagem ubíqua. Use ao modelar ou revisar o domínio, ou quando mencionarem DDD, aggregate root ou bounded context. Para isolamento domínio/infraestrutura, ver `hexagonal-architecture` ou `clean-architecture`.
version: 0.2.0
---

# Domain-Driven Design (tático + estratégico leve)

Modelar a camada de domínio para que o código fale a língua do negócio e as regras de negócio fiquem centralizadas, testáveis e protegidas de estado inconsistente — em vez de um modelo anêmico (classes que são só sacos de dados) com regras espalhadas em services genéricos.

Esta skill assume que já existe (ou vai existir) uma fronteira entre domínio e infraestrutura — se essa fronteira em si é o que está em dúvida (onde fica o domínio, como isolar de banco/framework), use primeiro `hexagonal-architecture` ou `clean-architecture`. Aqui o foco é **o que tem dentro** dessa fronteira.

## Quando usar

- Modelar um conceito de negócio novo: é entity, value object, ou nem merece ser uma classe própria?
- Revisão de um modelo de domínio existente suspeito de estar anêmico (classes com só getters/setters, toda regra em `*Service`/`*Manager`).
- Decidir a fronteira de um aggregate: o que entra, o que fica de fora, o que é referenciado só por ID.
- Desenhar a interface de um repository, uma factory para criação complexa, ou uma specification para uma regra combinável.
- Modelar e nomear domain events de um aggregate.
- Dúvida sobre se duas partes do sistema usam o mesmo termo com sentidos diferentes (sinal de bounded contexts colados).

**Não usar para**: decidir a arquitetura geral da aplicação (isso é `hexagonal-architecture`/`clean-architecture`) ou escolher entre event-driven/CQRS (isso é `event-driven-cqrs`) — esta skill assume que essas decisões já foram tomadas ou são ortogonais, e foca no modelo de domínio em si.

## Processo

1. **Entenda o domínio, não só o código.** Levante (perguntando ao usuário ou lendo o código existente):
   - Qual é o conceito de negócio central em questão e como as pessoas do negócio o chamam (linguagem ubíqua) — não o nome técnico que já está no código.
   - Quais operações mudam esse conceito, e quais invariantes (regras que nunca podem ficar inconsistentes) se aplicam a ele.
   - Se já existe modelo de domínio no código: são classes com comportamento, ou estruturas de dados manipuladas de fora por services?
   - Se o projeto é pequeno/novo (ex.: um módulo sendo criado do zero), não presuma bounded contexts múltiplos — comece com um modelo único e só separe quando o termo realmente divergir de significado em outro lugar.

2. **Percorra as referências relevantes** (carregue só o necessário — uma dúvida pontual sobre um aggregate não precisa das 9):

   | Tema | Referência |
   |---|---|
   | Nomear código com os termos do negócio, glossário vivo | `references/linguagem-ubiqua.md` |
   | Entity (identidade) vs. Value Object (estrutural), primitive obsession | `references/entities-value-objects.md` |
   | Aggregate, aggregate root, fronteira de consistência transacional | `references/aggregates-invariantes.md` |
   | Regra de negócio sem dono natural entre entities — domain service vs. modelo anêmico | `references/domain-services.md` |
   | Algo que aconteceu no domínio, nomeado no passado, quem publica pra fora | `references/domain-events.md` |
   | Abstração de coleção de aggregates, interface pertence ao domínio | `references/repositories.md` |
   | Criação complexa de aggregate, quando construtor/`new` não basta | `references/factories.md` |
   | Regra de negócio combinável e nomeada (predicate com nome de domínio) | `references/specifications.md` |
   | Fronteira entre modelos diferentes, mesmo termo com sentidos diferentes | `references/bounded-contexts.md` |

3. **Para revisão de modelo existente**: para cada achado, classifique o que é (modelo anêmico, aggregate grande demais, value object faltando, evento publicado direto pra infra, vazamento de invariante) e uma correção concreta em termos do domínio (não "refatore isso", e sim "mova a validação de X para o construtor de Y", "extraia Z como value object").

4. **Para modelagem nova**: proponha as entities/value objects envolvidos, os limites do(s) aggregate(s) e por que, os domain events relevantes, e a interface do repository — sempre nomeando pelo vocabulário do negócio levantado no passo 1, nunca por termo técnico genérico (`Manager`, `Processor`, `Helper`, `Data`, `Info`).

5. **Entregue um relatório curto**, estruturado assim:

   ```
   ## DDD — <escopo>

   ### Modelo proposto / Achados
   - **[Entity | Value Object | Aggregate | Domain Service | Domain Event | Repository] Nome**
     Papel: <1-2 frases em termos do negócio>
     Invariantes/regras: <o que precisa ser sempre verdade>
     Observação: <achado de revisão ou decisão de design, se aplicável>

   ### Fronteiras de aggregate
   - <Aggregate root> contém: <entities/VOs internos> · referencia por ID: <outros aggregates>

   ### Pontos em aberto
   - <pergunta que falta responder antes de fechar o modelo>
   ```

## Notas

- Kotlin é a stack de referência para exemplos concretos nesta skill (cada `references/*.md` tem uma seção "Em Kotlin"), mas os conceitos são agnósticos — aplicam-se a qualquer linguagem com suporte a objetos e encapsulamento.
- DDD tático é um custo de modelagem — não vale a pena para um CRUD simples sem regra de negócio real. Se o "domínio" é só validação de formato e persistência direta, um modelo mais simples é a escolha certa; não force aggregate/repository/factory onde uma classe com um método resolve.
- Bounded contexts (`references/bounded-contexts.md`) é o único tema estratégico aqui, incluído de forma leve porque a fronteira de um bounded context define até onde um modelo de domínio (e portanto todo o resto desta skill) é válido — não é um mergulho completo em context mapping.
- Se o domínio descrito for muito genérico ("preciso de um sistema de pedidos"), pergunte pelas invariantes e operações reais antes de propor aggregates — sem isso, qualquer fronteira proposta é chute.
