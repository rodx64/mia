---
name: creational-patterns
description: Use esta skill ao discutir como objetos são criados no código — gatilhos como "como eu crio instâncias disso de forma flexível", "esse construtor tá com parâmetros demais", "preciso de uma família de objetos relacionados", "esse singleton tá causando problema", "qual padrão de criação usar aqui", "revisar como esse módulo instancia dependências", ou ao revisar código com muitos `new`/construtores espalhados, factories ad-hoc, ou estado global disfarçado de instância única. Cobre os 5 padrões de criação do GoF (Factory Method, Abstract Factory, Builder, Singleton, Prototype) tanto para revisão de código existente quanto para decisão durante o desenvolvimento.
version: 0.1.0
---

# Padrões de criação (GoF)

Padrões que resolvem **como e onde objetos são instanciados**, desacoplando quem precisa de um objeto de como esse objeto é construído. Usada tanto para revisar código existente (identificar onde a criação de objetos está confusa, rígida ou acoplada demais) quanto para decidir, durante o desenvolvimento, qual padrão de criação se encaixa num problema descrito.

## Quando usar

- Revisão de código onde a lógica de instanciação está espalhada, duplicada, ou acoplada a classes concretas.
- Construtores com muitos parâmetros opcionais, ou múltiplas sobrecargas de construtor.
- Necessidade de trocar a implementação concreta de um objeto sem alterar quem o usa (ex.: trocar provedor de pagamento, driver de banco, storage).
- Dúvida sobre "como eu deveria instanciar isso" durante o design de uma feature nova.
- Suspeita de que um Singleton está sendo usado como atalho para estado global.

**Não usar para**: forçar um padrão de criação em código que já é simples e direto. Se `new MinhaClasse(x, y)` resolve e não há variação de implementação nem complexidade de montagem, não há problema de criação a resolver — introduzir uma factory aqui é complexidade sem benefício.

## Processo

1. **Entenda o problema real de criação.** Pergunte: existe mais de uma implementação possível para o mesmo papel? A construção do objeto é complexa (muitos passos, ordem importa, parâmetros opcionais)? Precisa de exatamente uma instância compartilhada, e por quê? A criação é cara (clonar é mais barato que recriar)?

2. **Identifique sintomas e mapeie para o padrão candidato**, usando os arquivos em `references/` (carregue só os relevantes ao caso):

   | Sintoma no código | Padrão | Referência |
   |---|---|---|
   | `if/switch` decidindo qual classe instanciar, repetido em vários lugares | Factory Method | `references/factory-method.md` |
   | Precisa criar famílias inteiras de objetos relacionados que devem ser consistentes entre si (ex.: componentes de UI por tema, drivers por plataforma) | Abstract Factory | `references/abstract-factory.md` |
   | Construtor com muitos parâmetros opcionais, ou objeto montado em múltiplos passos com validação intermediária | Builder | `references/builder.md` |
   | Necessidade real de exatamente uma instância global compartilhada (config, connection pool) | Singleton | `references/singleton.md` |
   | Criar objeto é caro/complexo e uma cópia de um exemplar existente resolve mais barato | Prototype | `references/prototype.md` |

3. **Avalie custo-benefício antes de recomendar.** Um padrão de criação adiciona uma camada de indireção. Pergunte: essa indireção paga o próprio custo aqui, ou o time vai manter uma factory para uma única implementação concreta que nunca muda? Overengineering nesta categoria é comum — a maioria dos problemas de "criação" se resolve com um construtor bem desenhado ou uma função fábrica simples, sem precisar do padrão nomeado formalmente.

4. **Produza a recomendação**: padrão sugerido (ou "nenhum, construção direta resolve"), por quê, e o ponto exato no código onde aplicar — ou, em revisão, onde um padrão foi aplicado sem necessidade e pode ser simplificado.

## Notas

- Um padrão de criação é meio, não fim. O objetivo é reduzir acoplamento e duplicação reais — se a mudança não reduz nenhum dos dois, é só indireção extra.
- Em revisão de código, sinalizar tanto a ausência de um padrão necessário quanto o uso de um padrão desnecessário (factory para uma única implementação, builder para um objeto de 2 campos obrigatórios) tem o mesmo valor.
- Singleton merece atenção redobrada: é o padrão de criação mais fácil de abusar como desculpa para estado global. Ver `references/singleton.md` antes de recomendá-lo.
