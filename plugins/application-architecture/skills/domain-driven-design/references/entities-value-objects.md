# Entity vs. Value Object

A distinção mais básica do DDD tático: uma **entity** é definida pela sua identidade e tem ciclo de vida (dois `Pedido` com os mesmos dados em momentos diferentes ainda são "o mesmo pedido" se tiverem o mesmo ID); um **value object** é definido pelos seus atributos, sem identidade própria e imutável (dois `Dinheiro(10, "BRL")` são intercambiáveis).

## O que procurar

- **Primitive obsession**: `string` para e-mail/CPF, `int`/`decimal` solto para dinheiro, `string`/`int` para intervalo de datas — conceitos do domínio com regras próprias representados como tipo primitivo, com validação (se existir) espalhada em vários lugares em vez de centralizada.
- **Entity com igualdade estrutural**: comparar duas entities pelos atributos em vez de pelo ID — duas pessoas com mesmo nome e mesma data de nascimento não são a mesma pessoa.
- **Value object mutável**: um "value object" com setters, que pode ser alterado depois de criado — se dois objetos podem divergir depois de criados com os mesmos valores, não é um value object de verdade.
- **Identidade desnecessária**: dar um ID a algo que não precisa de identidade nem ciclo de vida próprio (ex.: um `Endereco` dentro de um `Pedido` não precisa de ID se nunca é referenciado independentemente).

## Como aplicar

- Entity: igualdade e hash baseados só no ID, nunca nos demais atributos. Ciclo de vida rastreável (criada, modificada, talvez removida).
- Value object: imutável (qualquer "alteração" cria uma nova instância), igualdade estrutural, sem ID técnico. Deve encapsular validação própria no ponto de construção — um `Cpf` inválido nunca deveria existir como instância.
- Se um conceito primitivo aparece em mais de um lugar com a mesma regra de validação implícita, é candidato a virar value object.
- Comportamento que opera só sobre os atributos de um value object (ex.: `Dinheiro.somar(outro)`) deve morar nele, não em uma função utilitária externa.

## Em Kotlin

- `data class` gera `equals`/`hashCode`/`copy` estruturais — ótimo padrão para value objects (`data class Dinheiro(val centavos: Long, val moeda: Moeda)`), mas perigoso para entities se usado sem cuidado: um `data class Pedido(val id: PedidoId, val itens: List<Item>, ...)` vai comparar por todos os campos, não só pelo ID. Para entity, sobrescreva `equals`/`hashCode` manualmente baseado só no ID, ou evite `data class` e implemente a igualdade explicitamente.
- `value class` (inline class) é a ferramenta certa para IDs tipados e primitivos com significado sem overhead de alocação: `value class PedidoId(val valor: UUID)`, `value class Email(val valor: String) { init { require(valor.contains("@")) } }` — evita passar um `UUID`/`String` cru onde qualquer outro `UUID`/`String` compilaria sem erro.
- Construtores/factories com `init { require(...) }` ou `companion object` com função de criação que retorna `Result`/lança exceção centralizam a validação do value object em um único lugar.

## Perguntas para levantar contexto

- Esse conceito precisa ser rastreado ao longo do tempo (tem ciclo de vida, pode mudar mantendo "a mesma identidade"), ou é só um conjunto de valores que descreve algo?
- Existe alguma validação de formato/regra de negócio que hoje está duplicada em mais de um lugar para o mesmo tipo de dado primitivo?
