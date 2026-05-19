# Regras — Qualidade de Código

- Evitar violações de SOLID: God classes, violações de SRP, acoplamento excessivo, abstrações ausentes onde necessário.
- Não usar magic numbers ou magic strings soltos no código — extraia para constantes nomeadas ou valores de configuração.
- Atenção a riscos de nullable reference: null checks ausentes, uso indevido do operador `!` (null-forgiving) sem justificativa.
- Não deixar código morto ou blocos comentados no código de produção.
