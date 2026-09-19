# Abstract Factory

Cria famílias inteiras de objetos relacionados garantindo que as variantes escolhidas sejam consistentes entre si, sem o cliente saber qual família concreta está em uso.

## O que procurar no código

- Vários objetos criados juntos que precisam ser da mesma "linha"/"tema"/"plataforma" — misturar peças de famílias diferentes gera bug ou inconsistência (ex.: componente de botão de um tema com componente de input de outro).
- Parâmetro de configuração (tema, plataforma, ambiente, provedor) que é checado repetidamente em vários pontos de criação diferentes ao longo do código.
- Troca de fornecedor/plataforma inteira (ex.: trocar todos os drivers de um SGBD, ou toda a stack de um provedor de nuvem) exige editar dezenas de pontos de instanciação.

## Estrutura essencial

- **Fábrica abstrata**: interface com um método de criação para cada produto da família (`criarBotao()`, `criarInput()`).
- **Fábrica concreta**: uma por família/variante, implementando todos os métodos de forma consistente entre si.
- **Produtos abstratos e concretos**: cada tipo de peça da família, com suas variantes.
- Cliente recebe uma fábrica concreta (normalmente injetada/configurada uma vez) e só chama métodos da interface abstrata.

## Quando NÃO usar / armadilha comum

Só compensa quando existe mais de uma família real e a consistência entre as peças da mesma família importa de verdade. Aplicar Abstract Factory para "organizar" a criação de objetos que não têm essa relação de família (peças que podem ser combinadas livremente entre variantes sem problema) é complexidade desnecessária — nesse caso, Factory Method simples por peça já resolve. É também o padrão de criação mais raro de precisar na prática fora de frameworks de UI/plataforma — cuidado ao introduzi-lo "porque parece arquitetural".

## Exemplo conhecido

Toolkits de UI multiplataforma (ex.: bibliotecas de widgets que suportam temas ou sistemas operacionais diferentes) usam uma fábrica por tema/SO para garantir que botão, campo de texto e menu vêm todos do mesmo conjunto visual.
