# Regras — Nomenclatura

- **Commands**: sufixo `Command` (ex: `CreateOrderCommand`), resposta com sufixo `Response` quando necessário.
- **Queries**: sufixo `Query` (ex: `GetOrderByIdQuery`).
- **Handlers**: sufixo `Handler` (ex: `CreateOrderCommandHandler`).
- **Validators**: sufixo `Validator` (ex: `CreateOrderCommandValidator`).
- **Repositories**: prefixo `I` para interface + sufixo `Repository` (ex: `IOrderRepository`, `OrderRepository`).
- **Services de domínio**: sufixo `Service` (ex: `PricingService`).
- **Controllers**: sufixo `Controller`, nunca deve conter lógica de negócio.
- **DTOs**: sufixo `Dto` ou `Request`/`Response` conforme contexto.
- **Métodos async**: sufixo `Async` obrigatório em todo método criado ou modificado que for assíncrono (ex: `CriarContaAsync`, `BuscarPedidoAsync`).
