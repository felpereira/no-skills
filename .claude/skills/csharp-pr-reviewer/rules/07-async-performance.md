# Regras — Async / Performance

- Todos os métodos que fazem I/O (banco, HTTP, arquivo) devem ser `async`/`await` do início ao fim da cadeia.
- Proibido usar `.Result`, `.Wait()` ou `.GetAwaiter().GetResult()` em código de produção.
- `CancellationToken` deve ser propagado em todos os métodos públicos que fazem I/O.
- Listas grandes devem sempre ter paginação — nunca retornar coleções sem limite.
- N+1 queries: nunca carregar dados relacionados dentro de loops sem usar `.Include()` ou batch loading prévio.
- Evitar chains LINQ ineficientes que geram SQL ruim (ex: `ToList()` antes de `Where()`, `Select()` fora do contexto do EF).
- Evitar chamadas repetidas ao banco que poderiam ser agrupadas em uma única query ou cacheadas dentro do mesmo request.
