# Law of Demeter

"Fale só com seus vizinhos imediatos." Um método só deveria chamar métodos de: o próprio objeto, seus parâmetros, objetos que ele mesmo cria, e seus componentes diretos (campos do próprio objeto) — nunca de um objeto obtido através de outro objeto.

## O sinal clássico: encadeamentos tipo `a.getB().getC().getD()`

- `pedido.getCliente().getEndereco().getCidade().getNome()` — o código que escreve isso precisa conhecer a estrutura interna de `Pedido`, `Cliente` e `Endereco` para chegar no dado que quer. Qualquer mudança na forma como `Cliente` guarda o endereço (ex.: passa a ter múltiplos endereços) quebra todo código que fez esse encadeamento em qualquer lugar do sistema.
- O problema não é sintático (o tamanho da linha), é de acoplamento: quem chamou passou a depender da cadeia inteira de estruturas internas, não só do objeto com quem "conversou" diretamente.
- Isso é diferente de encadear métodos do mesmo objeto (method chaining/fluent interface, tipo `builder.comNome(x).comIdade(y).build()`) — ali cada chamada retorna o próprio tipo (ou um builder), não está atravessando objetos de domínio distintos.

## Como corrigir

- Perguntar ao objeto vizinho diretamente pelo que se precisa, deixando ele delegar internamente: `pedido.getCidadeDoCliente()` (o método existe em `Pedido` e delega para `cliente.getCidade()` internamente, que por sua vez pode delegar para `endereco`). Quem chama só conhece `Pedido`.
- Melhor ainda quando aplicável: em vez de só mover o "get" para mais perto, mover a **operação** para perto do dado (ver `tell-dont-ask.md`) — se o objetivo do encadeamento era pegar um dado para tomar uma decisão, considere se a decisão inteira não deveria virar um método do objeto mais próximo do dado.

## Relação com Tell Don't Ask e acoplamento

Law of Demeter e Tell Don't Ask atacam o mesmo problema de ângulos complementares: Tell Don't Ask diz "não pergunte estado para decidir de fora, mande fazer"; Law of Demeter diz "mesmo quando perguntar for legítimo, não atravesse a estrutura interna de outros objetos para isso". Um código que viola Law of Demeter quase sempre está a um passo de violar Tell Don't Ask também — o encadeamento geralmente existe porque quem chamou está buscando um dado para tomar uma decisão que poderia ser delegada.

O ganho de seguir a lei é reduzir o *raio de blast* de mudanças estruturais: uma mudança na forma como `Cliente` guarda endereço afeta só `Cliente` (e talvez `Pedido`, se ele delega), não qualquer lugar do sistema que tenha feito o encadeamento completo.

## Quando não vale a pena forçar

- DTOs e objetos de transporte de dado puro (sem invariante de negócio) não precisam seguir isso à risca — encadear acesso em uma estrutura de dado imutável e sem comportamento (ex.: JSON desserializado, view model) é aceitável, porque não há encapsulamento de regra de negócio para proteger ali.
- Aplicar a lei rigidamente a todo getter em todo contexto pode gerar uma explosão de métodos de delegação triviais sem ganho real — o critério é se a cadeia atravessa fronteiras de domínio/agregado que deveriam estar protegidas, não qualquer acesso a dado aninhado.

## Perguntas para levantar contexto

- Esse encadeamento atravessa objetos com regra de negócio própria (agregados, entidades), ou é acesso a uma estrutura de dado simples sem invariante a proteger?
- Se a estrutura interna de um dos objetos no meio da cadeia mudasse, quantos lugares do código seriam afetados por causa desse tipo de encadeamento?
