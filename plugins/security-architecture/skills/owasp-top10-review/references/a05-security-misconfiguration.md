# A05:2021 — Security Misconfiguration

O sistema está configurado de forma insegura por padrão, por omissão, ou por diferença entre ambientes — inclui infraestrutura, framework, containers e cloud.

## O que procurar na arquitetura

- **Configuração padrão do framework/serviço não revisada**: debug mode ligado em produção, stack trace exposto ao usuário, painel admin exposto publicamente.
- **Permissões excessivas**: bucket/storage público sem necessidade, service account/IAM role com mais permissão do que o serviço usa, container rodando como root.
- **Superfície exposta maior que o necessário**: portas/serviços abertos que não precisam estar acessíveis externamente (banco de dados, painel de admin, métricas).
- **Headers de segurança ausentes**: sem `Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security` etc.
- **Ambientes divergentes**: staging/dev com configuração mais permissiva que nunca foi revisada para produção, ou pior, prod copiando config de dev.
- **Segredos em variável de ambiente exposta em painel de CI/CD ou logs**, ou em imagem de container versionada.
- **Dependências de infraestrutura como código (Terraform, Helm, Dockerfile) sem revisão de segurança** — a configuração "nasce" insegura.

## Controles de design

- Hardening por padrão: desabilitar debug/verbose em produção via configuração de ambiente, não flag manual.
- Princípio do menor privilégio em IAM/roles/containers — revisar periodicamente permissões concedidas vs. usadas.
- Superfície mínima: só exportar/expor o que precisa ser público; banco, cache, filas atrás de rede privada.
- Headers de segurança configurados centralmente (proxy/gateway), não por endpoint.
- Infraestrutura como código revisada como código de produto (PR review, scanner de IaC).
- Segredos via secret manager/vault, nunca em variável de ambiente versionada ou imagem de container.

## Perguntas para levantar o contexto

- Alguma coisa está exposta à internet que não precisaria estar (banco, painel, métricas, porta de debug)?
- Como as configurações de produção diferem das de staging/dev, e quem revisou isso?
- Os containers/serviços rodam com o mínimo de privilégio necessário?
