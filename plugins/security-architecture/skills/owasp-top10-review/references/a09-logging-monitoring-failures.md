# A09:2021 — Security Logging and Monitoring Failures

Sem log e monitoramento suficientes, um ataque pode acontecer sem ser detectado — e sem log adequado, é impossível investigar depois. Não é sobre performance monitoring, é sobre visibilidade de eventos de segurança.

## O que procurar na arquitetura

- **Eventos de segurança não logados**: login (sucesso e falha), mudança de permissão, acesso a dado sensível, falha de autorização — nada disso é registrado.
- **Logs sem contexto suficiente** para investigar (sem timestamp, usuário, IP, ação — só uma stack trace genérica).
- **Ninguém olha os logs**: existe log, mas não há alerta, dashboard ou processo de revisão — o log só existe para debug funcional.
- **Logs armazenados de forma insegura ou apagados rápido demais** (retenção curta demais para investigar um incidente).
- **Dado sensível dentro do log** (ver A02) — problema duplo: exposição e não conformidade.
- **Sem plano de resposta a incidente**: mesmo que algo seja detectado, não há processo definido do que fazer.

## Controles de design

- Logar eventos de segurança de forma consistente: autenticação (sucesso/falha), autorização negada, mudanças administrativas, acesso a dados sensíveis.
- Formato estruturado com contexto suficiente para correlação (usuário, IP, timestamp, ação, recurso).
- Alertas automáticos para padrões suspeitos (múltiplas falhas de login, escalação de privilégio, volume anômalo de acesso).
- Retenção de log compatível com o tempo realista de detecção de um incidente (dias/semanas, não horas).
- Log centralizado e protegido contra alteração pelo próprio atacante (write-once ou sistema separado).

## Perguntas para levantar o contexto

- Se alguém tentasse forçar login 10 mil vezes hoje, algo perceberia?
- Quais eventos de segurança são logados hoje, e alguém realmente olha isso?
- Por quanto tempo os logs ficam disponíveis para investigação?
