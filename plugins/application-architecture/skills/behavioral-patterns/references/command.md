# Command

Encapsula uma ação (e seus parâmetros) como um objeto, permitindo parametrizar chamadas, enfileirar ações, logá-las, ou desfazê-las/refazê-las.

## O que procurar no código

- Necessidade de enfileirar ações para executar depois, em outro contexto ou processo (fila de jobs, agendamento, ações offline sincronizadas depois).
- Requisito de desfazer/refazer (undo/redo) uma sequência de ações do usuário.
- Necessidade de logar/auditar cada ação executada de forma uniforme, incluindo poder reexecutá-la.
- Botões/menus/atalhos de UI que hoje chamam lógica de negócio diretamente, dificultando reusar a mesma ação em vários lugares (menu, atalho, macro).

## Estrutura essencial

- **Command**: interface com `executar()` (e, se houver undo, `desfazer()`), guardando os parâmetros e o receptor necessários.
- **Receptor**: o objeto que de fato sabe realizar a ação.
- **Invocador**: dispara `comando.executar()` sem saber o que o comando faz internamente.
- Opcional: histórico de comandos executados, para undo/redo ou replay.

```
classe ComandoMoverArquivo implementa Command:
  metodo executar(): sistemaArquivos.mover(origem, destino)
  metodo desfazer(): sistemaArquivos.mover(destino, origem)
```

## Quando NÃO usar / armadilha comum

Se a ação é executada uma vez, imediatamente, sem necessidade de desfazer, enfileirar ou logar, encapsulá-la como objeto Command é indireção sem propósito — chame a função direto. A armadilha comum é implementar Command "para o futuro" (undo que nunca é pedido, fila que nunca é usada) e carregar esse peso de classes extras sem nenhum dos benefícios reais do padrão sendo exercitado.

## Exemplo conhecido

Editores de texto/imagem com pilha de undo/redo, e sistemas de fila de jobs (cada job serializado é essencialmente um Command a ser executado por um worker depois).
