# Arquitetura orientada a eventos

Componentes se comunicam publicando e reagindo a eventos (fatos que aconteceram) em vez de chamarem uns aos outros diretamente. Produtores não sabem quem consome; consumidores reagem de forma assíncrona, no seu próprio tempo.

## Peças

- **Produtor**: publica um evento quando algo relevante acontece no seu domínio (`PedidoConfirmado`, `EstoqueReservado`). Não sabe (nem deveria saber) quem vai consumir.
- **Consumidor**: reage a um evento publicado, executando sua própria lógica. Pode, por sua vez, produzir novos eventos.
- **Event bus/broker** (Kafka, RabbitMQ, SNS/SQS, EventBridge): a infraestrutura de transporte. Garante entrega (com graus variados de garantia — at-least-once é o mais comum), ordenação (às vezes, dentro de uma partição/tópico) e desacoplamento físico entre produtor e consumidor.
- **Acoplamento fraco temporal**: produtor e consumidor não precisam estar no ar ao mesmo tempo — o broker retém o evento até o consumidor processar. Isso é diferente de desacoplamento de contrato (o schema do evento ainda é um contrato forte).

## Domain event vs. integration event

- **Domain event**: interno a um bounded context, geralmente in-process (ex.: dentro do mesmo aggregate/módulo), granular, pode carregar referências internas de domínio.
- **Integration event**: cruza fronteira de serviço/bounded context, publicado no broker, é contrato público entre times/sistemas — precisa ser versionado e mais estável, carrega dado serializável e autocontido (não referências internas).
- Erro comum: publicar o domain event bruto como integration event, vazando estrutura interna do domínio para consumidores externos e acoplando-os a uma implementação que deveria ser livre para mudar.

## Armadilhas comuns

- **Eventual consistency mal comunicada**: usuário/outro time assume que o efeito de um evento é imediato; sem SLA claro de "quanto tempo até consistente", cria bugs percebidos como "dado sumiu" ou "dado errado" quando na verdade é apenas atraso de propagação.
- **Acoplamento oculto via schema de evento**: mesmo sem chamada direta, dois serviços ficam fortemente acoplados ao formato exato do evento — mudar um campo quebra consumidores silenciosamente, sem o compilador ou o teste de integração direto acusando isso. Precisa de contrato versionado (schema registry, versionamento explícito no payload) e política de compatibilidade (aditiva, nunca quebra consumidor existente sem migração).
- **Falta de idempotência no consumidor**: brokers em geral entregam at-least-once — o mesmo evento pode chegar duas vezes. Consumidor que não é idempotente (ex.: debita saldo duas vezes ao reprocessar) quebra em produção sob retry ou reprocessamento.
- **Ordem assumida incorretamente**: sistemas assumem que eventos chegam na ordem em que foram publicados; sem partição/chave de ordenação garantida isso não é verdade entre tópicos/partições diferentes.
- **Evento como RPC disfarçado**: usar evento para pedir uma resposta síncrona (publicar e esperar reação imediata) é forçar um padrão request/response dentro de uma ferramenta pensada para desacoplamento — geralmente sinal de que deveria ser uma chamada direta.

## Como decidir se vale a pena

- Vale quando há múltiplos consumidores reais (hoje ou claramente previstos) reagindo ao mesmo fato, ou quando o produtor não deve ficar bloqueado esperando o processamento do efeito.
- Não vale só para "desacoplar" um único produtor de um único consumidor síncrono — isso é complexidade de infraestrutura (broker, monitoramento de fila, DLQ) sem ganho real; uma chamada direta (ou um evento in-process) resolve.

## Perguntas para levantar contexto

- Quantos consumidores reais existem ou são esperados para cada evento? Um só consumidor de fato precisa de evento assíncrono, ou uma chamada direta resolveria?
- O consumidor é idempotente? O que acontece se o mesmo evento for processado duas vezes?
- Existe contrato/schema versionado para os integration events, ou o formato é o objeto interno do produtor exposto direto?
