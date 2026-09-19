# Interpreter

Define uma representação para a gramática de uma linguagem simples e um interpretador que avalia sentenças dessa gramática, modelando cada regra gramatical como uma classe.

## O que procurar no código

- Sistema precisa avaliar expressões/regras escritas pelo usuário ou configuráveis (fórmulas, filtros, regras de permissão, condições de negócio) que hoje são interpretadas por um parser manual ad-hoc ou por `eval` sobre string (risco de segurança, ver também injeção).
- Uma mini-linguagem de domínio específico (DSL) precisa ser avaliada repetidamente, com uma gramática pequena e estável.
- Regras compostas por combinação de operadores lógicos (E, OU, NÃO) sobre condições simples, montadas dinamicamente.

## Estrutura essencial

- **Expressão abstrata**: interface com `interpretar(contexto)`.
- **Expressão terminal**: representa os elementos básicos da gramática (valores, variáveis).
- **Expressão não-terminal**: representa regras compostas por outras expressões (E, OU, soma), interpretando recursivamente as expressões filhas.

```
interface Expressao: metodo interpretar(contexto)
classe E implementa Expressao:
  esquerda, direita: Expressao
  metodo interpretar(ctx): retorna esquerda.interpretar(ctx) e direita.interpretar(ctx)
```

## Quando NÃO usar / armadilha comum

Interpreter é o padrão comportamental mais raro de precisar na prática — para gramáticas com qualquer complexidade real, um parser gerado por ferramenta (parser combinator, gramática formal) ou reutilizar uma linguagem de expressão já existente (ex.: JSONLogic, CEL, expressões de biblioteca madura) é muito mais barato de manter do que implementar Interpreter manualmente classe por regra. A armadilha comum é usar este padrão para o que deveria ser resolvido com `eval` controlado/sandboxed de uma linguagem de expressão pronta, ou subestimar o quanto a gramática vai crescer e descobrir tarde que a abordagem não escala.

## Exemplo conhecido

Motores de regras de negócio (regras de desconto, elegibilidade) e calculadoras de expressão matemática simples embutidas em planilhas ou formulários configuráveis por usuário final.
