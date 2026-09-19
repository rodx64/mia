# Specification

O padrão Specification encapsula uma regra de negócio combinável — um predicado nomeado em termos do domínio — para ser reutilizada em validação, seleção de candidatos em memória, ou (com adaptação) construção de critério de busca, em vez de espalhar a mesma condição booleana em vários lugares.

## O que procurar

- **Mesma condição de negócio repetida** em `if`s espalhados pelo código (ex.: a regra "pedido é elegível para desconto" checada de formas ligeiramente diferentes em três lugares).
- **Método de aggregate/service com nome vago tipo `validar()` ou `checar()`** escondendo várias regras de negócio distintas dentro de um único método grande, sem nome individual para cada regra.
- **Specification usada para toda validação simples**: aplicar o padrão para uma condição trivial de um único campo (`idade >= 18`) é over-engineering — o ganho do padrão está em regras que precisam ser nomeadas, combinadas (`and`/`or`/`not`) ou reutilizadas em mais de um contexto.

## Como aplicar

- Extraia a condição para um tipo nomeado pelo negócio (`PedidoElegivelParaDesconto`, não `Regra1`) só quando ela é reutilizada em mais de um lugar, ou quando o nome em si já documenta uma decisão de negócio importante.
- Specifications devem ser combináveis (`and`, `or`, `not`) quando o domínio genuinamente compõe regras — se nunca são combinadas na prática, um método simples já resolve.
- Manter a specification stateless e sem dependência de infraestrutura — ela avalia sobre o objeto de domínio recebido, não faz I/O.

## Em Kotlin

- `fun interface` deixa a specification leve e permite lambdas onde fizer sentido:

  ```kotlin
  fun interface Specification<T> {
      fun isSatisfiedBy(candidato: T): Boolean

      infix fun and(outra: Specification<T>): Specification<T> =
          Specification { isSatisfiedBy(it) && outra.isSatisfiedBy(it) }
  }

  val pedidoElegivelParaDesconto = Specification<Pedido> { it.valorTotal() >= Dinheiro(100, "BRL") }
  ```
- Para regras nomeadas mas usadas uma única vez, um método de extensão (`fun Pedido.elegivelParaDesconto(): Boolean`) já dá o nome de domínio sem o overhead do padrão completo — reservar `Specification` para quando a combinabilidade (`and`/`or`) é realmente usada.

## Perguntas para levantar contexto

- Essa regra de negócio aparece em mais de um lugar do código, ou é usada uma única vez?
- Faz sentido combinar essa regra com outras (ex.: "elegível para desconto E cliente ativo")?
