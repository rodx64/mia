# DDD tático dentro da arquitetura hexagonal (resumo)

Domain-Driven Design tático dá vocabulário e forma ao que fica *dentro* do hexágono. A arquitetura hexagonal diz onde estão as fronteiras (domínio isolado de infraestrutura); o DDD tático diz como organizar o que está dentro dessa fronteira.

Para o processo completo (entities vs. value objects, aggregates e invariantes, domain services, domain events, repositories, factories, specifications, linguagem ubíqua, bounded contexts), use a skill `domain-driven-design` — este arquivo cobre só o essencial para não interromper uma revisão de hexagonal com um desvio longo.

- **Aggregate root** é a única entity de um cluster acessível de fora; repository opera sobre ela inteira, nunca sobre uma entidade filha isolada.
- **Value object** encapsula validação e comportamento de conceitos com regras próprias (dinheiro, e-mail, CPF) em vez de deixá-los como primitivos soltos.
- **Domain event** é produzido pelo domínio (registrado/retornado), mas quem publica de fato em um broker/bus é um adapter de saída — o domínio não sabe onde o evento vai parar.
- **Repository como port**: interface em termos do domínio, implementada por um adapter concreto; opera sobre o aggregate root, nunca vaza sintaxe de query.

## Perguntas para levantar contexto

- Quais são os aggregates do domínio e qual é a raiz de cada um?
- Conceitos como dinheiro, e-mail, CPF — são value objects ou primitivos soltos?
- Eventos de domínio são publicados diretamente por código de domínio, ou existe um adapter fazendo essa ponte?
