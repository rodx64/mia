# A03:2021 — Injection

Dado não confiável é interpretado como código/comando pelo interpretador de destino: SQL, NoSQL, comando de OS, LDAP, template engine, ORM inseguro, etc.

## O que procurar na arquitetura

- **Concatenação de string para montar query** (SQL, NoSQL, LDAP) com dado de entrada do usuário.
- **Uso de `eval`, `exec`, template rendering, ou desserialização** sobre conteúdo controlado por usuário.
- **Chamadas de shell/OS** (`os.system`, `subprocess` com `shell=True`, `Runtime.exec`) montadas com input externo.
- **ORMs usados de forma insegura**: raw query/`.raw()` com interpolação de string em vez de parâmetros.
- **Falta de validação de tipo/formato na fronteira**: campo esperado como número aceita qualquer string sem validação antes de chegar no componente que interpreta.
- **Cross-Site Scripting (XSS)** também é injeção — no contexto de arquitetura, ver se a camada de renderização faz escaping por padrão (template engine seguro) ou se depende de disciplina manual em cada view.

## Controles de design

- Queries parametrizadas / prepared statements sempre; nunca concatenar input em query.
- ORM com API segura por padrão; raw query é exceção revisada, nunca regra.
- Nunca passar input de usuário para `eval`, `exec`, deserialização de objetos, ou shell — se inevitável, usar allowlist rígida.
- Validar entrada na fronteira do sistema (tipo, formato, tamanho) antes de qualquer processamento — validação é defesa em profundidade, não substitui parametrização.
- Template engine com auto-escaping habilitado por padrão (a maioria dos frameworks modernos já faz isso — verificar se não foi desabilitado).
- Encoding de saída apropriado ao contexto (HTML, atributo, JS, URL) para qualquer dado renderizado.

## Perguntas para levantar o contexto

- Existe algum ponto do sistema que monta comando/query por concatenação de string em vez de parâmetros?
- O template engine usado faz escaping automático, e alguém desabilitou isso em algum lugar (`| safe`, `dangerouslySetInnerHTML`, etc.)?
- Há chamadas a shell/OS com dado de entrada do usuário no meio do comando?
