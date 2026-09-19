# Decorator

Adiciona responsabilidades a um objeto dinamicamente, envolvendo-o em um ou mais wrappers que implementam a mesma interface, sem alterar sua classe nem gerar subclasses para cada combinação.

## O que procurar no código

- Explosão combinatória de subclasses para cobrir combinações de comportamento (`CafeComLeite`, `CafeComLeiteEAcucar`, `CafeComLeiteSemAcucarComChantilly`...).
- Necessidade de compor comportamentos opcionais em runtime (logging, cache, retry, compressão) ao redor de um objeto que implementa uma interface comum.
- Middleware/pipeline onde cada camada envolve a próxima, todas respeitando o mesmo contrato.

## Estrutura essencial

- **Componente**: interface comum entre o objeto original e os decoradores.
- **Componente concreto**: implementação base.
- **Decorator**: implementa a mesma interface, mantém referência a um Componente interno, delega a chamada acrescentando comportamento antes/depois.

```
classe ComRetry implementa Servico:
  interno: Servico delegado
  metodo executar():
    tenta delegado.executar() ate N vezes
```

## Quando NÃO usar / armadilha comum

Se as combinações de comportamento são poucas e fixas (não crescem), uma única classe com flags/parâmetros é mais simples de ler do que uma pilha de decoradores encadeados. A armadilha comum é empilhar decoradores até o ponto em que rastrear o comportamento final exige seguir uma cadeia longa de wrappers — isso piora a legibilidade que o padrão deveria melhorar; se a ordem dos decoradores importa e não é óbvia, é sinal de que a composição virou complexa demais para o problema.

## Exemplo conhecido

Streams de I/O em Java (`BufferedInputStream(GZIPInputStream(FileInputStream(...)))`) e middlewares HTTP (cada middleware envolve o próximo handler) são Decorator clássico.
