# A regra de dependência

Dependências de código sempre apontam para dentro: infraestrutura depende de aplicação, aplicação depende de domínio, e o domínio não depende de nada externo a ele. Isso não é sobre fluxo de dados ou de chamadas em tempo de execução (que podem ir nas duas direções) — é sobre a direção do `import`/`require`/referência de tipo no código-fonte.

## Inversão de dependência como mecanismo

O domínio não pode simplesmente "não depender" de um banco de dados se ele precisa persistir algo — a forma de resolver isso é o domínio definir uma interface (porta) e a infraestrutura implementá-la (adapter). O `import` da interface fica no domínio; o `import` da implementação concreta (driver, SDK) fica só no adapter. Em tempo de execução, alguém (container de injeção de dependência, factory, composition root) monta a ligação entre os dois — e esse "alguém" fica na camada mais externa, nunca dentro do domínio.

## Como isso aparece concretamente em imports/módulos

- Domínio: só importa tipos primitivos, outras classes de domínio, e interfaces definidas no próprio domínio. Zero import de pacote de banco, framework web, biblioteca de HTTP, SDK de nuvem.
- Aplicação (casos de uso): importa domínio e as interfaces (ports) que declara precisar. Não importa implementação concreta de adapter.
- Infraestrutura/adapters: importa aplicação e domínio (para implementar as interfaces) e as bibliotecas externas (driver de banco, cliente HTTP). É a única camada que pode importar essas bibliotecas.
- Em monorepo ou módulos separados, isso pode (e idealmente deve) ser reforçado por regra de build/lint (ex.: `dependency-cruiser`, módulos Java/Kotlin separados, ArchUnit) e não só por convenção.

## Sinais de violação

- `import` de driver de banco (`pg`, `mongoose`, `JPA`/`Hibernate` annotations), framework web (`express.Request`, `@RestController` em classe de domínio) ou SDK de nuvem dentro de arquivo de domínio.
- Caso de uso que instancia diretamente uma implementação concreta (`new PostgresPedidoRepository()`) em vez de receber a interface injetada.
- Regra de negócio (cálculo, validação, decisão) implementada dentro do adapter porque "é mais fácil ali perto do dado" — a dependência de código pode estar correta e ainda assim a regra estar no lugar errado.
- Enum ou constante de domínio derivada de um valor específico de uma tabela/coluna externa, acoplando o modelo de domínio ao schema físico.
- Testes de domínio que precisam subir banco, servidor HTTP ou mock de infraestrutura pesada para rodar — se o domínio dependesse só dele mesmo, o teste unitário seria isolado por natureza.

## Como corrigir

- Extrair a interface no domínio/aplicação, mover a implementação concreta para um adapter, injetar via composition root.
- Se uma "regra de negócio" está no adapter, mover a decisão para o domínio e deixar o adapter só traduzir dado (nenhuma lógica de decisão, só mapeamento).
- Usar um teste de arquitetura automatizado quando o time for grande o suficiente para a convenção começar a vazar sem querer.

## Perguntas para levantar contexto

- O domínio tem algum teste unitário que roda sem subir infraestrutura (banco, HTTP, fila)? Se não, é sinal de acoplamento na direção errada.
- Existe alguma ferramenta de lint/arquitetura garantindo essa direção, ou é só convenção de time?
- Quando alguém precisou trocar um banco ou biblioteca no passado, quanto código de domínio precisou mudar?
