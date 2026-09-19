# Factory Method

Delega a decisão de qual classe concreta instanciar para subclasses ou para um método dedicado, em vez de espalhar `new ClasseConcreta()` pelo código que consome o objeto.

## O que procurar no código

- `if/else` ou `switch` repetido em múltiplos lugares decidindo qual classe concreta criar a partir de um tipo/string (`if (tipo == "pix") return new PagamentoPix()`).
- Código cliente que conhece e importa diretamente várias classes concretas de uma mesma família, quando só deveria depender da interface/abstração comum.
- Adicionar uma nova variante da família exige tocar em vários pontos do código que fazem essa escolha.

## Estrutura essencial

- **Produto**: interface/classe abstrata comum às variantes.
- **Produto concreto**: cada implementação específica.
- **Criador**: expõe um método de criação (`criar()`); pode ser abstrato (subclasses decidem) ou uma função/método concentrando o `switch` uma única vez.
- Cliente depende só do Criador e do Produto, nunca das classes concretas.

```
metodo criar(tipo):
  switch tipo: retorna ProdutoA() | ProdutoB() | ...
```

## Quando NÃO usar / armadilha comum

Se existe só uma implementação concreta hoje e não há sinal de que outra vai aparecer, uma factory é indireção sem propósito — instancie direto. O erro mais comum é criar uma `Factory` para "flexibilidade futura" que nunca se materializa, adicionando uma camada que todo mundo tem que aprender a navegar para nada. Vale a pena a partir de duas variantes reais coexistindo, ou quando a lógica de escolha é complexa o suficiente para merecer um lugar único.

## Exemplo conhecido

`document.createElement(tag)` no DOM decide a classe concreta do elemento (`HTMLDivElement`, `HTMLInputElement`, ...) a partir de uma string, sem o chamador precisar conhecer cada classe.
