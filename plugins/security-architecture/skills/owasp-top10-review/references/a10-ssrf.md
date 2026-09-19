# A10:2021 — Server-Side Request Forgery (SSRF)

O servidor faz uma requisição a uma URL controlada (total ou parcialmente) pelo usuário, permitindo alcançar recursos internos que o usuário não deveria acessar diretamente (metadados de cloud, serviços internos, `localhost`).

## O que procurar na arquitetura

- **Qualquer funcionalidade que busca uma URL fornecida pelo usuário**: preview de link, importação de imagem por URL, webhook de callback, integração que busca um recurso externo apontado pelo cliente.
- **Sem validação/allowlist de destino**: o serviço aceita qualquer host/IP, incluindo `localhost`, IPs de rede privada (`169.254.169.254` para metadados de cloud, `10.x`, `192.168.x`) e esquemas alternativos (`file://`, `gopher://`).
- **Redirecionamento seguido automaticamente**: validação do host acontece antes do redirect, mas o cliente HTTP segue o redirect para um destino não validado depois.
- **Serviço interno sem autenticação** alcançável a partir do componente vulnerável a SSRF, ampliando o impacto (pivot para outro sistema interno).

## Controles de design

- Allowlist de destinos permitidos sempre que possível, em vez de blocklist (blocklist de IP privado é fácil de contornar com DNS rebinding, redirects, encoding).
- Desabilitar/restringir esquemas não-HTTP(S) no cliente HTTP usado para a requisição.
- Não seguir redirects automaticamente sem revalidar o destino final.
- Isolar de rede o componente que faz requisições a URLs externas, sem acesso direto a serviços internos sensíveis (segmentação de rede como defesa em profundidade).
- Bloquear acesso a endpoints de metadados de cloud (`169.254.169.254`) a partir de qualquer processo que aceite URL de usuário.

## Perguntas para levantar o contexto

- Existe alguma funcionalidade onde o servidor busca uma URL fornecida (direta ou indiretamente) pelo usuário?
- Esse componente roda em uma rede com acesso a serviços internos sensíveis ou a endpoints de metadados de cloud?
- Como o cliente HTTP usado trata redirects e esquemas de URL?
