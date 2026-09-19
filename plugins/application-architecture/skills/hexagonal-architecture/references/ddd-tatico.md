# DDD tático dentro da arquitetura hexagonal

Domain-Driven Design tático dá vocabulário e forma ao que fica *dentro* do hexágono. A arquitetura hexagonal diz onde estão as fronteiras (domínio isolado de infraestrutura); o DDD tático diz como organizar o que está dentro dessa fronteira.

## Aggregate e Aggregate Root

- **Aggregate**: um cluster de entidades e value objects tratado como unidade de consistência transacional. Mudanças dentro do aggregate obedecem invariantes que são garantidas atomicamente.
- **Aggregate Root**: a única entidade do cluster acessível de fora. Todo acesso externo (leitura via repository, comando de mutação) passa pela raiz — nunca se manipula uma entidade filha do aggregate diretamente de fora.
- Sinal de erro comum: repository expondo `buscarItemDoPedido(id)` separado de `Pedido`, permitindo que código externo altere um item sem passar pelas invariantes do agregado (ex.: total do pedido ficando inconsistente com os itens).
- Aggregates pequenos são preferíveis a grandes: um aggregate que tenta modelar "tudo relacionado" vira gargalo de concorrência e contêiner de lógica não relacionada.

## Value Object

- Objeto definido pelos seus atributos, sem identidade própria — dois value objects com os mesmos valores são iguais (`Dinheiro(10, "BRL") == Dinheiro(10, "BRL")`). Imutável.
- Usado para encapsular validação e comportamento de conceitos do domínio que, de outra forma, virariam primitivos soltos (`string` de e-mail sem validação, `int` de centavos sem moeda associada).
- Sinal de subuso: campos primitivos (`string`, `decimal`) espalhados representando conceitos com regras próprias (CPF, e-mail, dinheiro, intervalo de datas) sem nenhuma validação centralizada.

## Domain Event

- Representa algo que aconteceu no domínio e é relevante para outras partes do sistema (`PedidoConfirmado`, `PagamentoRecusado`). Nomeado no passado, imutável.
- Dentro do hexágono, o domínio apenas *produz* o evento (retorna, registra em uma lista interna do aggregate); quem efetivamente publica em um broker/bus é um adapter de saída — o domínio não sabe se o evento vai para Kafka, RabbitMQ ou uma tabela outbox.
- Não confundir com integration event (ver `event-driven-cqrs/references/event-driven.md`) — domain event é um conceito interno, mais granular, que pode ou não virar um evento de integração publicado externamente.

## Repository como port

- `Repository` é a porta de saída clássica do DDD tático: interface definida em termos do domínio (`salvar(pedido)`, `buscarPorId(id): Pedido`), implementada por um adapter concreto (Postgres, Mongo, em memória para teste).
- A interface do repository opera sobre o aggregate root inteiro, nunca sobre partes internas dele — reforça a regra de que o aggregate é a unidade de consistência.
- Métodos do tipo `buscarPorFiltroComplexo` que vazam detalhe de query (SQL parcial, sintaxe de índice) para a interface do domínio são sinal de vazamento de infraestrutura para o port.

## Perguntas para levantar contexto

- Quais são os aggregates do domínio e qual é a raiz de cada um? Existe acesso direto a entidades internas de um aggregate a partir de fora dele?
- Conceitos como dinheiro, e-mail, CPF, período — são value objects com validação própria ou primitivos soltos validados (ou não) em vários lugares?
- Eventos de domínio são publicados diretamente por código de domínio em um broker, ou existe um adapter de saída fazendo essa ponte?
