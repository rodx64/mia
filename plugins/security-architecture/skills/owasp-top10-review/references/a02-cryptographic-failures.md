# A02:2021 — Cryptographic Failures

Dados sensíveis expostos por falha em proteger dados em trânsito ou em repouso — antes chamado de "Sensitive Data Exposure"; o foco é a causa (criptografia ausente/errada), não só o sintoma.

## O que procurar na arquitetura

- **Dados sensíveis sem necessidade**: sistema guarda dado sensível (CPF, cartão, senha em texto puro, histórico médico) que nem precisaria armazenar.
- **Sem TLS ou TLS mal configurado**: tráfego interno "porque é rede privada" sem criptografia; certificados não validados (`verify=False`); versões antigas de TLS habilitadas.
- **Hash de senha inadequado**: MD5/SHA1/SHA256 puro em vez de bcrypt/scrypt/Argon2 com salt.
- **Criptografia simétrica com chave fixa no código/repo** em vez de KMS/secret manager.
- **Dados sensíveis em logs, URLs (query string) ou mensagens de erro.**
- **Ausência de criptografia em repouso** para backups, dumps de banco, ou armazenamento de arquivos com dados sensíveis (ex.: bucket S3 público por engano).
- **Geração de token/session ID previsível** (não usa CSPRNG).

## Controles de design

- Classificar dados por sensibilidade e minimizar o que é coletado/retido (data minimization).
- TLS em qualquer comunicação que atravesse uma fronteira de rede, mesmo "interna".
- Senhas: algoritmo de hash com custo ajustável (Argon2id, bcrypt), nunca hash reversível.
- Chaves de criptografia geridas por KMS/secret manager, com rotação — nunca hardcoded.
- Criptografar em repouso qualquer dado sensível persistido (disco, backup, cache).
- Mascarar/redigir dados sensíveis em logs por padrão (não depender de disciplina manual).

## Perguntas para levantar o contexto

- Que dados sensíveis o sistema realmente precisa guardar? Algum deles poderia não ser coletado?
- Onde ficam as chaves/segredos hoje (código, variável de ambiente, secret manager)?
- Existe alguma comunicação entre serviços sem TLS "porque é rede interna"?
