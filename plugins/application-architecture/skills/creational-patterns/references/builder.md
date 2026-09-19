# Builder

Separa a construção de um objeto complexo, feita em múltiplos passos, da sua representação final — permitindo montar o mesmo tipo de objeto de formas diferentes com o código de construção legível.

## O que procurar no código

- Construtor com muitos parâmetros, vários deles opcionais (telescoping constructor: `new Pedido(a, b, null, null, true, null, 5)`).
- Múltiplas sobrecargas de construtor cobrindo combinações diferentes de parâmetros.
- Objeto que precisa de validação ou passos condicionais durante a montagem, antes de estar em estado válido.
- Código que monta o mesmo tipo de objeto de formas ligeiramente diferentes em vários lugares, duplicando a lógica de montagem.

## Estrutura essencial

- **Builder**: expõe métodos encadeáveis para configurar cada parte (`comNome(x).comEndereco(y)`), e um método final (`construir()`) que retorna o objeto pronto e validado.
- **Produto**: o objeto complexo resultante, idealmente imutável após construído.
- Opcional: um **Director** que encapsula receitas comuns de construção, quando há sequências reutilizáveis.

```
Pedido.builder()
  .comCliente(c)
  .comItem(i1).comItem(i2)
  .comDesconto(d)
  .construir()  // valida e retorna Pedido imutável
```

## Quando NÃO usar / armadilha comum

Se o objeto tem poucos campos (2-3) e a maioria é obrigatória, um construtor direto ou parâmetros nomeados da linguagem já resolvem — Builder aqui é ritual sem ganho. A armadilha comum é usar Builder só para "parecer mais limpo" em objetos simples, ou deixar o Builder mutável exposto sem nunca validar nada no `construir()`, perdendo a única vantagem real do padrão (garantir objeto válido no final).

## Exemplo conhecido

`StringBuilder`/`StringBuffer` em Java, ou builders de requisição HTTP (`HttpRequest.newBuilder().uri(...).header(...).build()`) — montagem incremental de um objeto que seria inviável de expressar num único construtor.
