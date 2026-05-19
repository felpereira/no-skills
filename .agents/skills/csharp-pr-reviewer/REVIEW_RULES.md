# Review Rules — .NET Backend Project

> Este arquivo é o ponto de entrada das regras do projeto.
> A skill `csharp-pr-reviewer` lê automaticamente todos os arquivos da pasta `rules/` e os prioriza sobre os padrões genéricos.
> Para editar ou adicionar regras, abra o arquivo da seção correspondente em `rules/`.

## Seções disponíveis

| Arquivo                        | Conteúdo                        |
|--------------------------------|---------------------------------|
| `rules/01-arquitetura.md`      | Clean Architecture, dependências entre camadas |
| `rules/02-nomenclatura.md`     | Sufixos/prefixos de Commands, Queries, Handlers, métodos Async, etc. |
| `rules/03-mediatr.md`          | Padrões MediatR, Commands, Validators |
| `rules/04-entity-framework.md` | AsNoTracking, DbContext, Lazy Loading, EF.Constant |
| `rules/05-fluentvalidation.md` | Onde validar, o que cobrir, validações de banco |
| `rules/06-seguranca.md`        | Authorize, logs, SQL injection, claims |
| `rules/07-async-performance.md`| async/await, CancellationToken, paginação |
| `rules/08-tratamento-erros.md` | catch vazio, exceções de domínio, null returns |
| `rules/09-testes.md`           | Testes unitários, nomenclatura, mocks |
| `rules/10-organizacao.md`      | Arquivos criados devem estar em uso |
| `rules/11-nao-reportar.md`     | O que ignorar na revisão |
| `rules/12-qualidade-codigo.md` | SOLID, magic numbers, nullable risks, dead code |
