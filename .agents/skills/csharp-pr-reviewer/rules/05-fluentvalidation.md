# Regras — FluentValidation

- Toda validação de entrada deve estar no `Validator`, nunca no `Handler` ou `Controller`.
- Validadores devem cobrir: campos obrigatórios, tamanhos máximos, formatos (email, CPF, etc.) e regras de negócio simples.
- Validações que exigem acesso ao banco (ex: verificar se e-mail já existe) devem ser feitas no `Handler`, não no `Validator`.
