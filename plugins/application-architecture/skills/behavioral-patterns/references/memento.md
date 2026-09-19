# Memento

Captura e externaliza o estado interno de um objeto, sem violar seu encapsulamento, para que esse estado possa ser restaurado depois (undo, checkpoint, histórico).

## O que procurar no código

- Necessidade de desfazer uma mudança de estado complexa de um objeto, e a solução hoje é expor campos internos publicamente para que código externo possa "salvar e restaurar" manualmente.
- Requisito de checkpoints/snapshots de estado (salvar progresso, versionar um documento) sem duplicar toda a lógica de construção do objeto.
- Código externo hoje precisa conhecer detalhes internos do objeto para saber o que salvar para poder restaurar depois — acoplamento que o Memento existe para eliminar.

## Estrutura essencial

- **Originador**: o objeto cujo estado será salvo; sabe criar um Memento de si mesmo (`salvar()`) e se restaurar a partir de um (`restaurar(memento)`).
- **Memento**: objeto opaco que carrega o estado capturado; só o Originador consegue ler seu conteúdo em detalhe.
- **Cuidador (Caretaker)**: guarda a lista de Mementos (histórico) sem nunca inspecionar ou alterar seu conteúdo.

```
classe Editor:
  metodo salvar(): retorna Memento(copiaDoEstadoAtual())
  metodo restaurar(m): este.estado = m.estadoInterno()
```

## Quando NÃO usar / armadilha comum

Se o estado do objeto é pequeno e já é seguro expor (um objeto de dados simples, imutável), copiar o estado diretamente é mais simples do que formalizar um Memento. A armadilha comum é capturar estado grande demais com muita frequência sem estratégia de limite (histórico ilimitado crescendo sem cap), gerando problema de memória — e vazar o conteúdo do Memento para fora do Originador, o que anula a razão de ser do padrão (preservar encapsulamento).

## Exemplo conhecido

Pilha de undo em editores de texto/imagem, e snapshots de estado em jogos (save state) que permitem voltar a um ponto anterior sem o restante do sistema conhecer a estrutura interna do estado salvo.
