# Strategy

Encapsula uma família de algoritmos intercambiáveis atrás de uma interface comum, permitindo trocar o algoritmo usado em tempo de execução sem alterar o código que o utiliza.

## O que procurar no código

- `if/else` ou `switch` selecionando entre variações de um mesmo cálculo/algoritmo (formas de calcular frete, de aplicar desconto, de validar dado, de ordenar).
- A mesma condicional de seleção de algoritmo aparece repetida em vários pontos do código.
- Necessidade de adicionar uma nova variação do algoritmo sem editar a classe que já existe (violação aberta a extensão sempre que uma nova variante aparece).

## Estrutura essencial

- **Estratégia**: interface comum a todas as variações (`calcular(entrada)`).
- **Estratégias concretas**: uma classe/função por variação.
- **Contexto**: mantém referência à estratégia atual (injetada ou configurável) e delega a ela, sem saber qual variação está em uso.

```
interface CalculoFrete: metodo calcular(pedido)
classe Contexto:
  interno: CalculoFrete estrategia
  metodo processar(pedido): retorna estrategia.calcular(pedido)
```

## Quando NÃO usar / armadilha comum

Se existe só uma variação do algoritmo hoje e nenhuma real no horizonte, Strategy é uma interface e uma classe extra para nada — um método simples resolve. A armadilha comum é confundir "parâmetro que muda o comportamento" com "estratégia": se as variações são só valores diferentes de configuração (não lógica diferente), um parâmetro ou tabela de configuração é mais simples do que uma hierarquia de classes.

## Exemplo conhecido

Comparadores/`Comparator` customizados passados para funções de ordenação (`sort(lista, comparador)`) são Strategy: o algoritmo de ordenação é fixo, mas o critério de comparação é intercambiável.
