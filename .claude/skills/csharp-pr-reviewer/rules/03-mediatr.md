# Regras — MediatR

- Todo caso de uso deve ser implementado como um `Command` ou `Query` com seu respectivo `Handler`.
- Controllers devem apenas receber a requisição, mapear para um Command/Query, disparar via `IMediator` e retornar o resultado.
- Handlers **não devem** chamar outros handlers via `IMediator` — crie um serviço compartilhado se necessário.
- Todo `Command` que modifica estado **deve** ter um `Validator` correspondente com FluentValidation.
- Handlers não devem lançar exceções genéricas — use o padrão de resultado do projeto (ex: `Result<T>`, `OneOf`, ou exceções de domínio específicas).
- Handlers não devem fazer coisas demais (God Handler) — se um handler cresce além de uma única responsabilidade, extraia serviços de domínio.
- Todo Handler deve ter tipo de retorno consistente e explícito — evitar retornar `object`, `dynamic` ou misturar `void`/`Task` sem motivo.
