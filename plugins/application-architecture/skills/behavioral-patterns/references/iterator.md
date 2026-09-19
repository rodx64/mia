# Iterator

Fornece uma forma de percorrer os elementos de uma coleção sequencialmente sem expor sua estrutura interna (array, lista encadeada, árvore, etc.).

## O que procurar no código

- Código cliente acessando diretamente a estrutura interna de uma coleção customizada (índices de array, ponteiros de nó) para percorrê-la, em vez de um contrato de "próximo elemento".
- Necessidade de suportar mais de uma forma de percorrer a mesma coleção (ordem normal, reversa, filtrada) sem duplicar a estrutura de dados.
- Coleção customizada (não nativa da linguagem) que precisa se comportar como as coleções nativas em laços `for`/`foreach`.

## Estrutura essencial

- **Iterador**: interface com `temProximo()` e `proximo()` (ou equivalente da linguagem, como protocolos de iteração nativos).
- **Coleção**: expõe um método para criar um iterador (`criarIterador()`), sem revelar sua estrutura interna.
- Múltiplos iteradores podem percorrer a mesma coleção simultaneamente e de forma independente.

```
interface Iterador<T>: metodo temProximo(); metodo proximo(): T
classe Colecao:
  metodo criarIterador(): retorna IteradorConcreto(este)
```

## Quando NÃO usar / armadilha comum

Na maioria das linguagens modernas, coleções nativas já implementam o protocolo de iteração embutido (`for...of`, `foreach`, generators/iteradores da linguagem) — implementar Iterator do zero só faz sentido para estruturas de dados customizadas com forma de percorrer não trivial. A armadilha comum é reinventar Iterator manualmente quando a linguagem já oferece geradores/protocolos de iteração nativos que resolvem o mesmo problema com muito menos código.

## Exemplo conhecido

Generators em Python/JavaScript (`yield`) e a interface `Iterable`/`Iterator` de Java são a materialização nativa deste padrão nas linguagens modernas.
