# Prototype

Cria novos objetos copiando (clonando) um objeto existente já configurado, em vez de construí-los do zero.

## O que procurar no código

- Criar um objeto do zero é caro (I/O, cálculo pesado, carga de dados externos) e existe um exemplar já pronto que serve de base.
- Código que monta um objeto de configuração "padrão" e depois faz várias cópias com pequenos ajustes.
- Necessidade de duplicar um objeto complexo preservando seu estado interno, incluindo sub-objetos, sem reexecutar toda a lógica de inicialização.
- Hierarquias de classes onde criar via `new ClasseX()` exigiria o código cliente conhecer a classe concreta exata do objeto a duplicar.

## Estrutura essencial

- **Protótipo**: interface com um método `clonar()`.
- **Protótipo concreto**: implementa `clonar()` retornando uma cópia de si mesmo (rasa ou profunda, dependendo do que os campos exigem).
- Cliente pede a cópia ao próprio objeto (`objetoExistente.clonar()`), sem precisar saber a classe concreta nem repetir os parâmetros de construção.

```
metodo clonar():
  retorna copiaProfunda(este)
```

## Quando NÃO usar / armadilha comum

Se construir o objeto do zero é barato e simples, clonar não traz benefício — só adiciona a responsabilidade de manter `clonar()` correto conforme novos campos são adicionados (armadilha clássica: clone raso quando deveria ser profundo, vazando referências compartilhadas entre "cópias" que deveriam ser independentes). Também não é a resposta para "quero configuração padrão reutilizável" quando um objeto imutável compartilhado ou um Builder com valores default já resolve de forma mais simples e sem risco de aliasing.

## Exemplo conhecido

`Object.clone()` em Java/`copy.deepcopy` em Python usados para duplicar objetos de configuração complexos; em jogos, clonar um "template" de inimigo/item já configurado em vez de reconstruir toda a árvore de atributos a cada spawn.
