# Review Rules — .NET Backend Project

> Este arquivo define as regras e convenções do projeto.
> A skill `csharp-pr-reviewer` lê este arquivo e prioriza estas regras sobre os padrões genéricos.
> Edite conforme a realidade do seu projeto.

---

## Arquitetura

- O projeto segue **Clean Architecture** com as camadas: `Domain`, `Application`, `Infrastructure`, `API`.
- Dependências só podem apontar para dentro: `API → Application → Domain`. `Infrastructure` implementa interfaces definidas em `Application` ou `Domain`.
- Nenhuma camada interna (`Domain`, `Application`) pode referenciar `Infrastructure` ou `API`.
- Classes de domínio não devem ter dependências externas (sem injeção de serviços, sem referência a EF, sem HTTP).

---

## Nomenclatura

- **Commands**: sufixo `Command` (ex: `CreateOrderCommand`), resposta com sufixo `Response` quando necessário.
- **Queries**: sufixo `Query` (ex: `GetOrderByIdQuery`).
- **Handlers**: sufixo `Handler` (ex: `CreateOrderCommandHandler`).
- **Validators**: sufixo `Validator` (ex: `CreateOrderCommandValidator`).
- **Repositories**: prefixo `I` para interface + sufixo `Repository` (ex: `IOrderRepository`, `OrderRepository`).
- **Services de domínio**: sufixo `Service` (ex: `PricingService`).
- **Controllers**: sufixo `Controller`, nunca deve conter lógica de negócio.
- **DTOs**: sufixo `Dto` ou `Request`/`Response` conforme contexto.

---

## MediatR

- Todo caso de uso deve ser implementado como um `Command` ou `Query` com seu respectivo `Handler`.
- Controllers devem apenas receber a requisição, mapear para um Command/Query, disparar via `IMediator` e retornar o resultado.
- Handlers **não devem** chamar outros handlers via `IMediator` — crie um serviço compartilhado se necessário.
- Todo `Command` que modifica estado **deve** ter um `Validator` correspondente com FluentValidation.
- Handlers não devem lançar exceções genéricas — use o padrão de resultado do projeto (ex: `Result<T>`, `OneOf`, ou exceções de domínio específicas).

---

## Entity Framework

- Sempre usar `.AsNoTracking()` em queries de leitura que não vão modificar as entidades.
- Nunca expor `DbContext` fora da camada `Infrastructure`. Use repositórios ou `IUnitOfWork`.
- Operações que envolvem múltiplas entidades em uma mesma transação devem usar `IDbContextTransaction` ou `IUnitOfWork`.
- Evitar `Include()` em cadeia excessiva — prefira projeções com `Select()` para queries de leitura.
- Migrations devem ser revisadas separadamente e nunca misturadas com código de feature na mesma PR.
- Não usar `Lazy Loading` — todas as relações devem ser carregadas explicitamente.
- Sempre que trabalhar com listas utilizar EF.Constant para manter a compatibilidade, e aumentar performance.

---

## FluentValidation

- Toda validação de entrada deve estar no `Validator`, nunca no `Handler` ou `Controller`.
- Validadores devem cobrir: campos obrigatórios, tamanhos máximos, formatos (email, CPF, etc.) e regras de negócio simples.
- Validações que exigem acesso ao banco (ex: verificar se e-mail já existe) devem ser feitas no `Handler`, não no `Validator`.

---

## Segurança

- Endpoints que retornam ou modificam dados de usuário **devem** ter `[Authorize]` ou política equivalente.
- Nunca logar senhas, tokens, CPF, cartão de crédito ou qualquer dado sensível.
- Toda query que recebe input do usuário deve usar parâmetros — nunca concatenação de strings em SQL.
- Claims do usuário autenticado devem ser lidas via `ICurrentUserService` ou equivalente, nunca diretamente de `HttpContext` fora da camada `API`.

---

## Async / Performance

- Todos os métodos que fazem I/O (banco, HTTP, arquivo) devem ser `async`/`await` do início ao fim da cadeia.
- Proibido usar `.Result`, `.Wait()` ou `.GetAwaiter().GetResult()` em código de produção.
- `CancellationToken` deve ser propagado em todos os métodos públicos que fazem I/O.
- Listas grandes devem sempre ter paginação — nunca retornar coleções sem limite.
- Todo método criado ou modificado e for Async deve ter o Sufixo no fincal indicando que ele é Async EX: CriarContaAsync

---

## Tratamento de Erros

- Não usar `catch (Exception ex) { }` vazio — sempre logar ou relançar.
- Exceptions de domínio (ex: `NotFoundException`, `BusinessRuleException`) devem ser capturadas no middleware global de erros, não espalhadas pelo código.
- Não retornar `null` de métodos que podem não encontrar resultado — use `Result<T>`, `Option<T>`, ou lance exceção de domínio específica.

---

## Testes (para arquivos de teste modificados na PR)

- Todo novo caso de uso (`Command`/`Query`) deve ter pelo menos um teste unitário no `Handler`.
- Testes não devem depender de ordem de execução.
- Mocks devem ser criados com a biblioteca padrão do projeto (ex: Moq, NSubstitute).
- Nomes de teste devem seguir o padrão: `Metodo_Cenario_ResultadoEsperado`.

---

## O que NÃO reportar

- Warnings de nullable em código legado já existente que não foi modificado na PR.
