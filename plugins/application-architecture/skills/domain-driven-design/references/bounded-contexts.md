# Bounded Context (estratégico, leve)

Um bounded context é a fronteira dentro da qual um modelo de domínio e sua linguagem ubíqua são consistentes. Fora dela, o mesmo termo pode (e frequentemente deve) significar outra coisa. Isso é o único conceito estratégico coberto aqui — incluído porque define até onde o resto desta skill (aggregates, entities, linguagem ubíqua) se aplica sem ambiguidade.

## O que procurar

- **Mesmo termo, dois modelos diferentes, tratados como se fossem um só**: `Cliente` no módulo de vendas (nome, histórico de compras, limite de crédito) e `Cliente` no módulo de suporte (nome, tickets abertos, SLA) forçados a ser a mesma classe/tabela — cada tentativa de adicionar campo de um lado quebra ou infla o outro.
- **Um aggregate gigante tentando servir múltiplos contextos de uso** ao mesmo tempo, geralmente crescendo com condicionais (`if contexto == "vendas"`) porque na verdade são dois modelos com uma fronteira não reconhecida.
- **Integração direta entre módulos** que deveriam ser contextos separados, sem nenhuma tradução entre os modelos (mudança em um propaga direto e silenciosamente para o outro).

## Como aplicar

- Não crie fronteiras de contexto preventivamente em um sistema pequeno/novo — comece com um modelo único e só separe quando o mesmo termo genuinamente divergir de significado ou de regras entre duas partes do sistema.
- Quando a divergência é real, cada contexto tem seu próprio modelo (sua própria classe `Cliente`, com os campos que fazem sentido ali), e a comunicação entre contextos acontece por tradução explícita (mapeamento de um modelo para o outro), não por compartilhar a mesma classe.
- Padrões de relação entre contextos, para nomear a situação encontrada (não é preciso aplicar todos, só reconhecer qual descreve o caso real):
  - **Shared kernel**: dois contextos compartilham deliberadamente uma parte pequena do modelo (ex.: um `Dinheiro` value object comum) — funciona quando os times coordenam mudanças nessa parte compartilhada.
  - **Customer-supplier**: um contexto (supplier) atende as necessidades de outro (customer), que depende do formato que o supplier expõe.
  - **Conformist**: um contexto aceita o modelo do outro como está, sem tradução, porque não tem poder/necessidade de negociar mudanças.
  - **Anticorruption layer**: um contexto traduz o modelo de outro (frequentemente externo ou legado) para seus próprios termos na fronteira, evitando que o vocabulário/estrutura alheia vaze para dentro do próprio modelo.

## Em Kotlin

- Bounded contexts costumam mapear para módulos Gradle separados (`:vendas`, `:suporte`) ou pacotes bem isolados, cada um com sua própria classe de domínio para conceitos com o mesmo nome mas modelos diferentes — evite um único módulo `:comum` viciado em acumular "tudo que parece compartilhado" sem ser de fato um shared kernel deliberado.
- Uma anticorruption layer aparece tipicamente como um adapter (no sentido de `hexagonal-architecture`) que traduz DTOs/modelos de um sistema externo para os value objects/entities do próprio contexto, isolando o resto do domínio dessa tradução.

## Perguntas para levantar contexto

- Esse termo/conceito é usado por mais de uma parte do sistema? As regras e os dados relevantes são realmente os mesmos nas duas, ou só o nome coincide?
- Existe um módulo "compartilhado" hoje que na prática só serve a um dos lados, forçando o outro a se adaptar?
