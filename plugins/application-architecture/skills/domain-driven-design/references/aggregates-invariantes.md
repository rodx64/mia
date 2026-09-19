# Aggregate, Aggregate Root e Invariantes

Um **aggregate** é um cluster de entities e value objects tratado como uma unidade de consistência transacional: tudo dentro dele muda junto, respeitando invariantes garantidas atomicamente. O **aggregate root** é a única entity do cluster acessível de fora — todo acesso externo passa por ela.

## O que procurar

- **Acesso direto a entidade interna do aggregate**: um repository ou service que busca/altera uma entidade filha (ex.: `ItemPedido`) sem passar pela raiz (`Pedido`), permitindo estado inconsistente (total do pedido divergindo da soma dos itens).
- **Aggregate gigante**: um cluster tentando modelar "tudo relacionado" (ex.: `Cliente` carregando todos os seus `Pedido`s, que carregam todos os `Pagamento`s) — vira gargalo de concorrência (toda transação trava o cluster inteiro) e mistura invariantes que não têm relação real.
- **Referência direta entre aggregates diferentes** (objeto completo, não ID) — cria acoplamento de carregamento (carregar um aggregate carrega o grafo inteiro) e viola a ideia de que cada aggregate é sua própria unidade transacional.
- **Invariante verificada fora do aggregate**: uma regra que deveria ser sempre verdadeira (ex.: "total do pedido nunca é negativo") sendo checada em um service externo, com múltiplos pontos de entrada que podem esquecer a checagem.

## Como aplicar

- Defina o aggregate pelo que precisa mudar **atomicamente junto**, não pelo que parece relacionado no modelo de dados. Pergunta-teste: "se eu salvar só uma parte disso e falhar antes de salvar o resto, o sistema fica em estado inválido?" — se sim, está dentro do mesmo aggregate; se não, são aggregates separados.
- Aggregates pequenos por padrão. Comece pequeno e só junte duas entities no mesmo aggregate quando houver uma invariante real que force isso.
- Referencie outros aggregates só por ID (`clienteId: ClienteId`, nunca `cliente: Cliente`) — carregar o outro aggregate é responsabilidade explícita de quem precisa dele, via seu próprio repository.
- Toda mutação passa por um método da raiz que garante a invariante antes de aceitar a mudança (`pedido.adicionarItem(item)` valida limites/regras, não um `pedido.itens.add(item)` direto em uma lista pública).
- Consistência entre aggregates diferentes é eventual, não transacional — se dois aggregates precisam ficar sincronizados, isso acontece via domain event e processo separado, não via transação distribuída.

## Em Kotlin

- Exponha coleções internas como `List` somente-leitura (`val itens: List<Item> get() = _itens.toList()` com `_itens: MutableList<Item>` privado), nunca como `MutableList` público — força toda mutação a passar por um método da raiz.
- Construtor privado ou `internal` + companion object com factory (ver `factories.md`) evita que o aggregate seja criado em estado inválido a partir de fora do módulo de domínio.
- Métodos da raiz devem lançar exceção de domínio (`IllegalStateException`/exceção customizada) ou retornar `Result`/tipo de erro do domínio quando uma invariante seria violada — nunca aceitar silenciosamente um estado inconsistente.

## Perguntas para levantar contexto

- Quais mudanças no sistema precisam acontecer atomicamente juntas, sem exceção? Isso define o limite do aggregate.
- Existe algum código que acessa ou altera uma entidade "filha" de um aggregate sem passar pela raiz?
- Dois aggregates estão referenciando um ao outro por objeto completo, e isso poderia ser só um ID?
