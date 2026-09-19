# A01:2021 — Broken Access Control

Um usuário consegue agir fora do que foi autorizado: ver, editar ou apagar dados/recursos de outro usuário, ou acessar funções administrativas sem privilégio para isso.

## O que procurar na arquitetura

- **Autorização decidida no cliente**: front-end esconde botões/rotas, mas o back-end não revalida a permissão em cada request.
- **IDs previsíveis sem checagem de posse**: endpoints como `/orders/{id}` que buscam o recurso pelo ID sem verificar se ele pertence ao usuário autenticado.
- **Falta de modelo de autorização explícito**: papéis/permissões implementados como `if (user.role == "admin")` espalhados pelo código, em vez de uma camada central (RBAC/ABAC).
- **Herança de permissão em multi-tenant**: dados de tenants diferentes na mesma tabela/índice sem isolamento reforçado por linha (row-level security) ou por serviço.
- **CORS permissivo** (`Access-Control-Allow-Origin: *` com credenciais) expondo APIs autenticadas a qualquer origem.
- **Verbos HTTP inconsistentes**: checagem de autorização só implementada no `GET`, esquecida em `PUT`/`PATCH`/`DELETE` do mesmo recurso.
- **Mass assignment**: endpoints que aceitam o objeto inteiro do cliente (`PATCH /me` aceitando `role`, `isAdmin`) sem allowlist de campos.

## Controles de design

- Autorização sempre no backend, por request, nunca só na UI.
- Um único ponto de decisão de autorização (middleware/policy layer), não checagens espalhadas.
- Verificar posse do recurso (`resource.owner_id == session.user_id`) além de autenticação.
- Multi-tenancy: isolar por padrão (deny-by-default), com testes automatizados de cross-tenant access.
- Allowlist explícita de campos mutáveis por endpoint, nunca bind direto do body inteiro em um model.
- Negar por padrão; liberar por exceção (deny-by-default nas rotas novas).

## Perguntas para levantar o contexto

- Como o sistema decide "usuário X pode fazer Y no recurso Z"? Existe uma camada única ou é decisão espalhada?
- Existe mais de um tenant/organização compartilhando a mesma infraestrutura de dados?
- Todos os verbos HTTP de um mesmo recurso passam pela mesma checagem de autorização?
