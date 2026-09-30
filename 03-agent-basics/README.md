# 03. Механики: чем агент расширяется

[← к оглавлению](../README.md)

Шесть механик. Ставьте их по мере надобности, а не все сразу: каждая — это
ещё немного контекста и ещё немного того, что может сломаться.

Порядок освоения примерно такой:
**режим plan → разрешения → скиллы → субагенты → MCP → хуки.**

| Механика | Когда нужна | Одной строкой |
|---|---|---|
| [Режим plan](#режим-plan) | Всегда, с первого дня | Агент думает, но не пишет, пока вы не утвердили |
| [Разрешения и песочница](#разрешения-и-песочница) | Всегда | Что агент может делать, не спрашивая |
| [Скиллы](#скиллы) | Повторяете одну процедуру | Многошаговая инструкция в файле, грузится по надобности |
| [Субагенты](#субагенты) | Большая или параллельная работа | Отдельный агент со своим чистым контекстом |
| [MCP](#mcp) | Нужен внешний сервис | Стандартный разъём для внешних инструментов и данных |
| [Хуки](#хуки) | Правило должно соблюдаться железно | Скрипт, который срабатывает автоматически |

---

## Режим plan

Агент читает, ищет, думает и формулирует план — но не трогает файлы, пока вы
план не утвердите. Это первое, что стоит включить, и последнее, что стоит
выключать.

```bash
claude --permission-mode plan
```

В запущенной сессии режим переключается Shift+Tab. Для больших задач полезнее
попросить план в файл (`PLAN.md`) и поправить его руками — правки в файле агент
увидит, правки в чате забудет.

→ [Choose a permission mode](https://code.claude.com/docs/en/permission-modes)

## Разрешения и песочница

Агент запускает команды в вашей системе. Вопрос не «доверяете ли вы ему», а
«что произойдёт при ошибке».

Два независимых уровня:

- **Режим разрешений** — спрашивает ли агент перед действием. От «спрашивать
  про всё» до «не спрашивать ничего». Поверх режима кладутся правила: что
  разрешено всегда, что запрещено всегда (запрет сильнее всего).
- **Песочница** — что действие может достать, когда уже запустилось: какие
  файлы, какая сеть.

Разумный старт для исследователя:

- разрешить без вопросов чтение, `git status`, `git diff`, запуск тестов и
  сборку PDF;
- запретить наглухо: `git push`, любые `rm -rf`, отправку чего-либо наружу,
  доступ к каталогам с персональными данными;
- всё остальное — спрашивать.

Правила живут в `.claude/settings.json` и коммитятся вместе с проектом, так что
соавторы получают ту же настройку.

→ [Configure permissions](https://code.claude.com/docs/en/permissions) ·
[Sandboxing](https://code.claude.com/docs/en/sandboxing) ·
[Choose a sandbox environment](https://code.claude.com/docs/en/sandbox-environments)
· Codex: [Permissions](https://learn.chatgpt.com/docs/permission-modes),
[Sandbox](https://learn.chatgpt.com/docs/sandboxing),
[Agent approvals & security](https://learn.chatgpt.com/docs/agent-approvals-security)

## Скиллы

Скилл — это папка с файлом `SKILL.md`: инструкция на несколько шагов, которую
агент подгружает **только когда она нужна**. В этом весь смысл: в отличие от
`CLAUDE.md`, длинный скилл ничего не стоит, пока не вызван.

Когда заводить скилл: вы третий раз вставляете в чат одну и ту же простыню
инструкций, или раздел в `CLAUDE.md` разросся из «факта» в «процедуру».

### Куда класть

| Путь | Область |
|---|---|
| `~/.claude/skills/<имя>/SKILL.md` | Ваш личный, во всех проектах |
| `.claude/skills/<имя>/SKILL.md` | Проектный, едет с репозиторием |

Для Codex скиллы описаны здесь: [Build skills](https://learn.chatgpt.com/docs/build-skills).

### Как выглядит

```markdown
---
name: check-citations
description: Сверяет все \cite в тексте с .bib и проверяет DOI через Crossref.
  Использовать перед подачей и когда просят проверить ссылки.
disable-model-invocation: true
---

## Шаги

1. Собери все ключи `\cite*{...}` из `paper/*.tex`.
2. Сверь с ключами в `paper/refs.bib` — выведи недостающие и неиспользуемые.
3. Для каждой записи с DOI дёрни `https://api.crossref.org/works/<DOI>`
   и сверь заголовок, год и первого автора.
4. Отчёт таблицей: ключ | статус | что не сошлось. Ничего не правь сам.
```

Обязателен только `description` — по нему агент решает, когда скилл уместен.
Пишите его конкретно: не «про ссылки», а «когда просят проверить ссылки перед
подачей». `disable-model-invocation: true` означает «запускаю только я, вручную,
через `/check-citations`» — ставьте это всему, у чего есть побочные эффекты.

Полный список полей — в [документации](https://code.claude.com/docs/en/skills#frontmatter-reference).
Формат `SKILL.md` — открытый стандарт: [agentskills.io](https://agentskills.io),
[спецификация](https://agentskills.io/specification).

Рабочий пример для исследователя лежит в этом репозитории:
[`skills/lit-note/SKILL.md`](../skills/lit-note/SKILL.md).

→ [Extend Claude with skills](https://code.claude.com/docs/en/skills) ·
[Agent Skills (API)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) ·
[Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) ·
[Equipping agents for the real world with Agent
Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) ·
[anthropics/skills](https://github.com/anthropics/skills)

## Субагенты

Субагент — отдельный агент, которого основной запускает на подзадачу. У него
свой чистый контекст: он не тащит в себя весь ваш разговор и не засоряет
основную сессию.

Зачем это исследователю:

- **Параллель.** Три рецензента на вашу статью одновременно, каждый со своим
  углом зрения — и ни один не видит, что написали другие. Это не трюк, а
  единственный способ получить независимые оценки от одной модели.
- **Экономия контекста.** «Прошерсти 200 PDF и выпиши те, где есть контрольная
  группа» — тяжёлая работа, результат которой должен вернуться в двадцать строк.
- **Разделение ролей.** Один пишет, другой критикует и не знает, что писавший
  имел в виду.

Канонический пример — параллельная рецензия:
[AlexWortega/ai-peer-review-skill](https://github.com/AlexWortega/ai-peer-review-skill)
(MIT). Скилл запускает несколько субагентов-рецензентов на вашу статью и потом
сводит их отзывы в мета-ревью. Адаптация
[poldrack/ai-peer-review](https://github.com/poldrack/ai-peer-review), где ту
же схему делают на нескольких разных моделях. Посмотрите его `SKILL.md` и
`prompts/` — это лучший учебник по теме на две страницы.

→ [Create custom subagents](https://code.claude.com/docs/en/sub-agents) ·
[Run agents in parallel](https://code.claude.com/docs/en/agents) ·
Codex: [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)

## MCP

Model Context Protocol — открытый стандарт, как подключать к агенту внешние
инструменты и источники данных. Вместо самописного клиента к каждому сервису —
один разъём.

Что из этого полезно в исследовании: доступ к Zotero, к базам статей, к
лабораторному хранилищу, к трекеру задач, к базе данных. Список готовых
серверов — [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
и большие community-каталоги ([punkpeye](https://github.com/punkpeye/awesome-mcp-servers),
[wong2](https://github.com/wong2/awesome-mcp-servers)).

Предупреждение: **MCP-сервер — это код, который вы пускаете к своим данным.**
Ставьте только то, чей источник вы понимаете. Для чужих серверов держите
отдельный профиль без доступа к чувствительным каталогам.

Отдельно стоит знать: часто вместо MCP достаточно обычного CLI. Если у сервиса
есть `curl`-able API, агент прекрасно сходит туда сам, и это проще и прозрачнее.

→ [modelcontextprotocol.io](https://modelcontextprotocol.io/) ·
[Introduction](https://modelcontextprotocol.io/docs/getting-started/intro) ·
[Connect to MCP servers](https://code.claude.com/docs/en/mcp-quickstart) ·
[MCP в Claude Code](https://code.claude.com/docs/en/mcp) ·
Codex: [Model Context Protocol](https://learn.chatgpt.com/docs/extend/mcp) ·
[Code execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp)

## Хуки

Хук — скрипт, который выполняется автоматически в определённый момент: перед
вызовом инструмента, после правки файла, в конце сессии. В отличие от записи в
`CLAUDE.md`, хук не «просьба», а механизм: он сработает независимо от того, что
агент решил.

Для исследователя это способ сделать правило железным:

- перед любым `Bash` — блокировать команды, трогающие `data/raw/`;
- после правки `.tex` — прогнать `latexmk` и вернуть агенту ошибки сборки;
- после правки `.py` — прогнать линтер и тесты;
- при попытке записи вне проекта — отказ.

Правило простое: **что должно соблюдаться всегда — в хук, а не в память
проекта.** Память агент трактует, хук — исполняет.

→ [Automate actions with hooks](https://code.claude.com/docs/en/hooks-guide) ·
[Hooks reference](https://code.claude.com/docs/en/hooks) ·
Codex: [Hooks](https://learn.chatgpt.com/docs/hooks)

---

## Полезное сверх этого

- [Commands](https://code.claude.com/docs/en/commands) — встроенные команды:
  `/context` (чем забит контекст), `/compact` (сжать разговор), `/cost`.
- [Manage costs effectively](https://code.claude.com/docs/en/costs) — куда уходят деньги.
- [Run parallel sessions with worktrees](https://code.claude.com/docs/en/worktrees) —
  две задачи в одном репозитории, не мешая друг другу.
- [Run Claude Code programmatically](https://code.claude.com/docs/en/headless) —
  агент внутри скрипта или пайплайна, без интерактива.
- [Plugins overview](https://code.claude.com/docs/en/plugins/overview) — как
  раздавать коллегам набор скиллов, субагентов и хуков одним пакетом.
- [Glossary](https://code.claude.com/docs/en/glossary) — если термины путаются.

---

[← 02. Память проекта](../02-memory/README.md) · [к оглавлению](../README.md) · [дальше: 04. Инструменты →](../04-tools/README.md)
