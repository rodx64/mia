# Camadas concêntricas e regra de dependência

Clean Architecture (Robert C. Martin) organiza o sistema em círculos concêntricos. A regra é a mesma da arquitetura hexagonal, com outro vocabulário: **dependências de código só apontam para dentro**. Uma camada mais interna nunca conhece uma mais externa.

## As camadas, de dentro para fora

1. **Entities**: regras de negócio mais gerais e estáveis do domínio — objetos e regras que existiriam mesmo se o sistema fosse outro aplicativo totalmente diferente usando os mesmos conceitos de negócio.
2. **Use Cases**: regras de aplicação — orquestram entities para realizar uma operação específica do sistema (`ConfirmarPedido`, `CancelarAssinatura`). Conhecem entities, não conhecem UI nem banco.
3. **Interface Adapters**: convertem dado entre o formato conveniente para use cases/entities e o formato conveniente para agentes externos — controllers, presenters, gateways, mappers.
4. **Frameworks & Drivers**: a camada mais externa — banco de dados, framework web, UI, dispositivos. Detalhe, não decisão.

## Boundaries e interfaces entre camadas

- A comunicação entre camadas cruza fronteiras (boundaries) sempre via interface definida pela camada mais interna. Um use case que precisa persistir dado define uma interface (`OutputPort`/gateway); a camada de frameworks implementa essa interface — mesma inversão de dependência do hexagonal.
- Fluxo de controle pode ir de fora para dentro (uma request HTTP aciona um use case), mas a dependência de compilação/import da interface é sempre definida pela camada interna e implementada pela externa.

## DTOs cruzando fronteiras

- Dado que atravessa uma boundary deve ser a estrutura mais simples possível para aquele propósito — um DTO ou struct de dados puro, não uma entity, e não um objeto do framework externo (`Request` do Express, `ResultSet` do JDBC).
- Isso evita que uma mudança de formato de banco ou de payload HTTP force mudança em entity/use case, e evita que a entity vaze detalhes internos (como IDs técnicos ou campos de auditoria) para fora sem controle.
- Sinal de violação: use case retornando a própria entity de domínio direto para o controller serializar como resposta HTTP — funciona no começo, mas acopla o formato de resposta externa ao modelo interno.

## Sinais de violação a procurar em revisão

- Entity com dependência de biblioteca de ORM, framework ou serialização.
- Use case chamando diretamente uma classe concreta de infraestrutura em vez de uma interface.
- Lógica de decisão de negócio dentro de um controller ou dentro do código de acesso a dados.
- Objeto de request/response do framework web passado como parâmetro para dentro do use case.

## Perguntas para levantar contexto

- As regras de negócio centrais dependeriam de reescrever algo se o framework web ou o banco fossem trocados amanhã?
- Existe uma interface clara entre "o que o use case precisa" e "como isso é implementado", ou o use case já assume a tecnologia concreta?
- O que cruza a fronteira entre controller e use case: um DTO simples ou um objeto de infraestrutura?
