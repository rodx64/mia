# A06:2021 — Vulnerable and Outdated Components

O sistema depende de bibliotecas, frameworks, imagens base ou serviços com vulnerabilidades conhecidas, não suportados, ou sem processo de atualização.

## O que procurar na arquitetura

- **Sem inventário de dependências**: ninguém sabe com certeza quais libs/versões estão em produção (direto e transitivo).
- **Sem processo de atualização**: dependências desatualizadas há muito tempo porque "atualizar quebra" e nunca foi priorizado.
- **Imagem base de container desatualizada ou desconhecida** (`latest` sem pin, imagem não oficial sem verificação).
- **Componentes sem suporte/EOL** (versão de linguagem, framework ou banco fora do ciclo de suporte).
- **Ausência de scanning automatizado** (SCA) no pipeline de CI/CD — vulnerabilidade só é descoberta se alguém procurar manualmente.
- **Uso de biblioteca desnecessária** só para uma função pequena, aumentando superfície de ataque sem necessidade.

## Controles de design

- Inventário de dependências (SBOM) gerado automaticamente no build.
- Scanning de vulnerabilidades (SCA) no CI/CD, com política de severidade que bloqueia merge/deploy quando crítico.
- Processo recorrente (não ad-hoc) de atualização de dependências — dependabot/renovate ou equivalente.
- Pin de versão explícito para imagens base e dependências, com atualização deliberada.
- Avaliar necessidade real antes de adicionar uma dependência nova (menos é mais superfície de ataque a menos).

## Perguntas para levantar o contexto

- Existe algum scanner de dependências (SCA) rodando hoje, no CI ou fora dele?
- Quando foi a última vez que as dependências principais foram atualizadas?
- As imagens de container usadas têm versão fixa e são de fonte confiável?
