# 07. Другие подборки

[← к оглавлению](../README.md)

> **Важно: мы это не проверяли.**
>
> Всё ниже — чужие репозитории. Мы убедились только в том, что ссылка
> открывается, а описание и лицензия взяты из метаданных GitHub на 30 сентября
> 2026 года. Содержимое скиллов мы не читали, качество не оценивали,
> безопасность не проверяли.
>
> **Скилл — это инструкция, которую ваш агент выполнит.** Прежде чем ставить
> что-то отсюда, откройте `SKILL.md` и прочитайте его целиком. Особенно —
> `allowed-tools` и всё, что запускает скрипты или ходит в сеть. Ставить
> чужой скилл не глядя примерно так же разумно, как запускать чужой
> `install.sh` не глядя.

---

## Подборки скиллов для исследования

| Репозиторий | О чём | Лицензия |
|---|---|---|
| [Yila-AI/awesome-research-skills](https://github.com/Yila-AI/awesome-research-skills) | Открытые Agent Skills для планирования, написания и доводки статей уровня SCI/SSCI, с упором на сохранение доказательной базы и силы утверждений | Apache-2.0 |
| [neverbiasu/awesome-research-skills](https://github.com/neverbiasu/awesome-research-skills) | Большой каталог `SKILL.md` для академической работы: литература, дизайн исследования, эксперименты, статистика, графики, письмо, рецензия. Каждая запись — с указанием лицензии и свежести | CC0-1.0 |
| [kael-odin/awesome-academic-research-skills](https://github.com/kael-odin/awesome-academic-research-skills) | Автообновляемый рейтинг научных скиллов для Claude Code / Codex / OpenCode. Интерфейс на китайском | MIT |
| [Jason-Mar1/awesome-academic-research-skills](https://github.com/Jason-Mar1/awesome-academic-research-skills) | Двуязычная карта скиллов по направлениям: письмо, литература, эксперименты, графики, рецензия, привязка к дисциплинам | CC0-1.0 |
| [fengmo11/awesome-paper-research-skills](https://github.com/fengmo11/awesome-paper-research-skills) | Подборка вокруг статьи: поиск идей, литература, эксперименты, цитирование, LaTeX/DOCX, рецензия, подача | MIT |
| [xjtulyc/awesome-rosetta-skills](https://github.com/xjtulyc/awesome-rosetta-skills) | Универсальный набор исследовательских скиллов с претензией на все дисциплины; Claude Code, Codex, Gemini CLI, Cursor | указана как «прочая» |

## Наборы скиллов (не списки, а сами скиллы)

| Репозиторий | О чём | Лицензия |
|---|---|---|
| [Orchestra-Research/AI-Research-SKILLs](https://github.com/Orchestra-Research/AI-Research-SKILLs) | Библиотека скиллов для AI-исследований и инженерии | MIT |
| [Master-cai/Research-Paper-Writing-Skills](https://github.com/Master-cai/Research-Paper-Writing-Skills) | Написание статей по ML/CV/NLP; собрано по открытым заметкам проф. Пэн Сыда | MIT |
| [zLanqing/codex-claude-academic-skills](https://github.com/zLanqing/codex-claude-academic-skills) | Три скилла под полный цикл: чтение литературы и генерация отчётов, письмо и ответы рецензентам, научные вычисления и графики. Ориентирован на китайскоязычных пользователей | MIT |
| [wanshuiyin/Auto-claude-code-research-in-sleep](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep) | Автономные ML-эксперименты и кросс-модельные циклы рецензирования, только markdown, без фреймворка | MIT |
| [Imbad0202/academic-research-skills](https://github.com/Imbad0202/academic-research-skills) | Цепочка research → write → review → revise → finalize для Claude Code | указана как «прочая» |
| [sangwonme/ClaudeCode-For-Researcher](https://github.com/sangwonme/ClaudeCode-For-Researcher) | Небольшой набор скиллов и хуков под исследовательскую работу | не указана |

## Смежное

| Репозиторий | О чём | Лицензия |
|---|---|---|
| [anthropics/skills](https://github.com/anthropics/skills) | Официальные скиллы Anthropic. Лучший образец того, как пишется `SKILL.md` | не указана в метаданных репозитория |
| [hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) | Общая большая подборка по Claude Code: скиллы, субагенты, хуки, инструменты | указана как «прочая» |
| [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) | Референсные MCP-серверы | — |
| [punkpeye/awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) · [wong2/awesome-mcp-servers](https://github.com/wong2/awesome-mcp-servers) | Каталоги MCP-серверов | — |
| [trailofbits/skills](https://github.com/trailofbits/skills) | Скиллы для аудита безопасности от Trail of Bits. Не про науку, но полезный образец аккуратно написанных скиллов | CC-BY-SA-4.0 |

---

## Как выбирать из этого

1. **Читайте `SKILL.md`, а не README.** README пишут для звёздочек, `SKILL.md` —
   то, что реально попадёт в вашего агента.
2. **Смотрите дату последнего коммита.** Инструменты меняются каждый месяц;
   скилл годичной давности может ссылаться на несуществующие возможности.
3. **Смотрите лицензию.** Если её нет — по умолчанию у вас нет прав
   использовать код. Для `SKILL.md` это чаще всего неважно на практике, но если
   вы тащите чужое в свой публичный репозиторий — важно.
4. **Проверяйте `allowed-tools` и скрипты.** Скилл может выдать себе широкие
   права. Особенно смотрите на сетевые вызовы и на всё, что пишет вне проекта.
5. **Лучше написать свой.** Скилл — это полстраницы markdown. Чужой скилл
   описывает чужой процесс; ваш будет описывать ваш. Начните с
   [примера в этом репозитории](../skills/lit-note/SKILL.md).

---

[← 06. Грабли и этика](../06-pitfalls/README.md) · [к оглавлению](../README.md)
