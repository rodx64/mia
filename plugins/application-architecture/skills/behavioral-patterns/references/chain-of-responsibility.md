# Chain of Responsibility

Passa uma requisição por uma cadeia de possíveis tratadores, onde cada um decide se trata a requisição ou a repassa ao próximo, sem o emissor saber qual tratador específico vai lidar com ela.

## O que procurar no código

- Sequência de validações/checagens onde cada uma pode "barrar" o fluxo ou deixar passar para a próxima, hoje implementada como uma cadeia de `if` que retorna cedo (early return) misturada com a lógica de negócio.
- Middlewares/interceptadores de requisição HTTP, cada um decidindo processar e/ou repassar adiante.
- Necessidade de adicionar, remover ou reordenar tratadores sem alterar o código que dispara a requisição original.

## Estrutura essencial

- **Handler**: interface com `tratar(requisicao)` e uma referência ao próximo handler da cadeia.
- **Handlers concretos**: cada um decide se processa a requisição, se repassa adiante, ou ambos.
- Quem monta a cadeia (ordem dos handlers) fica separado de quem dispara a requisição.

```
classe HandlerAutenticacao implementa Handler:
  metodo tratar(req):
    se nao autenticado(req): retorna erro
    retorna proximo.tratar(req)
```

## Quando NÃO usar / armadilha comum

Se a sequência de checagens é curta, fixa, e nunca precisa ser reconfigurada dinamicamente, uma função com `if`s sequenciais é mais direta de ler do que uma cadeia de objetos handler. A armadilha comum é uma cadeia onde nenhum handler trata a requisição e ela "cai no vazio" silenciosamente — sempre ter um handler final explícito (ou erro claro) para requisição não tratada por ninguém na cadeia.

## Exemplo conhecido

Pipelines de middleware em frameworks web (Express, ASP.NET, etc.) e cadeias de validação de formulário onde cada validador pode interromper o processamento e retornar um erro.
