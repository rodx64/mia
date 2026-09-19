# DRY e YAGNI

Dois princípios que puxam em direções aparentemente opostas — um contra duplicação, outro contra construção antecipada — e que só funcionam bem aplicados juntos.

## DRY — Don't Repeat Yourself

Cada pedaço de **conhecimento** (uma regra de negócio, uma decisão, um invariante) deve ter uma única representação autoritativa no sistema. DRY é sobre duplicação de conhecimento, não sobre duplicação de texto/sintaxe.

- **Sinal de violação real**: a mesma regra de negócio (ex.: "desconto máximo permitido é 30%") implementada em dois lugares independentes que podem divergir com o tempo — corrigir em um e esquecer o outro é o sintoma clássico.
- **Não é violação de DRY** (mesmo parecendo): dois trechos de código com sintaxe parecida mas representando conceitos de negócio diferentes que só coincidem hoje. Ex.: validação de CPF e validação de código de produto podem ter uma estrutura de código similar (regex + checagem de dígito) sem serem o mesmo conhecimento — unificá-los numa função genérica cria acoplamento artificial entre dois conceitos que evoluem por razões diferentes.
- **Armadilha da abstração prematura por semelhança superficial**: perceber que dois códigos "parecem iguais" e extrair uma função/classe comum antes de confirmar que representam o mesmo conceito de domínio. Quando um dos dois precisar mudar por um motivo que não se aplica ao outro, a abstração vira uma bagunça de parâmetros e `if`s para acomodar os dois casos — pior que a duplicação original.
- Como decidir: pergunte "se essa regra mudar amanhã, os dois lugares deveriam mudar junto, pela mesma razão?". Se sim, é duplicação de conhecimento — unifique. Se não, é coincidência — deixe duplicado.

## YAGNI — You Aren't Gonna Need It

Não construa algo para um requisito hipotético que ainda não existe, mesmo que pareça razoável que vá existir.

- **Sinal de violação**: parâmetro de configuração, campo de banco, camada de abstração ou integração construída "porque provavelmente vamos precisar" sem um requisito concreto e presente.
- **Como aplicar**: construir para o requisito que existe agora, da forma mais direta; adicionar o que falta quando o requisito hipotético se tornar real — mudar código para adicionar uma capacidade nova custa menos do que manter uma capacidade especulativa que nunca é usada (e que também precisa ser testada, documentada e entendida por quem lê o código).

## Como os dois se equilibram

DRY mal aplicado é frequentemente uma violação de YAGNI disfarçada: ao unificar dois códigos parecidos "para não repetir", cria-se uma abstração genérica com pontos de extensão (parâmetros, flags, hooks) para acomodar variações que só existem porque alguém antecipou que "no futuro vai precisar variar mais". Isso é construir flexibilidade não pedida (violação de YAGNI) sob o pretexto de eliminar duplicação (motivação de DRY).

A ordem prática recomendada: tolerar duplicação de código pequena no início; aplicar DRY só quando a duplicação for confirmada como duplicação de conhecimento (mesma regra, mesmo motivo de mudança) e já tiver aparecido mais de uma vez; nesse momento, extrair a abstração mínima que resolve a duplicação atual, não a mais genérica que se possa imaginar.

## Perguntas para levantar contexto

- A duplicação encontrada representa a mesma regra de negócio, ou dois conceitos que hoje têm código parecido por coincidência?
- Essa abstração/configuração/parâmetro resolve uma variação que existe hoje, ou uma que "pode vir a existir"?
