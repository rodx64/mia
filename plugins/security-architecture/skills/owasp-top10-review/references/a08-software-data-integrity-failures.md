# A08:2021 — Software and Data Integrity Failures

O sistema assume que código, dados ou atualizações não foram adulterados, sem verificar isso — inclui pipeline de CI/CD, desserialização insegura e dependências não verificadas.

## O que procurar na arquitetura

- **Pipeline de CI/CD sem verificação de integridade**: dependências baixadas sem checar hash/assinatura, artefato de build não assinado, deploy que aceita qualquer imagem.
- **Auto-update sem verificação de assinatura** (cliente/agente que baixa e executa atualização sem validar origem).
- **Desserialização de dados não confiáveis** (objetos serializados, JWT, cookies assinados) sem verificar assinatura/integridade antes de processar.
- **JWT mal validado**: aceitar `alg: none`, não verificar assinatura, ou confiar em claims sem validar o emissor.
- **Uso de repositórios/registries de terceiros sem controle** (qualquer um pode publicar um pacote com nome parecido — dependency confusion).
- **Infraestrutura como código aplicada sem revisão** (qualquer PR mergeado dispara apply automático em produção sem gate).

## Controles de design

- Verificar assinatura/hash de dependências e artefatos de build (supply chain: lockfiles, provenance, SBOM assinado quando possível).
- Pipeline de CI/CD com least privilege e aprovação humana para mudanças que afetam produção.
- Nunca desserializar dado não confiável sem verificar integridade/assinatura antes.
- Validar JWT com allowlist de algoritmo (nunca aceitar `none`) e verificar emissor/audiência.
- Registries privados com controle de publicação para evitar dependency confusion.

## Perguntas para levantar o contexto

- O pipeline de build/deploy verifica a integridade do que baixa e do que publica?
- Existe algum ponto do sistema que desserializa dado vindo de fora (cookie, token, upload) sem checar integridade primeiro?
- Quem pode fazer merge/deploy direto em produção, e existe algum gate humano?
