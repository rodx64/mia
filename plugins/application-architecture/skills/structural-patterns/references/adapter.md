# Adapter

Converte a interface de uma classe existente em outra interface que o cliente espera, permitindo que classes incompatíveis colaborem sem alterar nenhuma das duas.

## O que procurar no código

- Integração com biblioteca/API externa cuja interface não bate com a que o resto do sistema usa, gerando código de "tradução" espalhado em vários pontos de chamada em vez de concentrado.
- Wrappers ad-hoc repetidos ao redor da mesma dependência externa em lugares diferentes do código, cada um convertendo os dados de um jeito ligeiramente diferente.
- Troca de biblioteca/fornecedor exigiria alterar código de negócio porque ele depende diretamente do formato da lib antiga.

## Estrutura essencial

- **Target**: interface que o cliente espera.
- **Adaptee**: classe existente com interface incompatível (geralmente de terceiros, não deve ser alterada).
- **Adapter**: implementa o Target, traduzindo chamadas para o Adaptee internamente.

```
classe AdapterPagamento implementa InterfacePagamento:
  metodo pagar(valor):
    retorna libExterna.executarCobranca(valor * 100, "BRL")  // traduz formato
```

## Quando NÃO usar / armadilha comum

Se as duas interfaces já são compatíveis (mesmos métodos, mesma semântica), não crie um Adapter só para "desacoplar por precaução" — isso é indireção sem tradução real acontecendo. Outra armadilha: deixar o Adapter acumular lógica de negócio além da tradução de interface, virando um objeto de responsabilidade confusa — Adapter deve só traduzir, não decidir.

## Exemplo conhecido

Bibliotecas de acesso a dados que adaptam drivers de bancos diferentes (Postgres, MySQL, SQLite) para uma interface única de consulta, permitindo trocar o banco sem mudar o código que usa a interface comum.
