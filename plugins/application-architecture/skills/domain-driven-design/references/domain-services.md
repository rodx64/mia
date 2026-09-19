# Domain Service (e o risco de modelo anêmico)

Um **domain service** encapsula uma regra de negócio que não pertence naturalmente a nenhuma entity ou value object específico — geralmente porque envolve mais de um aggregate, ou é uma operação sem estado próprio (ex.: calcular frete considerando regras que dependem de mais de uma entidade). É diferente de um **application service**, que orquestra casos de uso (transação, chamada a repository, publicação de evento) mas não deveria conter regra de negócio.

## O que procurar

- **Modelo anêmico**: entities que são só dados (getters/setters, sem métodos com comportamento), com toda regra de negócio concentrada em classes `*Service`/`*Manager` que recebem a entity, leem seus campos e decidem por fora. Isso é o sintoma mais comum de DDD "de nome" sem DDD de fato.
- **Domain service usado para o que deveria ser método de entity**: uma operação que só precisa dos dados de um único aggregate virou um método de service — deveria estar na entity/aggregate root.
- **Application service fazendo papel de domain service**: regra de negócio (não só orquestração) dentro de um service de camada de aplicação, misturando responsabilidade de coordenar (transação, I/O) com responsabilidade de decidir (regra de domínio).
- **Domain service com estado**: um "service" guardando estado mutável entre chamadas — domain services deveriam ser stateless, a regra opera sobre os parâmetros recebidos.

## Como aplicar

- Primeira pergunta ao decidir onde colocar uma regra: "essa operação usa dados de só um aggregate?" Se sim, é método desse aggregate/entity, não um service.
- Domain service só quando a regra genuinamente não pertence a nenhuma entity sozinha (ex.: `CalculadoraDeFrete` que precisa de `Pedido` + `Transportadora` + `TabelaDeFrete`) ou é uma política que pode variar independente do estado de qualquer entity (ex.: estratégia de precificação).
- Domain service continua na camada de domínio (não conhece infraestrutura), é stateless, e é nomeado com um verbo/conceito do negócio, não `Service` genérico (`PoliticaDeDesconto`, não `DescontoService`).
- Application service fica fora do domínio: orquestra (busca aggregate via repository, chama método do aggregate/domain service, persiste, publica evento) mas não decide regra de negócio.

## Em Kotlin

- Domain service stateless modela-se bem como `object` (singleton sem estado) ou classe com só métodos puros recebendo os aggregates/value objects necessários como parâmetro — sem injeção de dependência de infraestrutura no construtor.
- Cuidado com o padrão comum em times vindos de Spring de colocar `@Service` em tudo: nem toda classe anotada como service da camada de aplicação deveria concentrar regra de negócio — isso é o caminho mais direto para o modelo anêmico.

## Perguntas para levantar contexto

- Essa regra de negócio está numa classe `*Service`/`*Manager` só porque "sempre foi assim", ou porque genuinamente não pertence a nenhuma entity específica?
- As entities do domínio têm métodos com comportamento, ou são só estruturas de dados manipuladas de fora?
