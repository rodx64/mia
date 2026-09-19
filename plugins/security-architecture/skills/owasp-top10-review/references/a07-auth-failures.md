# A07:2021 — Identification and Authentication Failures

Falhas em confirmar de forma confiável quem é o usuário: login, gestão de sessão, e recuperação de credencial mal desenhados. Diferente de A01 (que é sobre *o que* o usuário autenticado pode fazer).

## O que procurar na arquitetura

- **Sem proteção contra credential stuffing/brute force**: login sem rate limit, sem lockout, sem CAPTCHA/desafio progressivo.
- **Política de senha fraca ou ausente**, sem checagem contra listas de senhas vazadas.
- **Sem MFA disponível** para contas de maior risco (admin, dados sensíveis, valor financeiro).
- **Gestão de sessão frágil**: session ID previsível, sem expiração, não invalidado no logout, ou não regenerado após login (session fixation).
- **Fluxo de "esqueci minha senha" inseguro**: token de reset previsível, sem expiração curta, ou que revela se o e-mail existe no sistema (enumeration).
- **Credenciais em URL, log ou armazenadas sem hash adequado** (ver também A02).
- **APIs internas/serviço-a-serviço sem autenticação** ("está na rede interna, não precisa").

## Controles de design

- Rate limiting e lockout progressivo em login e reset de senha; CAPTCHA como camada adicional, não substituto.
- MFA obrigatório ou fortemente incentivado para contas de risco elevado.
- Session ID gerado por CSPRNG, regenerado no login, invalidado no logout e após período de inatividade.
- Tokens de reset de senha: alta entropia, expiração curta (minutos), uso único, resposta genérica que não revela existência de conta.
- Autenticação exigida em toda comunicação serviço-a-serviço, mesmo em rede "interna" (mTLS, tokens de serviço).

## Perguntas para levantar o contexto

- O que impede alguém de tentar milhares de senhas contra o login hoje?
- Como a sessão é criada, expirada e invalidada?
- Serviços internos se autenticam entre si, ou confiam só na topologia de rede?
