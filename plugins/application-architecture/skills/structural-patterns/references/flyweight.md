# Flyweight

Compartilha eficientemente o estado comum entre muitos objetos semelhantes, mantendo separado apenas o estado que de fato varia entre instâncias — reduzindo drasticamente o consumo de memória.

## O que procurar no código

- Um número muito grande de objetos quase idênticos instanciados (milhares a milhões), cada um carregando dados repetidos que poderiam ser compartilhados (ex.: mesma textura/glifo/estilo repetido em cada instância).
- Perfil de memória mostrando consumo desproporcional para o número de objetos "logicamente" existentes.
- Dado claramente separável em duas partes: o que é **intrínseco** (igual entre muitas instâncias, ex.: forma do caractere "a") e o que é **extrínseco** (varia por instância, ex.: posição na tela).

## Estrutura essencial

- **Flyweight**: objeto compartilhado contendo só o estado intrínseco (imutável).
- **Contexto**: objeto leve por instância lógica, guardando o estado extrínseco e uma referência ao Flyweight compartilhado.
- **Fábrica de Flyweight**: garante que flyweights com o mesmo estado intrínseco sejam reutilizados (cache/pool), nunca duplicados.

```
fabrica.obterGlifo('a')  // retorna sempre a mesma instância compartilhada
Caractere(glifo=fabrica.obterGlifo('a'), posicaoX=10, posicaoY=20)  // estado extrínseco separado
```

## Quando NÃO usar / armadilha comum

Se o número de objetos é modesto (dezenas, centenas) ou a duplicação de dados não é um problema de memória medido de verdade, Flyweight é otimização prematura — a separação intrínseco/extrínseco deixa o código mais difícil de seguir sem ganho real. É o padrão estrutural mais fácil de justificar por engenharia especulativa ("pode ficar pesado no futuro") em vez de por um problema de performance observado; só vale a complexidade quando o volume de objetos e o custo de memória por objeto já são conhecidos e relevantes.

## Exemplo conhecido

Editores de texto e engines de renderização de fonte compartilham um único objeto de glifo por caractere/estilo entre todas as ocorrências do documento, guardando só a posição de cada ocorrência separadamente.
