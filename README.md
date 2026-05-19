# no-skills

Coleção de skills do Codex para uso no dia a dia.

---

## Skills

| Skill | Descrição |
|---|---|
| [`csharp-pr-reviewer`](.agents/skills/csharp-pr-reviewer/) | Revisão de código C# .NET — analisa o diff contra `develop` e reporta achados Médios e Críticos |

---

## csharp-pr-reviewer

Revisor automático para backends C# .NET. Analisa o diff da branch atual via `git diff develop`, aplica as regras do projeto e gera um relatório com achados **Médios** e **Críticos**.

Cobre: segurança, performance, qualidade de código, MediatR, Entity Framework, arquitetura e organização.

Ao final do relatório, você pode pedir para o Codex corrigir os problemas encontrados diretamente nos arquivos.

**Uso** — estando na sua branch de feature:

```
revisa o código
```

**Instalação:**

```bash
# Por projeto
cp -r .agents/skills/csharp-pr-reviewer /seu/projeto/.agents/skills/

# Global
cp -r .agents/skills/csharp-pr-reviewer ~/.codex/skills/
```

**Regras customizadas:**

Copie a pasta `rules/` para a raiz do seu projeto e edite os arquivos conforme as convenções do time. Cada arquivo cobre uma categoria de regras independente.

```bash
cp -r .agents/skills/csharp-pr-reviewer/rules /seu/projeto/rules
```

---

## Licença

[MIT](LICENSE)
