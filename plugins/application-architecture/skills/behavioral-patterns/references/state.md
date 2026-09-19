# State

Permite que um objeto altere seu comportamento quando seu estado interno muda, encapsulando cada estado em uma classe própria em vez de espalhar condicionais sobre "estado atual" pelo código.

## O que procurar no código

- `switch`/`if` sobre um campo de "status"/"estado" (`pedido.status == "pago"`, `== "enviado"`, `== "cancelado"`) repetido em vários métodos da mesma classe.
- Transições de estado inválidas só detectadas em runtime (ex.: tentar cancelar um pedido já entregue) porque não há um lugar único que module quais transições são permitidas.
- Comportamento de um método muda de forma significativa dependendo do estado, a ponto do método virar um bloco de condicionais grande.

## Estrutura essencial

- **Contexto**: mantém referência ao estado atual e delega chamadas a ele.
- **Estado**: interface comum a todos os estados possíveis (`avancar()`, `cancelar()`).
- **Estados concretos**: cada um implementa o comportamento válido para aquele estado, incluindo a transição para o próximo estado (ou rejeição da transição).

```
interface EstadoPedido: metodo avancar(pedido)
classe Pago implementa EstadoPedido:
  metodo avancar(pedido): pedido.estado = Enviado()
```

## Quando NÃO usar / armadilha comum

Se existem só dois ou três estados com transições triviais e sem comportamento diferente de verdade por estado (só um campo booleano/enum sendo lido), transformar isso em uma hierarquia de classes é over-engineering — um enum com um `switch` pequeno e centralizado já é legível o suficiente. A armadilha comum é confundir State com Strategy: em State, é o próprio objeto que troca de comportamento e dispara suas próprias transições; se quem decide a variação é sempre um código externo escolhendo qual "modo" usar, é Strategy, não State.

## Exemplo conhecido

Máquinas de estado de pedido em e-commerce (criado → pago → enviado → entregue → cancelado) e de conexões de rede (TCP: fechado, ouvindo, estabelecido, fechando) são State clássico quando cada estado tem regras de transição e comportamento próprios.
