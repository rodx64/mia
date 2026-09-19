# A04:2021 — Insecure Design

Falta de controle de segurança no *design*, não na implementação — o código pode estar "correto" e ainda assim o sistema ser inseguro porque o desenho não considerou o abuso. É a categoria mais ligada a threat modeling.

## O que procurar na arquitetura

- **Nenhum threat model** foi feito para o fluxo (ex.: fluxo de pagamento, recuperação de senha, convite de usuário) — ninguém perguntou "como isso pode ser abusado?".
- **Regras de negócio sem limite** (rate limiting, limite de valor/quantidade): ex. sistema de cupom sem limite de uso, endpoint de "esqueci minha senha" sem limite de tentativas.
- **Fluxos que confiam implicitamente no cliente** para decisões de negócio (preço calculado no front-end e só validado no back).
- **Ausência de segregação entre ambientes** (dev/staging compartilhando dados/credenciais de produção).
- **Funcionalidade de alto risco sem camada extra de verificação** (ex.: transferência de valor alto sem step-up authentication).
- **Reuso de padrão inseguro conhecido** só porque "sempre foi feito assim" no time.

## Controles de design

- Threat modeling leve (mesmo que informal: "quem é o atacante, o que ele ganha, como ele tentaria") em qualquer fluxo novo com dado sensível ou valor envolvido.
- Regras de negócio com limites explícitos (rate limit, valor máximo, quantidade máxima) decididos no design, não como patch depois de um incidente.
- Nenhuma decisão de preço/permissão/quantidade confiada ao cliente — sempre revalidada no servidor.
- Segregação real entre ambientes (dados sintéticos em dev/staging, credenciais próprias).
- Funcionalidades de alto impacto (dinheiro, dados sensíveis, admin) com camada extra: confirmação, aprovação, MFA, ou revisão humana.

## Perguntas para levantar o contexto

- Esse fluxo já passou por algum exercício de "como alguém mal-intencionado tentaria abusar disso"?
- Existe algum limite (rate, valor, quantidade) definido, ou é "sem limite até dar problema"?
- O que o cliente decide vs. o que o servidor decide nesse fluxo?
