# Regras — Segurança

- Endpoints que retornam ou modificam dados de usuário **devem** ter `[Authorize]` ou política equivalente.
- Nunca logar senhas, tokens, CPF, cartão de crédito ou qualquer dado sensível.
- Toda query que recebe input do usuário deve usar parâmetros — nunca concatenação de strings em SQL.
- Claims do usuário autenticado devem ser lidas via `ICurrentUserService` ou equivalente, nunca diretamente de `HttpContext` fora da camada `API`.
- Nunca usar desserialização insegura de tipos arbitrários (ex: `BinaryFormatter`, `TypeNameHandling.All` no JSON).
- Nunca expor propriedades desnecessárias em DTOs de entrada que permitam mass assignment — use `[BindNever]` ou DTOs explícitos com apenas os campos permitidos.
