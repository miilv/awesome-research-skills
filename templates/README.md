# Шаблоны

[← к оглавлению](../README.md)

Заготовки для исследовательского проекта. Копируйте и правьте — они намеренно
короткие: длинный файл памяти работает хуже короткого.

| Файл | Куда класть |
|---|---|
| [`AGENTS.md.example`](AGENTS.md.example) | `AGENTS.md` в корне проекта (Codex и Claude Code) |
| [`CLAUDE.md.example`](CLAUDE.md.example) | `CLAUDE.md` в корне проекта (Claude Code) |
| [`repo-structure.md`](repo-structure.md) | Не копируется — это описание того, как разложить проект |
| [`decisions/0001-example.md`](decisions/0001-example.md) | `docs/decisions/` — журнал решений |
| [`experiments/EXPERIMENT.md`](experiments/EXPERIMENT.md) | `experiments/` — лог прогонов |

> Файлы названы `*.example`, чтобы агент не подхватил их как настоящую память
> этого репозитория. Копируя к себе, переименуйте в `AGENTS.md` / `CLAUDE.md`.

## Если вы пользуетесь и Claude Code, и Codex

Держите один файл, а не два расходящихся:

```bash
cp templates/AGENTS.md.example AGENTS.md
ln -s AGENTS.md CLAUDE.md
```

Claude Code умеет читать `AGENTS.md` напрямую — тогда симлинк не нужен вовсе.
Подробности: [How Claude remembers your
project](https://code.claude.com/docs/en/memory).

## С чего начать, если лень

Запустите `/init` в Claude Code: агент осмотрит репозиторий и сделает
черновик. Потом откройте эти шаблоны и допишите то, чего он знать не мог —
договорённости, требования площадки, запреты.

---

[← к оглавлению](../README.md) · [подробно о памяти проекта →](../02-memory/README.md)
