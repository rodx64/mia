# Visitor

Permite adicionar uma nova operação a uma hierarquia de tipos existente sem modificar as classes dessa hierarquia, movendo a lógica da operação para uma classe Visitor separada.

## O que procurar no código

- Necessidade recorrente de adicionar operações novas (exportar, validar, calcular) sobre uma hierarquia de tipos estável (que quase não ganha subtipos novos), sem querer poluir cada classe da hierarquia com mais um método.
- `instanceof`/checagem de tipo concreto espalhada em várias funções externas à hierarquia, cada uma implementando uma lógica diferente por tipo.
- Múltiplas operações não relacionadas (renderizar, serializar, validar) competindo por espaço dentro das mesmas classes de domínio, inchando-as.

## Estrutura essencial

- **Elemento**: interface da hierarquia com um método `aceitar(visitor)`.
- **Elementos concretos**: cada um chama de volta o método do Visitor correspondente ao seu tipo (`visitor.visitar(este)`), permitindo despacho duplo.
- **Visitor**: interface com um método de visita por tipo concreto da hierarquia; cada operação nova é uma implementação de Visitor.

```
classe Circulo implementa Forma:
  metodo aceitar(visitor): visitor.visitarCirculo(este)

classe VisitorArea implementa Visitor:
  metodo visitarCirculo(c): retorna pi * c.raio^2
```

## Quando NÃO usar / armadilha comum

Se a hierarquia de tipos muda com frequência (novos subtipos aparecem sempre), Visitor é ruim: cada novo tipo exige atualizar *todos* os Visitors existentes — é o trade-off inverso de adicionar operação fácil / adicionar tipo caro, então só compensa quando a hierarquia é estável e as operações são o que muda. A armadilha comum é usar Visitor por "elegância" em hierarquias pequenas e mutáveis, onde um método comum simples ou um `switch` por tipo seria mais barato de manter.

## Exemplo conhecido

Compiladores e parsers de AST (Abstract Syntax Tree) usam Visitor extensivamente para implementar type-checking, otimizações e geração de código como operações separadas sobre a mesma árvore de nós.
