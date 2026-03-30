# 🚫 no-skills

> Não tem nada aqui. Só uma coleção de skills e prompts do Claude Code que uso no dia a dia. Pode seguir em frente.

---

## Skills disponíveis

| Skill                                                      | Descrição                                                                                                        |
| ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| [`csharp-pr-reviewer`](.claude/skills/csharp-pr-reviewer/) | Revisão profunda de código C# .NET — lê o `git diff` contra `develop` e reporta apenas achados Médios e Críticos |

---

## `csharp-pr-reviewer`

Revisor automático de código para backends C# .NET. Lê o diff da branch atual em relação à `develop` via `git diff`, aplica um conjunto de regras configurável e gera um relatório estruturado com apenas achados **Médios** e **Críticos** — sem ruído, sem sugestões óbvias.

**O que analisa:**

- 🔐 **Segurança** — SQL injection, `[Authorize]` faltando, secrets hardcoded, mass assignment
- ⚡ **Performance** — queries N+1, `.AsNoTracking()` ausente, async bloqueante (`.Result`, `.Wait()`), falta de paginação, listas sem `EF.Constant`
- 🧱 **Qualidade de código** — violações de SOLID, exceptions engolidas, riscos de nullable, código morto
- 📦 **MediatR** — lógica de negócio fora dos handlers, validators faltando, God handlers
- 🗄️ **Entity Framework** — lazy loading, transações faltando, `DbContext` vazando para fora da Infrastructure
- 🏛️ **Arquitetura** — violações de dependência entre camadas, anti-patterns de Clean Architecture / DDD

**Pré-requisitos:**

- Git com branch `develop` como base
- [Claude Code](https://claude.ai/claude-code) com suporte a skills

**Como usar:**

Estando na sua branch de feature, basta pedir a revisão no Claude Code:

```
revisa o código
```

```
analisa o código
```

```
o que tem de errado aqui?
```

```
faz um code review da branch atual
```

**Regras customizadas:**

Coloque um `REVIEW_RULES.md` na raiz do seu projeto (ou em `docs/REVIEW_RULES.md` / `.claude/REVIEW_RULES.md`) para sobrescrever as regras padrão com as convenções do seu projeto. A skill detecta o arquivo automaticamente e informa qual foi usado no topo do relatório.

Um [template pronto para editar](.claude/skills/csharp-pr-reviewer/REVIEW_RULES.md) está incluído, cobrindo:

- Regras de camadas do Clean Architecture
- Convenções de nomenclatura (Commands, Queries, Handlers, Validators, Repositories)
- Padrões de query com EF Core (incluindo `EF.Constant` para listas)
- Onde colocar validações com FluentValidation
- Requisitos de async, `CancellationToken` e sufixo `Async`
- Tratamento de erros e exceções de domínio
- O que **não** reportar (ex: warnings de nullable em código legado)

---

## Instalação

Copie a skill para a pasta `.claude` do seu projeto ou para o diretório global do Claude Code:

```bash
# Por projeto
cp -r .claude/skills/csharp-pr-reviewer /seu/projeto/.claude/skills/

# Global
cp -r .claude/skills/csharp-pr-reviewer ~/.claude/skills/
```

Copie e edite o template de regras:

```bash
cp .claude/skills/csharp-pr-reviewer/REVIEW_RULES.md /seu/projeto/REVIEW_RULES.md
```

---

## Licença

[MIT](LICENSE)
