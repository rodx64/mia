---
name: structural-patterns
description: Use esta skill ao discutir como classes e objetos se combinam para formar estruturas maiores — gatilhos como "como eu adapto essa interface incompatível", "quero adicionar comportamento sem herdar", "essa API tem interface complexa demais para o cliente", "como represento uma árvore de partes e todo", "preciso controlar acesso a esse objeto", "como desacoplo essa abstração da implementação", "esse código repete muitos objetos parecidos e tá pesado", ou ao revisar código com herança excessiva, wrappers ad-hoc, ou fachadas que viraram deus-objetos. Cobre os 7 padrões estruturais do GoF (Adapter, Decorator, Facade, Composite, Proxy, Bridge, Flyweight) tanto para revisão de código existente quanto para decisão durante o desenvolvimento.
version: 0.1.0
---

# Padrões estruturais (GoF)

Padrões que resolvem **como classes e objetos se compõem** para formar estruturas maiores, mantendo o sistema flexível e eficiente sem depender de herança rígida. Usada tanto para revisar código existente (identificar onde a composição está confusa, acoplada ou reimplementando um padrão conhecido de forma pior) quanto para decidir, durante o desenvolvimento, qual padrão estrutural se encaixa num problema descrito.

## Quando usar

- Revisão de código com integrações a APIs/bibliotecas externas com interfaces incompatíveis entre si.
- Necessidade de adicionar comportamento a um objeto sem alterar sua classe nem criar uma explosão de subclasses.
- Uma API ou subsistema ficou complexo demais para o cliente típico usar diretamente.
- Estrutura de dados recursiva de partes e todo (árvore de categorias, menu, sistema de arquivos).
- Necessidade de controlar, adiar ou interceptar acesso a um objeto (permissão, cache, lazy loading, chamada remota).
- Uma abstração precisa variar independentemente de sua implementação (ex.: mesmo controle de UI rodando sobre motores de renderização diferentes).
- Muitos objetos quase idênticos consumindo memória por dado redundante.

**Não usar para**: introduzir uma camada de composição onde herança simples ou uma função já resolvem. Facade para uma classe só, Adapter entre duas interfaces que já são idênticas, ou Proxy sem nenhuma lógica de controle real são sinais de padrão aplicado por aplicar.

## Processo

1. **Entenda a estrutura real do problema.** Existem interfaces incompatíveis que precisam conversar? Comportamento precisa ser combinável em tempo de execução? Existe uma relação parte-todo recursiva? Existe necessidade de indireção controlada (acesso, custo, localização) entre cliente e objeto real?

2. **Identifique sintomas e mapeie para o padrão candidato**, usando os arquivos em `references/` (carregue só os relevantes ao caso):

   | Sintoma no código | Padrão | Referência |
   |---|---|---|
   | Duas interfaces incompatíveis que precisam interoperar sem alterar nenhuma das duas | Adapter | `references/adapter.md` |
   | Precisa adicionar responsabilidades a um objeto dinamicamente, e subclassing explodiria combinatorialmente | Decorator | `references/decorator.md` |
   | Subsistema com muitas classes/chamadas que o cliente não deveria precisar conhecer | Facade | `references/facade.md` |
   | Estrutura recursiva de partes e todo tratada de forma uniforme | Composite | `references/composite.md` |
   | Precisa controlar/adiar/interceptar acesso a um objeto real (permissão, cache, remoto, lazy) | Proxy | `references/proxy.md` |
   | Abstração e implementação variando em eixos independentes, evitando explosão de subclasses | Bridge | `references/bridge.md` |
   | Muitas instâncias quase idênticas, custo de memória alto por dado redundante | Flyweight | `references/flyweight.md` |

3. **Avalie custo-benefício antes de recomendar.** Composição extra tem custo de leitura e indireção. Pergunte: esse desacoplamento vai ser exercitado de verdade (múltiplas implementações reais, combinações reais em runtime), ou é generalização especulativa para um caso hipotético que talvez nunca apareça?

4. **Produza a recomendação**: padrão sugerido (ou "composição/herança direta resolve"), por quê, e o ponto exato no código onde aplicar — ou, em revisão, onde uma camada estrutural existente pode ser removida sem perda.

## Notas

- Um padrão estrutural é meio, não fim. O critério é sempre: essa indireção resolve um problema real de acoplamento/composição que já existe, não um que pode existir um dia.
- Confundir Adapter com Facade, ou Decorator com Proxy, é comum — a diferença normalmente está na intenção (compatibilizar vs. simplificar; adicionar comportamento vs. controlar acesso), não na forma do código, que pode ser quase idêntica.
- Em revisão, sinalizar tanto a falta de um padrão estrutural que resolveria duplicação real quanto o uso de um padrão estrutural onde uma chamada direta bastaria.
