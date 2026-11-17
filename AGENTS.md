# AGENTS.md

Diretrizes para agentes de IA que trabalham neste repositório (git-skills).

## Idioma do código

Todo o código deve ser escrito em **português (pt-BR)**:

## Commits

Todo commit deve seguir as skills **`/git-commit`** e **`/git-conventional`**:

- Usar `/git-commit` para analisar o diff, preparar (stage) apenas o que pertence ao commit e criar o commit.
- Usar `/git-conventional` para formatar a mensagem no padrão Conventional Commits: `tipo(escopo opcional): descrição`.
- Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- Descrição em português, no imperativo/curta (até ~72 caracteres), sem ponto final.
- Mudanças que quebram compatibilidade: usar `!` após o tipo/escopo e/ou rodapé `BREAKING CHANGE:`.
- Um commit por mudança lógica; nunca misturar assuntos não relacionados.
