# Changelog

Releases da marketplace `mia`. A tag do repositório versiona o conjunto — os plugins têm versão própria em `plugins/<nome>/.claude-plugin/plugin.json` e evoluem de forma independente dentro dela.

## v0.2.0

Plugins nesta release: `security-architecture` 0.1.0 · `application-architecture` 0.2.0

### Adicionado

- Plugin `application-architecture` com 8 skills: `hexagonal-architecture`, `clean-architecture`, `event-driven-cqrs`, `domain-driven-design`, `creational-patterns`, `structural-patterns`, `behavioral-patterns`, `design-principles`.
- Comando `/architecture-review [escopo]`, que decide quais skills acionar conforme o escopo informado.
- Skill `domain-driven-design` com 9 referências (8 táticas + bounded contexts), com exemplos em Kotlin.

### Alterado

- Descrições das skills enxugadas para reduzir o custo de contexto no início da sessão — a listagem de skills é limitada por `skillListingBudgetFraction` (1% da janela por padrão) e descrições longas são truncadas quando o conjunto estoura.
- `hexagonal-architecture/references/ddd-tatico.md` reduzido a um resumo que aponta para a skill `domain-driven-design`, evitando conteúdo duplicado.

## v0.1.0

### Adicionado

- Plugin `security-architecture` com a skill `owasp-top10-review` (OWASP Top 10 2021, uma referência por categoria) e o comando `/owasp-review`.
