# application-architecture

Plugin de Claude Code com skills para **projetar e revisar** a arquitetura de aplicações e o design do código — estilos arquiteturais, padrões de projeto (GoF) e princípios de design.

## O que tem aqui

Skills (auto-invocadas conforme o contexto da conversa, ou forçadas via `/architecture-review`):

- **`hexagonal-architecture`** — ports & adapters, regra de dependência, DDD tático (aggregates, value objects, domain events, repositories).
- **`clean-architecture`** — camadas concêntricas, regra de dependência, comparação com hexagonal.
- **`event-driven-cqrs`** — arquitetura orientada a eventos e separação command/query.
- **`creational-patterns`** — Factory Method, Abstract Factory, Builder, Singleton, Prototype.
- **`structural-patterns`** — Adapter, Decorator, Facade, Composite, Proxy, Bridge, Flyweight.
- **`behavioral-patterns`** — Strategy, Observer, Command, Template Method, State, Chain of Responsibility, Mediator, Visitor, Iterator, Memento, Interpreter.
- **`design-principles`** — Tell Don't Ask, KISS, SOLID, DRY/YAGNI, Law of Demeter.

Cada skill cobre tanto **revisão** (auditar código/design existente contra o estilo/padrão/princípio) quanto **guia de decisão** (ajudar a escolher e aplicar durante o desenvolvimento) — não inclui scaffolding automático de projetos.

- **Comando `/architecture-review [escopo]`**: força a revisão a qualquer momento; decide sozinho quais skills são relevantes para o escopo informado.

## Instalar para desenvolvimento local

A partir de outro projeto (ex. `testing-mia`):

```bash
claude plugin marketplace add /caminho/para/mia --scope project
claude plugin install application-architecture@mia --scope project
```

Ou, sem instalar, carregando direto para uma sessão:

```bash
claude --plugin-dir /caminho/para/mia/plugins/application-architecture
```

## Próximos passos

Mais estilos arquiteturais (ex.: onion, layered/n-tier) e princípios (ex.: composition over inheritance) podem entrar aqui conforme necessidade.
