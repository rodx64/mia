# Template Method

Define o esqueleto de um algoritmo em um método na classe base, deixando que subclasses sobrescrevam apenas passos específicos sem alterar a estrutura geral do algoritmo.

## O que procurar no código

- Múltiplas subclasses/implementações repetindo a mesma sequência de passos, variando só um ou dois passos no meio.
- Duplicação de lógica de "orquestração" (a ordem dos passos) espalhada entre classes que deveriam compartilhá-la.
- Necessidade de garantir que uma sequência de passos sempre aconteça na mesma ordem, mesmo quando parte dela é customizável.

## Estrutura essencial

- **Classe base**: implementa o método template (`executar()`), que chama os passos em ordem fixa; passos variáveis são métodos abstratos ou com implementação padrão (hooks opcionais).
- **Subclasses**: sobrescrevem só os passos que variam.

```
classe ProcessadorBase:
  metodo executar():  // template, não sobrescrito
    validar()
    transformar()  // abstrato, cada subclasse implementa
    salvar()
```

## Quando NÃO usar / armadilha comum

Se as subclasses variam demais (mais da metade dos passos difere de uma para outra), o "esqueleto compartilhado" é pequeno demais para justificar a herança — nesse caso Strategy (composição) costuma encaixar melhor do que Template Method (herança). A armadilha comum é herança profunda: subclasses de subclasses sobrescrevendo passos de forma que fica difícil saber, olhando uma classe, qual é o comportamento final resultante — herança para reuso de algoritmo tende a envelhecer pior do que composição equivalente.

## Exemplo conhecido

Frameworks de teste (`setUp()` / `test()` / `tearDown()` chamados nessa ordem fixa pelo runner, cada um sobrescrito pela classe de teste concreta) e frameworks web com ciclo de vida de requisição fixo e hooks customizáveis em pontos específicos.
