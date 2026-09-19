# KISS — Keep It Simple

Prefira a solução mais simples que resolve o problema real que existe hoje, não o problema hipotético que pode vir a existir. Simplicidade aqui não é "menos código" a qualquer custo — é a menor quantidade de conceitos e indireção necessária para resolver o que foi pedido, com clareza.

## Sinais de violação

- **Abstração para um único caso de uso**: interface com um único implementador real, criada "para o caso de precisar trocar depois" — a troca nunca vem, e o código carrega uma camada extra de indireção sem propósito atual.
- **Camadas de indireção sem ganho**: um método que só chama outro método que só chama outro, cada um "por organização", sem que nenhuma delas agregue lógica, variação de comportamento ou ponto de extensão real.
- **Configurabilidade não pedida**: sistema de plugins, flags ou parâmetros genéricos para cobrir variações que ninguém pediu, "só por precaução" — cada opção extra é superfície de teste e manutenção permanente por um benefício que talvez nunca se realize.
- **Padrão de projeto aplicado sem o problema que ele resolve**: Strategy para uma única estratégia, Factory para uma única classe concreta, Observer para um único observador fixo — o padrão existe para resolver variação/desacoplamento; sem essa necessidade, é peso morto.
- **Generalização prematura por semelhança superficial**: dois trechos de código que hoje fazem coisas parecidas por coincidência sendo unificados numa função genérica cheia de parâmetros e `if`s para os casos distintos — mais difícil de entender que os dois trechos separados.

## Como aplicar

- Resolver o problema atual com o código mais direto possível; adicionar abstração quando a segunda ocorrência real de variação aparecer, não antes (regra prática: "rule of three" — na terceira repetição real, abstrai).
- Preferir função/classe simples e explícita a framework interno genérico, a menos que o domínio realmente peça extensibilidade (plugin de terceiros, múltiplas implementações já existentes e ativas).
- Ao revisar, perguntar de cada abstração: "que problema concreto ela resolve hoje, com dados reais, não hipotéticos?". Se a resposta for vaga ("pode ser útil no futuro"), é candidata a simplificação.
- Simplicidade não é evitar todo design — é não pagar o custo de flexibilidade antes de precisar dela.

## Tensão com extensibilidade prematura

O maior risco de aplicar KISS mal é confundir "simples agora" com "não pensar em nada". A tensão real é: extensibilidade tem custo imediato certo (mais código, mais abstração, mais para entender) por um benefício futuro incerto. KISS pede para adiar esse custo até o benefício deixar de ser hipotético — não para nunca pagá-lo. Ver também `dry-yagni.md`, que trata da mesma tensão sob outro ângulo.

## Perguntas para levantar contexto

- Essa abstração/camada/configuração tem mais de um uso real hoje, ou foi construída para um único caso concreto "pensando no futuro"?
- Se essa indireção fosse removida, o comportamento observável do sistema mudaria? Se não, ela provavelmente não está pagando pelo seu custo.
