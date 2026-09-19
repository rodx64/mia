---
name: behavioral-patterns
description: Use esta skill ao discutir como objetos se comunicam, distribuem responsabilidade ou variam comportamento em tempo de execução — gatilhos como "como eu troco esse algoritmo em runtime", "esse if/switch de estado tá gigante", "preciso notificar vários observadores de um evento", "como desfaço essa ação", "esses objetos estão acoplados demais entre si", "como percorro essa estrutura sem expor a implementação", "preciso desfazer/refazer uma ação", "esse método template repete passos entre subclasses", ou ao revisar código com cadeias de `if/else`/`switch` controlando comportamento, objetos que conhecem demais uns dos outros, ou lógica de negócio duplicada entre variações de um mesmo fluxo. Cobre os 11 padrões comportamentais do GoF (Strategy, Observer, Command, Template Method, State, Chain of Responsibility, Mediator, Visitor, Iterator, Memento, Interpreter) tanto para revisão de código existente quanto para decisão durante o desenvolvimento.
version: 0.1.0
---

# Padrões comportamentais (GoF)

Padrões que resolvem **como responsabilidade e comunicação são distribuídas entre objetos**: variação de algoritmo, notificação de eventos, encapsulamento de ações, transição de estado, e navegação de estruturas. Usada tanto para revisar código existente (identificar onde a comunicação entre objetos está acoplada, duplicada ou reimplementando mal um padrão conhecido) quanto para decidir, durante o desenvolvimento, qual padrão comportamental se encaixa num problema descrito.

## Quando usar

- Revisão de código com `if/else` ou `switch` grandes decidindo comportamento ou controlando transições de estado.
- Necessidade de trocar um algoritmo em tempo de execução sem alterar quem o usa.
- Um objeto precisa notificar vários outros sobre uma mudança, sem conhecê-los diretamente.
- Ações do usuário (ou do sistema) precisam ser enfileiradas, logadas, desfeitas ou refeitas.
- Comportamento de um objeto muda conforme seu estado interno, e isso vira condicional espalhada.
- Uma requisição pode ser tratada por mais de um handler possível, em ordem.
- Muitos objetos se comunicando diretamente entre si de forma emaranhada (many-to-many).
- Necessidade de aplicar uma operação nova sobre uma hierarquia de tipos sem poluir as classes com esse comportamento.
- Percorrer uma coleção sem expor sua estrutura interna.
- Necessidade de salvar/restaurar o estado interno de um objeto sem violar seu encapsulamento.
- Um mini-linguagem ou conjunto de regras precisa ser interpretado/avaliado.

**Não usar para**: introduzir uma máquina de estados formal, um barramento de eventos, ou uma hierarquia de comandos onde uma função simples ou um `if` isolado já resolvem. A maioria dos padrões comportamentais existe para lidar com *variação* e *crescimento* — se não há variação real nem previsão razoável dela, o padrão só adiciona indireção.

## Processo

1. **Entenda o problema de comunicação/comportamento real.** Existe variação de algoritmo que muda por contexto? Existe um evento que múltiplas partes precisam saber? Uma ação precisa ser tratada como dado (enfileirada, logada, desfeita)? O comportamento depende do estado interno do objeto? Uma requisição percorre uma cadeia de possíveis tratadores?

2. **Identifique sintomas e mapeie para o padrão candidato**, usando os arquivos em `references/` (carregue só os relevantes ao caso):

   | Sintoma no código | Padrão | Referência |
   |---|---|---|
   | `if/switch` selecionando entre variações de um mesmo algoritmo | Strategy | `references/strategy.md` |
   | Um objeto precisa notificar N dependentes sobre uma mudança, sem acoplar-se a eles | Observer | `references/observer.md` |
   | Uma ação precisa virar objeto: enfileirar, logar, desfazer/refazer | Command | `references/command.md` |
   | Subclasses repetem o esqueleto de um algoritmo, variando só alguns passos | Template Method | `references/template-method.md` |
   | Comportamento do objeto muda conforme estado interno, viraria `switch` sobre estado | State | `references/state.md` |
   | Requisição pode ser tratada por um entre vários handlers possíveis, em cadeia | Chain of Responsibility | `references/chain-of-responsibility.md` |
   | Muitos objetos referenciando uns aos outros diretamente (emaranhado many-to-many) | Mediator | `references/mediator.md` |
   | Precisa aplicar operação nova sobre hierarquia de tipos sem alterar as classes | Visitor | `references/visitor.md` |
   | Precisa percorrer uma coleção sem expor sua estrutura interna | Iterator | `references/iterator.md` |
   | Precisa capturar/restaurar estado interno de um objeto sem quebrar encapsulamento | Memento | `references/memento.md` |
   | Precisa interpretar/avaliar expressões de uma gramática simples e estável | Interpreter | `references/interpreter.md` |

3. **Avalie custo-benefício antes de recomendar.** Padrões comportamentais tendem a introduzir mais classes/interfaces do que os outros dois grupos. Pergunte: a variação é real e vai crescer, ou é um caso único disfarçado de "genérico"? Um Observer para um único ouvinte, um State machine para dois estados, ou um Command para uma ação que nunca precisa ser desfeita são sinais de over-engineering.

4. **Produza a recomendação**: padrão sugerido (ou "condicional/chamada direta resolve"), por quê, e o ponto exato no código onde aplicar — ou, em revisão, onde um padrão comportamental existente pode ser simplificado para código mais direto.

## Notas

- Um padrão comportamental é meio, não fim. O critério é sempre: essa estrutura reduz acoplamento/duplicação que já dói hoje, não uma dor hipotética futura.
- É a categoria com mais sobreposição aparente (Strategy vs. State, Command vs. Chain of Responsibility, Mediator vs. Observer) — a diferença geralmente está na intenção e no ciclo de vida do objeto, não na sintaxe.
- Em revisão, sinalizar tanto ausência de padrão onde a condicional já ficou insustentável quanto presença de padrão onde a variação nunca se materializou.
