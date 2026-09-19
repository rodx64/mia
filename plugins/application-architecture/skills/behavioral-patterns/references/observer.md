# Observer

Define uma dependência um-para-muitos entre objetos, de forma que quando um objeto muda de estado, todos os seus dependentes são notificados automaticamente, sem que o emissor precise conhecê-los diretamente.

## O que procurar no código

- Um objeto precisa avisar vários outros sobre um evento (mudança de estado, dado novo, ação concluída), e hoje faz isso chamando cada um deles diretamente, acoplado a suas classes concretas.
- Adicionar um novo "interessado" no evento exige editar o objeto emissor para chamá-lo explicitamente.
- Callbacks/listeners acumulando-se de forma ad-hoc sem um contrato único de inscrição/notificação.

## Estrutura essencial

- **Subject/Observável**: mantém lista de observadores inscritos, expõe `inscrever()`/`desinscrever()`/`notificar()`.
- **Observer**: interface com um método de notificação (`atualizar(evento)`), implementado por cada interessado.
- Subject não conhece os tipos concretos dos observadores, só a interface.

```
classe PedidoObservavel:
  observadores: lista<Observer>
  metodo notificar(evento): para cada o em observadores: o.atualizar(evento)
```

## Quando NÃO usar / armadilha comum

Se existe só um "ouvinte" fixo e conhecido, uma chamada direta é mais simples e mais fácil de depurar do que a indireção de inscrição/notificação. A armadilha mais comum é perder o controle da ordem e dos efeitos colaterais entre observadores (um observer que lança exceção pode travar a notificação dos seguintes), e vazamento de memória por observadores que nunca se desinscrevem — isso é um problema real e recorrente, não teórico, em sistemas de UI e event emitters de longa duração.

## Exemplo conhecido

Event emitters (`addEventListener`, `EventEmitter.on`) e sistemas reativos de UI (framework que re-renderiza componentes quando um estado observado muda) são Observer por definição.
