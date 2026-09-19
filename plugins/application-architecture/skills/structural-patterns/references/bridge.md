# Bridge

Separa uma abstração da sua implementação para que as duas possam variar independentemente, evitando a explosão combinatória de subclasses quando existem dois eixos de variação cruzados.

## O que procurar no código

- Hierarquia de classes com nomes compostos cobrindo combinações de dois eixos (`ControleRemotoAvancadoTV`, `ControleRemotoBasicoRadio`, `ControleRemotoAvancadoRadio`...) — sinal de dois eixos de variação sendo achatados em uma única hierarquia de herança.
- Adicionar uma nova variação em qualquer um dos dois eixos exige criar uma subclasse para cada combinação com o outro eixo.
- Abstração de alto nível (ex.: "forma de notificação") que hoje está fisicamente amarrada a uma implementação concreta específica (ex.: "envio por e-mail"), impedindo trocar a implementação sem tocar na abstração.

## Estrutura essencial

- **Abstração**: define a interface de alto nível, mantém uma referência à Implementação.
- **Implementação**: interface separada para o eixo de variação técnico/de baixo nível.
- Abstrações concretas e Implementações concretas variam independentemente; a Abstração delega para a Implementação em vez de herdar dela.

```
classe Notificacao:
  interno: CanalEnvio  // implementação
  metodo enviar(msg): canal.transmitir(msg)

classe NotificacaoUrgente extends Notificacao:
  metodo enviar(msg): super.enviar("URGENTE: " + msg)
```

## Quando NÃO usar / armadilha comum

Se existe só um eixo de variação (não dois cruzando), Bridge é complexidade desnecessária — Strategy ou herança simples já bastam. A armadilha comum é introduzir Bridge preventivamente "achando" que um segundo eixo vai aparecer; sem os dois eixos reais e crescendo, a separação abstração/implementação não paga o custo de ter duas hierarquias paralelas para manter.

## Exemplo conhecido

Drivers de banco de dados desacoplados da API de alto nível de acesso a dados (a mesma API de "consulta" funciona sobre Postgres, MySQL ou SQLite, cada um implementando a interface de baixo nível de forma independente).
