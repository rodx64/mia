---
name: owasp-top10-review
description: Use esta skill ao revisar arquitetura, design ou código de um sistema sob a ótica de segurança — gatilhos como "revisar segurança dessa arquitetura", "threat model", "quais riscos de segurança esse design tem", "avaliar segurança antes de codar", "auditoria de arquitetura", "code review com foco em segurança", ou qualquer menção a "OWASP Top 10". Cobre as 10 categorias do OWASP Top 10 (2021) em nível de design/arquitetura — não é para exploração ativa de vulnerabilidades (pentest, payloads, exploits) contra um sistema já rodando.
version: 0.1.0
---

# Revisão de arquitetura contra o OWASP Top 10 (2021)

Revisão de segurança em nível de **design e arquitetura**: dado um sistema (existente ou em planejamento), identificar onde ele é vulnerável a cada uma das 10 categorias do OWASP Top 10 (2021) e propor controles de mitigação — sem executar ataques.

## Quando usar

- Revisão de arquitetura/design antes ou durante o desenvolvimento (ADR, RFC, diagrama de componentes).
- Code review com lente de segurança (não só correção funcional).
- Threat modeling de um fluxo específico (ex.: cadastro de usuário, upload de arquivo, integração com serviço externo).
- Perguntas do tipo "isso aqui tem algum risco OWASP?".

**Não usar para**: testes ativos de exploração (SQLi, XSS, fuzzing, payloads contra um alvo rodando) — isso é pentest hands-on, um domínio diferente deste plugin.

## Processo

1. **Entenda o sistema.** Levante (perguntando ao usuário ou lendo o código/repo):
   - Componentes e como se comunicam (monólito, microsserviços, serverless).
   - Fronteiras de confiança: onde dados de fora entram (API pública, upload, webhook, fila).
   - Modelo de autenticação/autorização (quem pode fazer o quê).
   - Dados sensíveis armazenados/transmitidos e onde.
   - Dependências externas (bibliotecas, serviços terceiros, containers/imagens base).
   - Como logs, erros e configuração são tratados hoje.

2. **Percorra as 10 categorias.** Para cada uma, use o arquivo correspondente em `references/` (carregue só o que for relevante ao escopo da revisão — não precisa ler os 10 de uma vez se a revisão é sobre um fluxo específico):

   | # | Categoria | Referência |
   |---|-----------|-------------|
   | A01 | Broken Access Control | `references/a01-broken-access-control.md` |
   | A02 | Cryptographic Failures | `references/a02-cryptographic-failures.md` |
   | A03 | Injection | `references/a03-injection.md` |
   | A04 | Insecure Design | `references/a04-insecure-design.md` |
   | A05 | Security Misconfiguration | `references/a05-security-misconfiguration.md` |
   | A06 | Vulnerable and Outdated Components | `references/a06-vulnerable-outdated-components.md` |
   | A07 | Identification and Authentication Failures | `references/a07-auth-failures.md` |
   | A08 | Software and Data Integrity Failures | `references/a08-software-data-integrity-failures.md` |
   | A09 | Security Logging and Monitoring Failures | `references/a09-logging-monitoring-failures.md` |
   | A10 | Server-Side Request Forgery (SSRF) | `references/a10-ssrf.md` |

3. **Classifique cada achado** com: categoria OWASP, componente/fluxo afetado, severidade (crítico/alto/médio/baixo — pense em impacto x probabilidade dado o contexto, não um score genérico), e recomendação concreta (controle de design, não "use boas práticas").

4. **Priorize.** Não liste 40 itens teóricos — foque no que é plausível dado o sistema descrito. Se uma categoria claramente não se aplica (ex.: sistema sem SSRF possível porque não faz requisições a URLs controladas por usuário), diga isso em uma linha e siga.

5. **Entregue um relatório curto**, estruturado assim:

   ```
   ## Revisão OWASP Top 10 — <escopo>

   ### Achados
   - **[A0X · severidade] Título curto**
     Onde: <componente/fluxo>
     Risco: <1-2 frases>
     Recomendação: <ação concreta>

   ### Categorias sem risco relevante no escopo
   - A0Y — <por quê>
   ```

## Notas

- Isso é revisão estática/de design. Se o usuário quiser validar um achado na prática (ex.: confirmar que um IDOR realmente funciona), isso sai do escopo desta skill — é trabalho de teste ativo.
- Se o sistema descrito for muito genérico ou incompleto, pergunte pelos pontos do passo 1 antes de gerar o relatório — achado sem contexto real do sistema é ruído.
