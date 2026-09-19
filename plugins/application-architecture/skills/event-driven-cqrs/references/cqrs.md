# CQRS — Command Query Responsibility Segregation

Separar o modelo usado para escrever (comandos, que mudam estado) do modelo usado para ler (queries, que só retornam dado). Em vez de um único modelo/objeto servindo tanto para validar regras de negócio na escrita quanto para montar a resposta ideal de leitura, existem dois modelos, otimizados para propósitos diferentes.

## Quando vale a pena

- Vale quando leitura e escrita têm necessidades muito assimétricas: a escrita precisa de um modelo rico com invariantes e regras de negócio (aggregate, validações), e a leitura precisa de formatos variados, agregados, ou otimizados para performance (telas de dashboard, relatórios, listagens com múltiplos joins).
- Sinais de que o assimetria é real: queries de leitura constantemente fazendo joins complexos através de múltiplos aggregates só para montar uma view; o modelo de escrita ficando poluído com métodos/campos que só existem para servir alguma tela de leitura; necessidade de escalar leitura e escrita de forma independente (leitura com ordens de magnitude mais tráfego que escrita).
- **Não é default.** Para a maioria dos CRUDs, um único modelo servindo leitura e escrita é mais simples e suficiente. Introduzir CQRS sem essa assimetria real é complexidade acidental — mais código, mais lugares para manter sincronizados, sem ganho.

## CQRS simples vs. CQRS com stores separados

- **CQRS simples (mesmo banco)**: comandos e queries usam modelos/classes diferentes na aplicação, mas ambos leem/escrevem no mesmo banco de dados. Já dá o benefício de simplificar cada modelo para seu propósito, com custo de infraestrutura baixo — não precisa de sincronização entre stores.
- **CQRS com stores separados**: o lado de escrita persiste no seu banco (geralmente otimizado para consistência e regras transacionais); o lado de leitura é projetado para um ou mais stores de leitura (cache, banco desnormalizado, índice de busca), atualizados de forma assíncrona a partir de eventos gerados pela escrita.
- O segundo modelo introduz consistência eventual entre escrita e leitura — o dado lido pode estar momentaneamente desatualizado em relação à última escrita. Isso é uma decisão de produto, não só técnica: precisa ser explicitamente aceitável para o caso de uso (ex.: um contador de "curtidas" tolera atraso; um saldo bancário exibido pode não tolerar).

## Relação com Event Sourcing

- CQRS e Event Sourcing são conceitos **distintos e frequentemente confundidos**, mas nenhum exige o outro.
- CQRS é sobre separar modelo de leitura e escrita — não diz nada sobre como o estado de escrita é persistido.
- Event Sourcing é sobre persistir o estado como uma sequência de eventos (o log de tudo que aconteceu) em vez de persistir o estado atual diretamente — o estado atual é derivado reproduzindo os eventos.
- É comum combinar os dois (o lado de escrita usa event sourcing, e os eventos gerados alimentam os stores de leitura do CQRS), mas dá para ter CQRS sem event sourcing (escrita com persistência tradicional, leitura com modelo separado) e event sourcing sem CQRS (formalmente raro na prática, mas conceitualmente possível ter um único modelo de leitura direto sobre o event store).

## Armadilhas comuns

- Adotar CQRS com store separado "porque é escalável" sem ter volume ou assimetria de leitura/escrita que justifique — a maior parte dos sistemas nunca precisa disso.
- Confundir "CQRS" com "preciso de dois microsserviços" — CQRS é um padrão de modelagem, não exige separação física de serviço nem de banco.
- Esquecer de decidir e comunicar o SLA de consistência eventual quando há store de leitura separado.

## Perguntas para levantar contexto

- A necessidade de leitura é significativamente diferente da necessidade de escrita hoje, ou essa complexidade estaria sendo antecipada para um problema que ainda não existe?
- Consistência eventual entre escrita e leitura é aceitável para este caso de uso? Quem decide isso — foi validado com o dono do produto?
- Se já existe (ou se cogita) event sourcing, isso está sendo confundido com a necessidade de CQRS, ou são decisões independentes bem justificadas cada uma?
