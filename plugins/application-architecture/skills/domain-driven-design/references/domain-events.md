# Domain Event

Um domain event representa algo que **já aconteceu** no domínio e é relevante para outras partes do sistema (`PedidoConfirmado`, `PagamentoRecusado`, `EstoqueEsgotado`). Nomeado no passado, imutável, carrega os dados relevantes do que ocorreu no momento em que ocorreu.

## O que procurar

- **Evento publicado direto para infraestrutura de dentro do domínio**: código de domínio chamando um broker (Kafka, RabbitMQ) ou fazendo I/O diretamente ao invés de só registrar/retornar o evento — isso vaza infraestrutura para dentro do domínio (ver `hexagonal-architecture/references/dependency-rule.md`).
- **Evento nomeado no presente ou como comando**: `ConfirmarPedido` é um comando (uma intenção, ainda não aconteceu); `PedidoConfirmado` é um evento (já aconteceu). Confundir os dois é sinal de modelagem imprecisa.
- **Confusão entre domain event e integration event**: tratar todo domain event como algo que precisa virar mensagem publicada externamente — nem todo evento de domínio interessa fora do bounded context (ver `event-driven-cqrs/references/event-driven.md` para a diferença e quando um domain event vira integration event).
- **Evento mutável ou incompleto**: evento que pode ser alterado depois de criado, ou que não carrega dado suficiente para quem for reagir a ele (força o consumidor a buscar mais dados, criando acoplamento temporal).

## Como aplicar

- O aggregate produz o evento como efeito de uma operação de negócio (`pedido.confirmar()` internamente registra `PedidoConfirmado`) — o aggregate não publica, só registra/retorna.
- Quem lê os eventos registrados e efetivamente publica (broker, tabela outbox, outro mecanismo) é a camada de aplicação ou um adapter de saída — nunca o próprio domínio.
- Evento imutável, com todos os dados relevantes do momento em que ocorreu (não uma referência que exige buscar estado atual depois — o estado atual pode já ter mudado).
- Nomeie no passado e em termos do negócio: o evento documenta um fato que a pessoa de domínio reconheceria.

## Em Kotlin

- `sealed interface`/`sealed class` para a família de eventos de um aggregate (`sealed interface EventoPedido { data class Confirmado(...): EventoPedido; data class Cancelado(...): EventoPedido }`) permite `when` exaustivo no consumidor — o compilador avisa se um novo evento não for tratado em algum lugar.
- Aggregate root pode manter uma lista interna privada de eventos pendentes (`private val _eventos = mutableListOf<EventoPedido>()`) com um método para a camada de aplicação coletar e limpar após persistir (`fun eventosPendentes(): List<EventoPedido>`, `fun limparEventos()`) — um padrão comum para não acoplar o aggregate a um mecanismo de publicação.

## Perguntas para levantar contexto

- Esse evento está sendo publicado direto do código de domínio para um broker/fila, ou existe uma camada de aplicação/adapter fazendo essa ponte?
- Esse evento é relevante só dentro deste módulo/bounded context, ou outras partes do sistema (ou outros sistemas) precisam saber dele?
