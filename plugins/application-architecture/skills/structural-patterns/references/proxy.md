# Proxy

Fornece um substituto ou representante de outro objeto, controlando o acesso a ele — para adiar criação cara, checar permissão, adicionar cache, ou representar um objeto remoto.

## O que procurar no código

- Objeto caro de criar/carregar que só deveria ser instanciado de fato quando usado pela primeira vez (lazy loading).
- Necessidade de checar permissão antes de permitir uma chamada a um objeto real, sem misturar essa checagem na lógica de negócio do objeto.
- Chamadas a um objeto remoto (outro processo/serviço) representadas localmente como se fossem uma chamada normal.
- Necessidade de cachear resultados de chamadas repetidas a um objeto caro, de forma transparente para quem chama.

## Estrutura essencial

- **Sujeito**: interface comum entre o objeto real e o proxy.
- **Sujeito real**: implementação de fato, com o custo/restrição que motiva o proxy.
- **Proxy**: implementa a mesma interface, mantém referência ao Sujeito real (criado sob demanda ou não), e intercepta a chamada para aplicar controle antes de delegar.

```
classe ProxyImagem implementa Imagem:
  interno: ImagemReal ou nulo
  metodo exibir():
    se interno é nulo: interno = ImagemReal(carregarDoDisco())
    interno.exibir()
```

## Quando NÃO usar / armadilha comum

Se não há controle real a aplicar (nenhuma checagem, nenhum custo a adiar, nenhuma travessia de rede), um Proxy é só uma camada de delegação vazia — chame o objeto real direto. A armadilha comum é confundir Proxy com Decorator: Proxy controla *acesso* ao mesmo objeto/contrato (o cliente não deveria notar diferença de comportamento, só de custo/permissão), enquanto Decorator *adiciona* comportamento novo de propósito — se o "proxy" está mudando o resultado da operação, provavelmente é um Decorator mal nomeado.

## Exemplo conhecido

ORMs usam proxies para lazy loading de relacionamentos (`pedido.itens` só dispara a query no banco quando acessado pela primeira vez); bibliotecas de RPC/gRPC geram um proxy local que parece uma chamada de método comum mas na verdade serializa e envia pela rede.
