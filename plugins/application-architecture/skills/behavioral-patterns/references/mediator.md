# Mediator

Centraliza a comunicação entre um conjunto de objetos em um único objeto mediador, evitando que eles se referenciem diretamente uns aos outros de forma emaranhada.

## O que procurar no código

- Muitos objetos com referências diretas uns aos outros (many-to-many), onde mudar um exige entender o efeito em vários outros que ele conhece diretamente.
- Componentes de UI (campos de um formulário, por exemplo) que se comunicam diretamente entre si ("quando o campo A muda, desabilita o campo B, que atualiza o campo C") formando uma teia difícil de seguir.
- Adicionar um novo participante à comunicação exige alterar vários dos participantes existentes para que "saibam" dele.

## Estrutura essencial

- **Mediador**: interface e implementação que conhece todos os participantes e coordena a comunicação entre eles.
- **Participantes (Colleagues)**: só conhecem o mediador, nunca uns aos outros diretamente; notificam o mediador de eventos e recebem instruções dele.

```
classe MediadorFormulario:
  metodo notificar(origem, evento):
    se origem == campoA e evento == "mudou": campoB.desabilitar()
```

## Quando NÃO usar / armadilha comum

Se são só dois objetos se comunicando, um Mediador é indireção desnecessária — a referência direta já é simples de seguir. A armadilha comum é o Mediador virar um deus-objeto que concentra toda a lógica de coordenação do sistema (mesmo risco do Facade mal aplicado) — o ganho do padrão é *distribuir* o conhecimento de "quem fala com quem" para um lugar central e legível, não empilhar regra de negócio inteira nesse objeto.

## Exemplo conhecido

Controladores de tráfego aéreo como metáfora clássica do padrão; na prática, frameworks de UI com um "controller" de tela central coordenando vários widgets que não se conhecem diretamente entre si.
