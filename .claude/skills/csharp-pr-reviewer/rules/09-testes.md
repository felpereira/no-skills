# Regras — Testes (para arquivos de teste modificados na PR)

- Todo novo caso de uso (`Command`/`Query`) deve ter pelo menos um teste unitário no `Handler`.
- Testes não devem depender de ordem de execução.
- Mocks devem ser criados com a biblioteca padrão do projeto (ex: Moq, NSubstitute).
- Nomes de teste devem seguir o padrão: `Metodo_Cenario_ResultadoEsperado`.
