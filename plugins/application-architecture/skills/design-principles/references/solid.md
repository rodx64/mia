# SOLID

Cinco princípios de design orientado a objetos, cada um com um tipo específico de acoplamento/fragilidade que ele evita. Úteis tanto para revisar código existente quanto para decidir como estruturar algo novo — mas nenhum deles é um fim em si mesmo; aplicar um princípio sem o problema que ele resolve é over-engineering (ver `kiss.md`).

## S — Single Responsibility Principle

Uma classe/módulo deve ter um único motivo para mudar.

- **Sinal de violação**: uma classe que muda por razões de negócio completamente diferentes — ex.: `Pedido` que tem lógica de cálculo de desconto, lógica de formatação de e-mail de confirmação, e lógica de persistência, todas no mesmo lugar. Mudar a política de e-mail não deveria arriscar quebrar o cálculo de desconto.
- **Como corrigir**: separar em classes com um eixo de mudança cada (`CalculadoraDeDesconto`, `NotificadorDePedido`, `PedidoRepository`), cada uma dependendo do que precisa de `Pedido` sem concentrar tudo nele.
- Não confundir com "uma classe só pode ter um método" — responsabilidade é sobre motivo de mudança, não sobre tamanho.

## O — Open/Closed Principle

Aberto para extensão, fechado para modificação: deve ser possível adicionar comportamento novo sem alterar código existente que já funciona e está testado.

- **Sinal de violação**: um `switch`/`if-else` gigante sobre um tipo (`if (tipo == "cartao") ... else if (tipo == "boleto") ...`) que precisa ganhar mais um `else if` toda vez que um tipo novo aparece, espalhado por vários arquivos.
- **Como corrigir**: extrair uma interface/abstração comum (`FormaDePagamento` com método `processar()`) e cada tipo novo vira uma implementação nova, sem tocar no código que já orquestra os pagamentos existentes.
- Cuidado para não aplicar isso preventivamente sem ter pelo menos duas variações reais — ver KISS/YAGNI.

## L — Liskov Substitution Principle

Um subtipo deve poder substituir seu supertipo em qualquer lugar sem quebrar o comportamento esperado por quem usa a interface.

- **Sinal de violação**: subclasse que lança exceção em um método que a classe base implementa normalmente, ou que enfraquece uma pré-condição/fortalece uma pós-condição de forma que o código cliente escrito contra a classe base quebra (`Quadrado extends Retangulo` que faz `setLargura` também mudar a altura, surpreendendo código que assume independência entre os dois).
- **Como corrigir**: se o subtipo não consegue cumprir o contrato completo do supertipo, a relação de herança está errada — preferir composição, ou reformular a hierarquia para que a interface comum seja só o que todos os tipos realmente cumprem.

## I — Interface Segregation Principle

Nenhum cliente deve ser forçado a depender de métodos que não usa.

- **Sinal de violação**: uma interface grande (`RepositorioDeUsuario` com 15 métodos) onde a maioria das implementações/clientes só usa 2 ou 3, e implementações concretas precisam lançar `NotImplementedException` para os métodos que não fazem sentido para elas.
- **Como corrigir**: quebrar a interface grande em interfaces menores e coesas por papel (`LeitorDeUsuario`, `EscritorDeUsuario`), cada cliente dependendo só da que precisa.

## D — Dependency Inversion Principle

Módulos de alto nível não devem depender de módulos de baixo nível — ambos devem depender de abstrações. É o princípio que sustenta toda a arquitetura hexagonal/clean (ver `hexagonal-architecture/references/dependency-rule.md`).

- **Sinal de violação**: um caso de uso instanciando diretamente uma classe concreta de infraestrutura (`new PostgresRepositorio()`) em vez de receber uma interface injetada.
- **Como corrigir**: definir a interface no módulo de alto nível (domínio/aplicação), implementar no módulo de baixo nível (infraestrutura), e injetar a dependência (construtor, DI container) em vez de instanciar diretamente.

## Perguntas para levantar contexto

- Qual desses cinco sinais aparece com mais frequência no código em revisão? Geralmente vale focar em um ou dois, não tentar aplicar os cinco de uma vez sobre o mesmo trecho.
- A violação encontrada já causou dor real (bug, dificuldade de teste, medo de mexer no código), ou é uma violação teórica sem custo prático ainda?
