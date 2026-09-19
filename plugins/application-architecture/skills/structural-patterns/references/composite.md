# Composite

Trata objetos individuais e composições de objetos de forma uniforme, permitindo montar estruturas de árvore (partes e todo) onde o cliente não precisa distinguir folha de nó composto.

## O que procurar no código

- Estrutura recursiva natural no domínio: árvore de categorias, menu com submenus, sistema de arquivos (pasta/arquivo), estrutura organizacional, árvore de componentes de UI.
- Código cliente cheio de checagens `if (é folha) ... else (é composto, percorrer filhos) ...` para tratar o mesmo tipo de operação.
- Operações que precisam ser aplicadas tanto a um item individual quanto a um grupo dele, com o mesmo resultado esperado (somar preço de item ou de categoria inteira).

## Estrutura essencial

- **Componente**: interface comum entre folha e composto (ex.: `calcularPreco()`).
- **Folha**: implementação sem filhos.
- **Composto**: mantém lista de Componentes filhos (podendo ser folhas ou outros compostos) e implementa a operação agregando o resultado dos filhos.

```
interface Item: metodo preco()
classe Produto implementa Item: preco() -> valor fixo
classe Categoria implementa Item:
  filhos: lista<Item>
  preco() -> soma(f.preco() para f em filhos)
```

## Quando NÃO usar / armadilha comum

Se a estrutura do domínio não é recursiva de verdade (não há aninhamento de nível arbitrário), Composite é generalização desnecessária — uma lista simples resolve. A armadilha comum é forçar folha e composto a compartilharem métodos que só fazem sentido para um dos dois (ex.: `adicionarFilho()` numa folha, que precisa lançar erro ou ser vazio) — isso quebra a uniformidade que é a própria razão de ser do padrão; se isso está acontecendo, vale reconsiderar a interface comum.

## Exemplo conhecido

DOM do navegador (`Node` com `Element` e `Text`, onde `Element` pode ter filhos `Node`) e árvores de arquivos/pastas em qualquer sistema operacional são Composite por definição.
