# Linguagem Ubíqua

O vocabulário do negócio (como as pessoas de domínio falam sobre o problema) deve ser o mesmo vocabulário usado em nomes de classes, métodos e variáveis no código do domínio — sem tradução para termos técnicos genéricos no meio do caminho.

## O que procurar

- **Nomes técnicos genéricos** onde deveria haver um termo do negócio: `Manager`, `Processor`, `Helper`, `Handler`, `Data`, `Info`, `Util` — esses nomes não dizem nada sobre o domínio, só sobre a existência de código.
- **Tradução perdida entre negócio e código**: a pessoa de negócio fala em "cancelamento", o código só tem `setStatus(4)` ou `update(status: "CANCELLED")`. O conceito "cancelar" devia ser um método nomeado (`pedido.cancelar()`), não um efeito colateral de um setter genérico.
- **Mesmo termo, significados diferentes** em partes diferentes do código sem essa divergência ser intencional (ver `bounded-contexts.md` quando a divergência é real e justifica separação).
- **Glossário inexistente ou desatualizado**: ninguém sabe ao certo o que "conta ativa" significa tecnicamente vs. o que o time de negócio entende por isso.

## Como aplicar

- Nomeie classes, métodos e eventos com o termo que a pessoa de domínio usaria em uma frase, não com o termo que descreve a implementação (`pedido.confirmar()`, não `pedido.setStatus(CONFIRMED)`).
- Operações relevantes do negócio viram métodos nomeados no domínio, não flags/enums setados de fora.
- Mantenha um glossário vivo (arquivo curto no repo, ou seção do README do módulo de domínio) só com os termos que geram confusão — não é preciso documentar tudo, só o que já causou mal-entendido.
- Quando a pessoa de negócio usa um termo nas reuniões, e o código usa outro para a mesma coisa, mude o código — a linguagem ubíqua se ajusta ao negócio, não o contrário.

## Em Kotlin

- Prefira métodos com nome de intenção de negócio (`pedido.confirmar()`) em vez de setters (`pedido.status = Status.CONFIRMADO`) — Kotlin facilita expor propriedades mutáveis (`var`), o que convida a pular a linguagem ubíqua; use `private set` ou `val` com métodos explícitos para forçar a operação nomeada.
- `sealed class`/`sealed interface` para modelar um vocabulário fechado do negócio (ex.: `sealed interface StatusPedido { object Rascunho; object Confirmado; ... }`) deixa o compilador reforçar que só esses estados existem, com nomes que o negócio reconhece.

## Perguntas para levantar contexto

- Existe um termo que a pessoa de negócio usa e que não aparece em lugar nenhum do código com esse nome?
- Alguma classe ou método tem nome que só faz sentido para quem já leu a implementação?
