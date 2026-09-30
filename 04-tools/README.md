# 04. Инструменты с CLI или API

[← к оглавлению](../README.md)

Агент силён ровно настолько, насколько силён инструментарий, до которого он
дотягивается. Критерий отбора здесь один: **у инструмента есть командная строка
или HTTP API**, то есть агент может им пользоваться сам, без вас.

Если у сервиса только веб-интерфейс — агенту он бесполезен, каким бы хорошим
ни был.

---

## Поиск и метаданные литературы

Четыре открытых API покрывают почти всё. Все четыре работают без ключа (для
части — с ключом выше лимиты), отдают JSON, вызываются одним `curl`.

| Инструмент | Зачем исследователю | Ссылка |
|---|---|---|
| **arXiv API** | Поиск и метаданные препринтов: по автору, по категории, по дате. Агент может каждое утро проверять новое в вашей области и складывать в `notes/`. | [info.arxiv.org/help/api](https://info.arxiv.org/help/api/index.html), [user manual](https://info.arxiv.org/help/api/user-manual.html) |
| **Crossref REST API** | Источник истины по DOI: заглавие, авторы, год, журнал. Главный инструмент проверки ссылок — сверяйте с ним каждую запись `.bib`. | [документация](https://www.crossref.org/documentation/retrieve-metadata/rest-api/), [swagger](https://api.crossref.org/swagger-ui/index.html), [поиск](https://search.crossref.org/) |
| **OpenAlex** | Открытый граф науки: работы, авторы, организации, цитирования. Тут удобно строить сети цитирования и считать, кого вы не процитировали. Без ключа, щедрые лимиты. | [docs.openalex.org](https://docs.openalex.org/) |
| **Semantic Scholar API** | Поиск по смыслу, references/citations одной статьи, TL;DR-аннотации. Хорош для «найди, что цитирует эту работу и делает то же на других данных». | [api-docs](https://api.semanticscholar.org/api-docs/), [о продукте](https://www.semanticscholar.org/product/api) |

Дополнительно:

- **[Unpaywall](https://www.unpaywall.org/)** — есть ли легальная открытая версия
  статьи по DOI. Полезно, когда агенту нужно не только метаданные, но и текст.
- **[OpenReview](https://openreview.net/)** — рецензии и обсуждения на
  ML-конференциях, есть API. Бесценно, чтобы понять, за что режут работы вроде
  вашей.
- **[ROR](https://ror.org/)** — справочник организаций, если нужно чистить
  аффилиации.
- **[Connected Papers](https://www.connectedpapers.com/)** — граф близких работ.
  Веб-сервис, агенту напрямую не доступен, но полезен вам.
- **[Hugging Face Papers](https://huggingface.co/papers)** — лента статей с
  кодом и обсуждением (сюда переехал Papers with Code).
- **[NCBI E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25501/)** — API к
  PubMed и остальным базам NCBI.

## Библиография

| Инструмент | Зачем | Ссылка |
|---|---|---|
| **Zotero** | Менеджер литературы. Главное для нас — локальная база и [Web API](https://www.zotero.org/support/dev/web_api/v3/start), то есть агент может читать вашу библиотеку. | [zotero.org](https://www.zotero.org/) |
| **Better BibTeX** | Плагин к Zotero: стабильные ключи цитирования и автоэкспорт `.bib`, который сам обновляется при изменении библиотеки. Без него ключи «плывут» и `.tex` ломается. | [retorque.re/zotero-better-bibtex](https://retorque.re/zotero-better-bibtex/) |
| **CSL styles** | Стили оформления библиографии для конкретных журналов — используются pandoc и Zotero. | [citation-style-language/styles](https://github.com/citation-style-language/styles), [citationstyles.org](https://citationstyles.org/) |
| **doi.org** | Резолвер DOI. Быстрая проверка «эта ссылка вообще существует». | [doi.org](https://www.doi.org/) |

Рабочая схема: Better BibTeX держит `paper/refs.bib` в актуальном состоянии,
агент правит только `.tex` и никогда `.bib` руками, а проверка ссылок идёт
через Crossref. Скилл для такой проверки — в
[03. Механики](../03-agent-basics/README.md#скиллы).

## Разбор PDF

Агент не умеет читать PDF так, как читаете вы: нужен слой, превращающий PDF в
структурированный текст. Три варианта, от простого к тяжёлому.

| Инструмент | Зачем | Ссылка |
|---|---|---|
| **docling** | Универсальный конвертер документов в markdown/JSON: сохраняет структуру, таблицы, порядок чтения. Хороший выбор по умолчанию, ставится как обычный Python-пакет. | [docling-project/docling](https://github.com/docling-project/docling), [docs](https://docling-project.github.io/docling/) |
| **marker** | PDF → markdown с упором на качество: формулы, таблицы, многоколоночная вёрстка. Тяжелее, но на научных PDF часто точнее. | [datalab-to/marker](https://github.com/datalab-to/marker) |
| **GROBID** | Заточен именно под научные статьи: вытаскивает заголовок, авторов, аффилиации, разделы и — главное — **структурированный список литературы** с привязкой к цитатам в тексте. Запускается сервисом (Docker), отдаёт TEI XML. | [kermitt2/grobid](https://github.com/kermitt2/grobid), [docs](https://grobid.readthedocs.io/en/latest/) |

Практика: GROBID — когда нужны ссылки и метаданные пачки статей; docling или
marker — когда нужен читаемый текст, чтобы агент разобрался в содержании.

## Текст и вёрстка

| Инструмент | Зачем | Ссылка |
|---|---|---|
| **pandoc** | Конвертер всего во всё: markdown ↔ LaTeX ↔ docx ↔ HTML, с библиографией по CSL. Когда соавтор требует `.docx`, а вы пишете в LaTeX — это pandoc в одну строку. | [pandoc.org](https://pandoc.org/), [MANUAL](https://pandoc.org/MANUAL.html), [исходники](https://github.com/jgm/pandoc) |
| **LaTeX / TeX Live** | Стандарт вёрстки. Агенту удобен тем, что это plain text под git: видно diff, видно, что изменилось в формуле. | [latex-project.org](https://www.latex-project.org/), [TeX Live](https://www.tug.org/texlive/) |
| **latexmk** | Одна команда вместо ручного цикла latex/bibtex/latex/latex. Агент запускает `latexmk -pdf` и читает лог сборки — так он сам чинит свои ошибки. | [CTAN](https://ctan.org/pkg/latexmk) |
| **Overleaf через git** | У проекта Overleaf есть git-remote: клонируете, работаете локально агентом, пушите — соавторы видят изменения в браузере. Так агент работает с Overleaf, не имея к нему доступа. | [Git integration](https://www.overleaf.com/learn/how-to/Git_integration) |
| **Quarto** | Исполняемые документы: текст, код и результаты в одном файле, сборка в PDF/HTML/docx/слайды. Хорошая альтернатива связке Jupyter + LaTeX. | [quarto.org](https://quarto.org/) |
| **Typst** | Современная замена LaTeX: быстрее, понятнее ошибки, тоже plain text. Уже принимается частью площадок; проверяйте требования вашей. | [typst.app/docs](https://typst.app/docs/), [исходники](https://github.com/typst/typst) |

## Вычисления и окружение

| Инструмент | Зачем | Ссылка |
|---|---|---|
| **uv** | Быстрый менеджер пакетов и окружений Python. `uv sync` воспроизводит окружение по lock-файлу — это и есть воспроизводимость на практике. Агенту достаточно одной команды. | [docs.astral.sh/uv](https://docs.astral.sh/uv/), [astral-sh/uv](https://github.com/astral-sh/uv) |
| **conda** | Когда нужны не-Python зависимости (компиляторы, GDAL, R) — привычный стандарт во многих областях. | [docs.conda.io](https://docs.conda.io/) |
| **Jupyter** | Ноутбуки. Агент умеет их читать и править, но для git лучше держать рядом **jupytext**. | [jupyter.org](https://jupyter.org/) |
| **jupytext** | Синхронизирует `.ipynb` с обычным `.py`/`.md`. Тогда diff читаемый, а не стена JSON — критично, когда ноутбук правит агент. | [jupytext.readthedocs.io](https://jupytext.readthedocs.io/) |
| **nbconvert** | Ноутбук → PDF/HTML/скрипт. Для приложения к статье. | [nbconvert.readthedocs.io](https://nbconvert.readthedocs.io/) |
| **pandas / numpy / scipy** | База анализа. Отдельно упоминаем потому, что агенту надо явно говорить, какой из них в проекте канон. | [pandas](https://pandas.pydata.org/docs/), [numpy](https://numpy.org/doc/stable/), [scipy](https://scipy.org/) |
| **matplotlib / seaborn** | Графики кодом. Код графика — в репозиторий, PNG — в `results/`. Никогда не правьте картинку руками: её должен пересобирать скрипт. | [matplotlib](https://matplotlib.org/stable/), [seaborn](https://seaborn.pydata.org/) |
| **Snakemake** | Пайплайн анализа как граф зависимостей: пересчитывается только то, что устарело. Когда шагов больше пяти — окупается. | [snakemake.readthedocs.io](https://snakemake.readthedocs.io/) |
| **Docker** | Заморозить всё окружение целиком, включая системные библиотеки. Для биоинформатики и R-проектов — [Rocker](https://rocker-project.org/). | [docker.com](https://www.docker.com/) |

## Git, GitHub и гигиена

| Инструмент | Зачем | Ссылка |
|---|---|---|
| **git** | Кнопка «отменить» на весь проект и способ увидеть, что сделал агент. Обязателен. | [git-scm.com/doc](https://git-scm.com/doc) |
| **gh** | GitHub из терминала: issues, PR, релизы, любой вызов API (`gh api`). Агент может завести issue на найденную проблему или открыть PR с правкой. | [cli.github.com](https://cli.github.com/), [manual](https://cli.github.com/manual/) |
| **pre-commit** | Проверки перед коммитом: формат, линтер, «не попал ли секрет в индекс». Страхует и вас, и агента. | [pre-commit.com](https://pre-commit.com/) |
| **direnv** | Переменные окружения на директорию, вне git. Правильное место для API-ключей — не в `AGENTS.md`. | [direnv.net](https://direnv.net/) |
| **Zenodo** | DOI на код и данные к статье, из GitHub в пару кликов. | [zenodo.org](https://zenodo.org/) |
| **OSF** | Хостинг материалов проекта, препринты, преregistration. | [osf.io](https://osf.io/) |

---

## Как это выглядит вместе

Типовая связка для статьи:

```
Zotero + Better BibTeX  ──►  paper/refs.bib
Crossref / OpenAlex API ──►  проверка каждой записи
GROBID / docling        ──►  чужие PDF → текст для агента
uv + Snakemake          ──►  данные → results/ (одной командой)
matplotlib              ──►  графики из скриптов
LaTeX + latexmk         ──►  paper/main.pdf
pandoc                  ──►  .docx для соавторов, которые не в LaTeX
git + gh                ──►  история, ветки, обсуждение
```

Всё, что выше, агент умеет запускать сам. Ваша работа — один раз описать эти
команды в [памяти проекта](../02-memory/README.md), чтобы он не угадывал.

---

[← 03. Механики](../03-agent-basics/README.md) · [к оглавлению](../README.md) · [дальше: 05. Воркфлоу →](../05-workflows/README.md)
