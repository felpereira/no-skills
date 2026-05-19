# Regras — Tratamento de Erros

- Não usar `catch (Exception ex) { }` vazio — sempre logar ou relançar.
- Exceptions de domínio (ex: `NotFoundException`, `BusinessRuleException`) devem ser capturadas no middleware global de erros, não espalhadas pelo código.
- Não retornar `null` de métodos que podem não encontrar resultado — use `Result<T>`, `Option<T>`, ou lance exceção de domínio específica.
