# Singleton

Garante que uma classe tenha exatamente uma instância, acessível globalmente por um ponto único.

## O que procurar no código

- Classe com instância estática guardada internamente e um `getInstance()`/acesso global equivalente.
- Módulos "de configuração" ou "de conexão" acessados de qualquer lugar do código sem passar por injeção de dependência.
- Testes que precisam de hacks (reset manual de estado estático, ordem de execução importando) porque a instância é compartilhada entre casos de teste.

## Estrutura essencial

- Construtor privado/inacessível de fora.
- Referência estática interna à única instância.
- Ponto de acesso global (`getInstance()`) que cria a instância na primeira chamada (lazy) ou na carga do módulo.

```
classe Config:
  estatico _instancia = nulo
  metodo estatico obter():
    se _instancia é nulo: _instancia = Config()
    retorna _instancia
```

## Por que é o mais controverso/abusado

Singleton é o único padrão de criação que introduz **estado global mutável disfarçado de padrão de design**. Isso traz três problemas concretos, não teóricos:

- **Acoplamento oculto**: qualquer código pode chamar `getInstance()` de qualquer lugar, então as dependências reais de uma função ficam invisíveis na assinatura — você só descobre lendo o corpo inteiro.
- **Testes difíceis**: estado compartilhado entre testes gera testes que só passam em determinada ordem, ou exigem reset manual do singleton entre casos — sinal de que o padrão está sendo usado para conveniência, não porque exista de fato uma restrição de instância única.
- **Falsa necessidade de "só uma instância"**: na prática, a maioria dos "singletons" no código não precisa ser singleton — precisa só ser uma instância *compartilhada por conveniência*, o que injeção de dependência (uma instância criada uma vez e passada explicitamente) resolve sem os efeitos colaterais acima.

Antes de usar Singleton, pergunte: existe uma restrição real do domínio/infraestrutura que exige exatamente uma instância (ex.: um único handle de hardware, um único lock de arquivo)? Ou é só "não quero ficar passando essa dependência por aí" — nesse caso, a resposta é injeção de dependência, não Singleton.

## Quando NÃO usar / armadilha comum

Não use Singleton como atalho para evitar passar dependências explicitamente (isso é o antipadrão de Service Locator disfarçado). Não use para "objetos caros de criar" — isso é problema de lifecycle/cache, resolvido por um container de DI com escopo `singleton`, não pela classe se autogerenciar. Se você precisa mockar o singleton em testes com truques de reflection ou reset manual, isso já é o sintoma de que ele deveria ser uma dependência injetada.

## Exemplo conhecido

Pool de conexões de banco de dados de verdade, quando a infraestrutura subjacente realmente limita a um pool por processo — mas mesmo aí, a prática moderna é o container de DI segurar essa instância única e injetá-la, em vez da classe se expor como singleton acessível globalmente.
