# Ports & Adapters

O núcleo (domínio + aplicação) define **ports**: interfaces que expressam o que ele precisa do mundo externo ou o que oferece a ele. **Adapters** são as implementações concretas dessas interfaces, vivendo fora do núcleo. A regra chave: quem define o contrato é sempre o domínio/aplicação, nunca a infraestrutura.

## Portas de entrada (driving/primary) vs. saída (driven/secondary)

- **Porta de entrada (driving)**: como o mundo externo aciona a aplicação. Ex.: `CriarPedidoUseCase` — uma interface que um controller HTTP, um handler de fila ou um comando de CLI chamam. O adapter de entrada (controller, consumer) traduz o protocolo externo (HTTP, mensagens) para uma chamada nessa porta.
- **Porta de saída (driven)**: o que a aplicação precisa do mundo externo para funcionar. Ex.: `PedidoRepository`, `NotificadorDePagamento`. O adapter de saída (implementação com Postgres, cliente HTTP de um gateway de pagamento) implementa essa interface.
- Direção do fluxo de controle e de dependência: em ambos os casos, o adapter depende da porta (interface), nunca o contrário. A diferença entre "entrada" e "saída" é só quem inicia a chamada — o núcleo continua sem saber o que há do outro lado.

## Sinais de vazamento de infraestrutura para dentro do domínio

- Entidade de domínio com anotação de ORM (`@Entity`, `@Column`) ou herdando de classe base de framework.
- Caso de uso recebendo `HttpRequest`, `ResultSet` ou objeto de biblioteca de fila diretamente como parâmetro, em vez de um DTO/comando próprio da aplicação.
- Regra de negócio dentro do controller ou do repository (ex.: validação de desconto máximo dentro do handler HTTP) — a porta virou lugar de lógica, não só de tradução.
- Domínio importando um cliente de SDK de nuvem, biblioteca de e-mail ou driver de banco para "só usar um tipo" — qualquer import de biblioteca de infraestrutura dentro da camada de domínio já é o sinal.
- Interface de porta desenhada em função do adapter existente (ex.: `RepositorioComQueryBuilder`) em vez de em função do que o domínio realmente precisa (ex.: `buscarPorId`, `salvar`).

## Como aplicar

- Nomeie a porta pelo que o domínio precisa, não pela tecnologia do adapter (`NotificadorDePagamento`, não `WebhookStripeClient`).
- Um adapter por tecnologia/integração; trocar Postgres por Mongo, ou Stripe por outro gateway, deve significar escrever um novo adapter, não tocar no domínio.
- DTOs/comandos simples cruzando a porta de entrada; nunca vazar o objeto de request do framework para dentro do caso de uso.
- Ao revisar: para cada porta, pergunte "essa interface faria sentido se eu trocasse o adapter concreto amanhã?". Se a resposta é não, a porta está vazando detalhe de implementação.

## Perguntas para levantar contexto

- Quais integrações externas esse sistema tem (banco, filas, APIs de terceiros, sistema de arquivos)? Cada uma tem uma porta correspondente ou o domínio fala direto com o SDK?
- As entidades de domínio têm alguma dependência de biblioteca externa (ORM, framework web, serialização)?
- Se a tecnologia de um adapter mudasse hoje, quantos arquivos fora do adapter precisariam mudar?
