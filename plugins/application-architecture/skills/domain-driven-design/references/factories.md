# Factory

Uma factory encapsula a criação de um aggregate/entity quando essa criação é complexa o suficiente para não caber num construtor simples: múltiplas invariantes precisam ser garantidas já no nascimento, existe escolha entre implementações, ou a criação depende de outros objetos que não deveriam ficar acoplados ao construtor público.

## O que procurar

- **Construtor público aceitando estado inválido**: qualquer código consegue montar um aggregate em estado inconsistente porque o construtor não valida as invariantes de criação.
- **Lógica de criação duplicada** em vários lugares do código (cada ponto de entrada monta o aggregate "do seu jeito", cada um esquecendo uma regra diferente).
- **Criação complexa misturada com lógica de aplicação**: um controller/handler HTTP montando um aggregate passo a passo, decidindo detalhes de negócio no meio do caminho, em vez de delegar para uma factory do domínio.

## Como aplicar

- Se a criação de um aggregate exige mais que "atribuir os campos recebidos" — calcular um valor derivado, decidir entre subtipos, validar uma combinação de campos — isso é responsabilidade de uma factory, não de quem está chamando o construtor.
- A factory garante que o aggregate só existe em estado válido desde o primeiro instante; construtor bruto fica privado/inacessível de fora do módulo de domínio.
- Factory não é necessária para criação simples: um value object com `init { require(...) }` ou um construtor direto já resolvem a maioria dos casos — reservar factory dedicada para os casos genuinamente complexos.

## Em Kotlin

- Padrão comum: construtor `private constructor(...)` + `companion object` com função de criação nomeada pelo negócio, retornando o aggregate ou um tipo de erro (`Result<Pedido>`, ou lançando exceção de domínio):

  ```kotlin
  class Pedido private constructor(val id: PedidoId, ...) {
      companion object {
          fun novo(clienteId: ClienteId, itens: List<ItemPedido>): Pedido {
              require(itens.isNotEmpty()) { "Pedido precisa de ao menos um item" }
              return Pedido(PedidoId.gerar(), clienteId, itens, StatusPedido.Rascunho)
          }
      }
  }
  ```
- Para escolha entre subtipos (`sealed class` de aggregate/entity com variantes), a factory decide qual variante instanciar com base nos parâmetros recebidos, escondendo essa decisão de quem chama.

## Perguntas para levantar contexto

- Existe mais de um lugar no código criando o mesmo tipo de aggregate, e esses lugares aplicam as mesmas regras de validação?
- A criação desse aggregate depende de decidir algo (qual subtipo, qual valor derivado) além de só copiar os parâmetros recebidos?
