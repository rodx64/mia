---
name: event-driven-cqrs
description: Use esta skill ao revisar ou decidir se/como aplicar arquitetura orientada a eventos ou CQRS — gatilhos como "faz sentido usar eventos aqui", "devo separar comando e query nesse serviço", "revisar esse design orientado a eventos", "esse consumidor é idempotente?", "CQRS resolve esse problema de leitura", "event sourcing ou CQRS, qual preciso", ou qualquer menção a "event-driven", "domain event", "integration event", "CQRS", "event sourcing". Cobre tanto revisão de design/código existente quanto guia de decisão sobre quando vale a pena adotar cada padrão.
version: 0.1.0
---

# Event-Driven Architecture e CQRS

Dois padrões frequentemente combinados mas conceitualmente distintos: comunicação assíncrona via eventos entre componentes, e separação entre modelo de escrita e modelo de leitura. Nenhum dos dois é default — servem para revisar se já estão bem aplicados e para decidir se valem a pena antes de introduzir a complexidade.

## Quando usar

- Decidir se um fluxo deveria ser síncrono (chamada direta) ou assíncrono (evento), e o porquê.
- Revisar um design orientado a eventos existente: acoplamento oculto via schema, falta de idempotência, consistência eventual mal comunicada.
- Decidir se CQRS resolve um problema real de assimetria entre leitura e escrita, ou se é complexidade antecipada sem necessidade.
- Esclarecer confusão entre CQRS e Event Sourcing — são conceitos independentes, frequentemente misturados na conversa.

**Não usar para**: aprovar de forma reflexiva "vamos usar eventos" ou "vamos separar leitura e escrita" sem antes checar se o problema real justifica — o processo abaixo existe justamente para essa checagem.

## Processo

1. **Entenda o contexto.** Levante (perguntando ou lendo o código/design):
   - O fluxo em questão tem um ou múltiplos consumidores reais do mesmo fato/evento?
   - Existe uma real assimetria entre necessidades de leitura e escrita (formatos, volume, escala), ou é um CRUD comum?
   - Se já há eventos: existe contrato/schema versionado? Os consumidores são idempotentes? Existe SLA comunicado de consistência eventual?
   - O objetivo é decidir se vale introduzir o padrão, ou revisar uma implementação já existente?

2. **Percorra as referências relevantes** (carregue só o necessário ao escopo):

   | Tema | Referência |
   |---|---|
   | Produtores/consumidores, domain vs. integration event, armadilhas (idempotência, acoplamento de schema, consistência eventual) | `references/event-driven.md` |
   | Separação de modelo de comando/query, quando vale a pena, CQRS simples vs. stores separados, relação com event sourcing | `references/cqrs.md` |

3. **Para decisão** ("devo usar isso?"): responda primeiro se o problema real justifica a complexidade (múltiplos consumidores reais? assimetria real de leitura/escrita?). Se não, recomende a alternativa mais simples explicitamente antes de detalhar como implementar o padrão.

4. **Para revisão**: classifique cada achado por tipo de problema (falta de idempotência, schema sem versionamento, consistência eventual não comunicada, CQRS sem assimetria real que justifique) com componente afetado e correção concreta.

5. **Entregue um relatório curto**, estruturado assim:

   ```
   ## Revisão/Decisão — Event-Driven / CQRS — <escopo>

   ### Recomendação ou Achados
   - **[Idempotência | Acoplamento de schema | Consistência eventual | Assimetria leitura/escrita] Título curto**
     Onde: <componente/fluxo>
     Descrição: <1-2 frases>
     Recomendação: <ação concreta, incluindo "não use este padrão aqui" quando for o caso>

   ### Pontos sem problema / já corretos
   - <componente> — <por quê>
   ```

## Notas

- A pergunta mais importante em qualquer conversa sobre estes padrões é "isso resolve um problema que existe hoje?" — ambos têm custo de infraestrutura e cognitivo real, e o ganho só aparece quando a assimetria (leitura/escrita) ou o desacoplamento (múltiplos consumidores) é genuíno.
- Nunca equipare CQRS a Event Sourcing na resposta — são decisões independentes; deixe isso explícito quando a pergunta do usuário misturar os dois.
- Se o contexto descrito for insuficiente para avaliar consumidores reais ou assimetria de leitura/escrita, pergunte antes de recomendar — recomendação sem esse dado é chute.
