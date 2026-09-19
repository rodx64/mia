# Facade

Fornece uma interface única e simplificada para um subsistema complexo composto por várias classes, sem esconder o subsistema para quem precisa de acesso mais fino.

## O que procurar no código

- Cliente precisa instanciar e coordenar várias classes de um subsistema, em uma ordem específica, só para realizar uma operação de alto nível ("processar pedido" = validar estoque + calcular frete + cobrar + emitir nota).
- Essa sequência de chamadas está duplicada em múltiplos pontos do código que usam o subsistema.
- Mudanças internas no subsistema quebram vários pontos do código cliente porque não há uma fronteira única entre os dois.

## Estrutura essencial

- **Facade**: expõe métodos de alto nível que internamente orquestram as classes do subsistema.
- **Subsistema**: as classes complexas por trás, que continuam acessíveis diretamente para quem precisa de controle fino — Facade é conveniência, não bloqueio de acesso.

```
classe FacadePedido:
  metodo finalizar(pedido):
    estoque.reservar(pedido)
    frete.calcular(pedido)
    pagamento.cobrar(pedido)
    notaFiscal.emitir(pedido)
```

## Quando NÃO usar / armadilha comum

Se o subsistema já é simples (uma ou duas classes, uma chamada), Facade é camada supérflua. A armadilha mais comum é a Facade virar um "deus-objeto": acumula tanta lógica de orquestração e regra de negócio que se torna o novo ponto de acoplamento total do sistema — nesse ponto ela deixou de simplificar e passou a esconder complexidade real que devia estar mais bem distribuída. Facade também não deveria ser o único ponto de acesso ao subsistema — se algum cliente precisa de uma chamada mais específica, ele deve poder acessar as classes internas diretamente.

## Exemplo conhecido

SDKs de nuvem que expõem um client de alto nível (`storage.upload(arquivo)`) escondendo autenticação, retry, multipart upload e chamadas HTTP internas de baixo nível.
