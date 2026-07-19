# Maestro

**Tech-lead orchestration mode for Claude Code. The expensive model decides, the cheap models type.**

Maestro turns your top-tier model (Fable, Opus) into a tech lead: it keeps decomposition, architecture, contested decisions and final synthesis — and delegates everything else to a three-tier bench of cheaper subagents. You get the judgment of the best model at a fraction of the token bill.

[Русская версия ниже ↓](#maestro-ru)

## The bench

| Tier | Agent | Model | Does |
|---|---|---|---|
| 1 | `scout` | Haiku | Bounded lookups: find a file, locate a symbol, confirm a fact. Read-only, answers in under 100 words. |
| 2 | `fast-worker` | Sonnet | Mechanical execution to a spec: tests, boilerplate, renames, batched edit checklists. Verifies its own work. |
| 2 | `deep-reasoner` | Sonnet (Opus on demand) | Long digs: codebase exploration, log grinding, multi-file debugging. Read-only; returns a distilled conclusion, not a dump. |
| 3 | the session model | Fable / Opus | Decomposition, architecture, reviewing results, final synthesis. |

The escalation ladder is **Haiku → Sonnet → Opus → the lead**. Each step up must be justified by the tier below being insufficient.

## What makes it economical

- **Sonnet-first digging** — long investigations default to Sonnet; Opus is a per-call escalation for digs that are long *and* subtle.
- **Haiku scout** — reconnaissance no longer costs top-tier tokens.
- **Batched specs** — five related edits go out as one worker spawn, not five.
- **Known-context packages** — every spec carries the facts the lead already established, so subagents never re-discover paid-for knowledge.
- **Agent reuse** — follow-up questions go to the same agent (context intact) instead of a fresh spawn that re-reads everything.
- **Background digs** — the deep-reasoner grinds in the background while the lead keeps working.
- **Diff-level review** — the lead spot-checks risky hunks instead of re-reading every touched file.
- **Report budgets** — subagent reports are hard-capped, so results come back distilled.

## Install

```
/plugin marketplace add zamena1985/maestro
/plugin install maestro@maestro
```

## Use

Start a session on a top-tier model and invoke:

```
/maestro
```

Best at the beginning of substantial multi-step work. If the session context gets compacted and delegation behavior fades, invoke `/maestro` again. Not intended for trivial single-edit tasks or sessions already running on a cheap model.

If the [Codex](https://github.com/openai/codex) plugin is installed, Maestro will also request a second opinion (`codex:rescue`) on risky decisions — security, migrations, contradictory findings. Optional; skipped when absent.

## License

MIT — see [LICENSE](LICENSE).

---

<a name="maestro-ru"></a>

# Maestro (RU)

**Режим тимлида для Claude Code. Дорогая модель решает, дешёвые модели печатают.**

Maestro превращает вашу топовую модель (Fable, Opus) в тимлида: она оставляет себе декомпозицию, архитектуру, спорные решения и финальный синтез — а всё остальное делегирует «скамейке» из трёх уровней дешёвых субагентов. Вы получаете суждения лучшей модели за долю токенового бюджета.

## Скамейка

| Уровень | Агент | Модель | Делает |
|---|---|---|---|
| 1 | `scout` | Haiku | Точечная разведка: найти файл, символ, подтвердить факт. Только чтение, ответ до 100 слов. |
| 2 | `fast-worker` | Sonnet | Механическое исполнение по спеку: тесты, шаблонный код, переименования, пакетные чек-листы правок. Сам верифицирует работу. |
| 2 | `deep-reasoner` | Sonnet (Opus по запросу) | Долгие «раскопки»: изучение кодовой базы, разбор логов, мультифайловая отладка. Только чтение; возвращает выжимку, а не свалку. |
| 3 | модель сессии | Fable / Opus | Декомпозиция, архитектура, ревью результатов, финальный синтез. |

Лестница эскалации: **Haiku → Sonnet → Opus → лид**. Каждый шаг вверх должен быть оправдан тем, что уровня ниже не хватило.

## За счёт чего экономия

- **Sonnet-first раскопки** — долгие исследования по умолчанию идут на Sonnet; Opus — точечная эскалация для «долгих И тонких» задач.
- **Haiku-разведчик** — поиск по коду больше не стоит токенов топ-модели.
- **Пакетные спеки** — пять связанных правок уходят одним запуском воркера, а не пятью.
- **Пакет известного контекста** — каждый спек несёт факты, которые лид уже установил: субагенты не переоткрывают уже оплаченное знание.
- **Переиспользование агентов** — уточняющие вопросы идут тому же агенту (контекст цел), а не новому, который перечитывает всё с нуля.
- **Фоновые раскопки** — deep-reasoner копает в фоне, пока лид продолжает работать.
- **Ревью по диффам** — лид выборочно проверяет рискованные места, а не перечитывает каждый тронутый файл.
- **Бюджеты отчётов** — отчёты субагентов жёстко ограничены по объёму: назад приходит только выжимка.

## Установка

```
/plugin marketplace add zamena1985/maestro
/plugin install maestro@maestro
```

## Использование

Начните сессию на топовой модели и вызовите:

```
/maestro
```

Лучше всего — в начале большой многошаговой работы. Если после сжатия контекста режим «выветрился», вызовите `/maestro` ещё раз. Не предназначен для тривиальных задач в одну правку и для сессий на дешёвой модели.

Если установлен плагин [Codex](https://github.com/openai/codex), Maestro запросит второе мнение (`codex:rescue`) по рискованным решениям — безопасность, миграции, противоречивые результаты. Опционально; при отсутствии пропускается.

## Лицензия

MIT — см. [LICENSE](LICENSE).
