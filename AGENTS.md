# Codex Project Instructions

This repository stores Codex skills.

## Structure

- Put project-local skills under `.agents/skills/<skill-name>/`.
- Every skill must include `SKILL.md` with YAML frontmatter containing `name` and `description`.
- Prefer adding `agents/openai.yaml` for Codex UI metadata.
- Put reusable review rules, references, scripts, and assets inside the relevant skill folder.

## Maintenance

- Keep skill names lowercase hyphen-case.
- Keep `SKILL.md` focused on execution instructions. Move detailed rules into bundled resources.
- Do not add README or installation guides inside individual skill folders.
- When changing a skill, run the skill validator if available:

```powershell
C:\Users\felip\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe C:\Users\felip\.codex\skills\.system\skill-creator\scripts\quick_validate.py .agents\skills\<skill-name>
```

If the bundled Python runtime does not include `PyYAML`, validate manually that the frontmatter has only supported keys and that the folder name matches `name`.
