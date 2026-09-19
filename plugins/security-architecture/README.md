# security-architecture

Plugin de Claude Code com skills para revisar arquitetura, design e código sob a ótica de segurança — não é uma ferramenta de pentest ativo.

## O que tem aqui

- **Skill `owasp-top10-review`** (auto-invocada): dispara quando você pede revisão de arquitetura/design com foco em segurança, threat modeling, ou menciona OWASP Top 10. Cobre as 10 categorias de A01 a A10 (2021), cada uma com checklist de arquitetura e controles de design em `skills/owasp-top10-review/references/`.
- **Comando `/owasp-review [escopo]`**: força a revisão a qualquer momento, opcionalmente com um escopo (`/owasp-review fluxo de checkout`).

## Instalar para desenvolvimento local

A partir de outro projeto (ex. `testing-mia`):

```bash
claude plugin marketplace add /caminho/para/mia --scope project
claude plugin install security-architecture@mia --scope project
```

Ou, sem instalar, carregando direto para uma sessão:

```bash
claude --plugin-dir /caminho/para/mia/plugins/security-architecture
```

## Próximos passos

Mais skills de segurança em arquitetura podem entrar aqui (ex. threat modeling STRIDE, checklist de segurança para APIs, revisão de IaC) — o OWASP Top 10 é só a primeira.
