# Regras — Entity Framework

- Sempre usar `.AsNoTracking()` em queries de leitura que não vão modificar as entidades.
- Nunca expor `DbContext` fora da camada `Infrastructure`. Use repositórios ou `IUnitOfWork`.
- Operações que envolvem múltiplas entidades em uma mesma transação devem usar `IDbContextTransaction` ou `IUnitOfWork`.
- Evitar `Include()` em cadeia excessiva — prefira projeções com `Select()` para queries de leitura.
- Migrations devem ser revisadas separadamente e nunca misturadas com código de feature na mesma PR.
- Não usar `Lazy Loading` — todas as relações devem ser carregadas explicitamente.
- Sempre que trabalhar com listas utilizar `EF.Constant` para manter a compatibilidade e aumentar performance.
- Nunca modificar entidades rastreadas em lugares inesperados (fora do Handler/Repository responsável) — isso pode causar saves involuntários no DbContext.
- Não usar EF com `Select(*)` para queries pesadas de relatório — prefira Dapper ou projeções SQL explícitas para leitura analítica.
