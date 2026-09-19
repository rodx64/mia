# Tell, Don't Ask

Diga ao objeto o que fazer; não pergunte pelo seu estado interno para depois decidir por ele de fora. A lógica que opera sobre o dado deve viver junto do dado, não em quem o consome.

## Sinais de violação

- Código externo chamando uma sequência de getters e depois aplicando lógica de decisão com o resultado: `if (conta.getSaldo() >= valor) { conta.setSaldo(conta.getSaldo() - valor); }` — quem chama está fazendo o trabalho que deveria ser da própria `Conta` (`conta.sacar(valor)`, com a regra e a validação dentro do método).
- Cadeias de getters (`pedido.getCliente().getEndereco().getCidade()`) usadas para tomar uma decisão de negócio fora do objeto dono do dado — ver também `law-of-demeter.md`, princípio irmão deste.
- Classes que são só sacos de dados com getters/setters públicos (anemic model) cercadas de "service" classes que concentram toda a lógica de negócio manipulando esses dados de fora.
- Testes que precisam inspecionar múltiplos campos internos de um objeto para validar uma operação, em vez de perguntar uma única coisa objetiva ao objeto (`pedido.estaAprovado()` em vez de checar três campos manualmente).

## Como aplicar

- Perguntar "quem tem a informação necessária para tomar essa decisão?" — a lógica deve morar lá, não em quem consulta a informação de fora.
- Métodos que expressam intenção de negócio (`sacar`, `aprovar`, `cancelar`) em vez de só expor estado para manipulação externa (`setStatus`, `setSaldo`).
- Ao encontrar um getter usado só para decisão externa, considerar mover a decisão para dentro do objeto como um método novo.
- Não é regra absoluta: objetos de leitura pura (DTOs, view models, dados que saem do sistema para exibição) não precisam disso — Tell Don't Ask se aplica a objetos com comportamento e invariantes de negócio, não a estruturas de transporte de dado.

## Relação com encapsulamento

Tell Don't Ask é a consequência prática de levar encapsulamento a sério: não basta esconder os campos atrás de getters/setters se a lógica de decisão ainda mora fora — isso é encapsulamento de dado sem encapsulamento de comportamento. Um objeto bem encapsulado expõe operações com significado de negócio e esconde como elas são realizadas internamente, incluindo a validação de quando são permitidas.

## Perguntas para levantar contexto

- Existem classes que são principalmente getters/setters cercadas de lógica de negócio em outras classes ("services" anêmicos)?
- Alguma decisão de negócio importante depende de ler múltiplos campos de um objeto de fora dele, em vez de perguntar uma coisa direta ao próprio objeto?
