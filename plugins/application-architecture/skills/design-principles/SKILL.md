---
name: design-principles
description: Use esta skill ao revisar código ou decidir como estruturar uma classe/função sob a ótica de princípios clássicos de design orientado a objetos — gatilhos como "isso viola SOLID?", "essa classe está fazendo coisa demais", "estou over-engineering isso?", "faz sentido duplicar esse código ou extrair uma função?", "esse encadeamento de getters está certo?", "revisar esse design de classes", ou qualquer menção a "SOLID", "KISS", "DRY", "YAGNI", "Tell Don't Ask", "Law of Demeter", "Lei de Demeter". Cobre tanto revisão de código existente quanto guia de decisão durante o desenvolvimento (quando aplicar um princípio, quando não aplicar).
version: 0.1.0
---

# Princípios de Design

Cinco princípios clássicos de design orientado a objetos, usados tanto para revisar código quanto para decidir como estruturar algo novo: SOLID, KISS, DRY/YAGNI, Tell Don't Ask e Law of Demeter. Nenhum é absoluto — o valor está em identificar o sinal concreto de violação (ou de over-engineering na direção oposta) e propor a correção mínima que resolve o problema real.

## Quando usar

- Revisão de código perguntando se uma classe/módulo respeita um princípio específico.
- Decisão durante o desenvolvimento sobre extrair ou não uma abstração, separar ou não uma responsabilidade, unificar ou não um código duplicado.
- Dúvida do tipo "estou complicando isso à toa?" ou, no sentido oposto, "essa classe está sobrecarregada?".
- Identificar acoplamento por encadeamento de getters ou por lógica de decisão vivendo fora do objeto dono do dado.

**Não usar para**: aplicar um princípio de forma dogmática sem o problema que ele resolve — isso é o próprio anti-padrão que KISS e YAGNI descrevem. O objetivo desta skill é balancear os princípios entre si, não maximizar aderência a um deles isoladamente.

## Processo

1. **Entenda o contexto.** Levante (perguntando ou lendo o código):
   - O que motivou a dúvida: um code review pontual, uma decisão de design antes de escrever código, ou uma sensação geral de "isso está confuso/enrolado"?
   - Qual é o trecho/classe/módulo específico em questão — princípios de design se aplicam a decisões concretas, não a avaliações abstratas do sistema inteiro.
   - Existe histórico de dor real (bug, dificuldade de teste, medo de mexer) associado a esse trecho, ou é uma preocupação teórica?

2. **Percorra as referências relevantes** (carregue só o necessário ao escopo — a dúvida geralmente aponta para um ou dois princípios, não os cinco):

   | Princípio | Referência |
   |---|---|
   | Tell, Don't Ask — comando em vez de consulta + decisão externa | `references/tell-dont-ask.md` |
   | KISS — simplicidade, abstração prematura, configurabilidade não pedida | `references/kiss.md` |
   | SOLID — SRP, OCP, LSP, ISP, DIP | `references/solid.md` |
   | DRY e YAGNI — duplicação de conhecimento vs. construção antecipada | `references/dry-yagni.md` |
   | Law of Demeter — encadeamento de acesso, acoplamento estrutural | `references/law-of-demeter.md` |

3. **Para revisão**: classifique cada achado pelo princípio violado, com o sinal concreto observado (não uma descrição genérica) e uma correção específica e mínima — não proponha reescrever tudo se o ajuste pontual resolve.

4. **Para decisão durante desenvolvimento**: ajude a decidir entre a opção mais simples e a mais abstrata, sendo explícito sobre o trade-off (custo de indireção agora vs. ganho de flexibilidade quando/se a variação aparecer).

5. **Entregue um relatório curto**, estruturado assim:

   ```
   ## Revisão/Decisão — Princípios de Design — <escopo>

   ### Achados / Recomendação
   - **[SRP | OCP | LSP | ISP | DIP | KISS | DRY | YAGNI | Tell Don't Ask | Law of Demeter] Título curto**
     Onde: <classe/trecho>
     Descrição: <1-2 frases com o sinal concreto observado>
     Recomendação: <ação específica e mínima>

   ### Pontos sem problema
   - <trecho> — <por quê>
   ```

## Notas

- Os princípios frequentemente se cruzam no mesmo trecho (ex.: um encadeamento de getters usado para decisão externa é ao mesmo tempo violação de Law of Demeter e de Tell Don't Ask) — cite as duas lentes quando ambas se aplicarem, em vez de forçar uma escolha artificial entre elas.
- DRY e KISS puxam em direções opostas quando aplicados sem critério (DRY tende a unificar, criando abstração; KISS tende a manter simples, tolerando alguma duplicação) — ao encontrar tensão entre os dois, prefira a opção com menor custo de manutenção dado o histórico real do código, não a mais "elegante" no papel.
- Se o trecho em questão for pequeno demais ou genérico demais para avaliar (ex.: um nome de classe sem ver o corpo), peça o código antes de apontar violação.
