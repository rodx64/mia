---
description: Executa uma revisão de arquitetura contra o OWASP Top 10 (2021) no projeto atual ou em um escopo/fluxo específico informado como argumento.
argument-hint: [escopo opcional, ex. "fluxo de login" ou "src/api"]
---

# /owasp-review

Escopo informado: `$ARGUMENTS` (se vazio, revise o projeto/repositório atual como um todo, priorizando os pontos de entrada externos).

Siga o processo da skill `owasp-top10-review`:

1. Se o escopo não foi informado ou está vago demais, explore o projeto atual (estrutura de pastas, pontos de entrada HTTP/API, dependências) e, se ainda faltar contexto de negócio (dados sensíveis envolvidos, modelo de autenticação, se há multi-tenant), pergunte antes de gerar o relatório.
2. Percorra as 10 categorias do OWASP Top 10 usando os arquivos em `references/` da skill, carregando apenas as relevantes ao escopo.
3. Produza o relatório no formato definido na skill: achados com severidade, componente afetado, risco e recomendação concreta; e uma lista curta das categorias sem risco relevante no escopo, com o motivo.
4. Não simule exploits nem gere payloads de ataque — esta é uma revisão de design, não um teste ativo.
