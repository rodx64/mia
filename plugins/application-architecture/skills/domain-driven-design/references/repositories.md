# Repository

Um repository dá a ilusão de uma coleção em memória de aggregates, escondendo os detalhes de como eles são persistidos e recuperados. A interface pertence ao domínio (ou à camada de aplicação, dependendo do estilo arquitetural), nomeada em termos do negócio; a implementação concreta (SQL, ORM, chamada a outro serviço) é infraestrutura.

## O que procurar

- **Repository operando sobre parte interna de um aggregate** (`buscarItemDoPedido(id)`) em vez do aggregate root inteiro (`buscarPedido(id): Pedido`) — quebra a garantia de que toda mutação passa pelas invariantes da raiz (ver `aggregates-invariantes.md`).
- **Vazamento de detalhe de infraestrutura na interface**: métodos como `buscarComQuery(sql: String)`, `buscarPorFiltroDinamico(filtros: Map<String, Any>)` ou parâmetros de paginação/ordenação amarrados à sintaxe de um banco específico — a interface do domínio não deveria saber que existe SQL, índice, ou um ORM específico por trás.
- **Repository genérico demais** (`CrudRepository<T>` com `save`/`findById`/`delete` só) quando o domínio precisa de operações nomeadas pelo negócio (`buscarPedidosPendentesDoCliente(clienteId)`) — a interface genérica empurra a decisão de "como buscar" para quem chama, em vez de nomear a necessidade real.
- **Repository fazendo papel de domain service**: lógica de negócio (cálculo, decisão) dentro da implementação do repository em vez de só buscar/persistir.

## Como aplicar

- Interface do repository definida em termos do domínio, com um método por necessidade real de negócio (`buscarPorId`, `buscarPedidosPendentesDoCliente`), não um CRUD genérico decorado.
- Opera sempre sobre o aggregate root inteiro — carregar e salvar o aggregate completo, nunca partes isoladas.
- A interface mora no domínio (ou módulo de aplicação); a implementação concreta é um adapter de infraestrutura que a implementa — isso é o próprio "repository como port" da arquitetura hexagonal (ver `hexagonal-architecture/references/ports-adapters.md`).
- Para consultas de leitura que não envolvem regra de negócio nem carregam aggregate completo (relatórios, telas de listagem), considerar um modelo de leitura separado (ver `event-driven-cqrs/references/cqrs.md`) em vez de forçar tudo pela interface do repository de escrita.

## Em Kotlin

- Interface simples no módulo de domínio: `interface PedidoRepository { fun salvar(pedido: Pedido); fun buscarPorId(id: PedidoId): Pedido? }`, implementada em um módulo de infraestrutura separado (`class PedidoRepositoryJdbc(...) : PedidoRepository`).
- Para testes de domínio/aplicação, uma implementação em memória (`class PedidoRepositoryEmMemoria : PedidoRepository`) evita subir banco real e mantém o teste rápido — só é possível porque a interface não vazou detalhe de infraestrutura.

## Perguntas para levantar contexto

- O repository expõe métodos para buscar/alterar parte interna de um aggregate, ou só a raiz inteira?
- Algum método da interface do repository menciona um detalhe de tecnologia (SQL, sintaxe de índice, nome de coluna)?
