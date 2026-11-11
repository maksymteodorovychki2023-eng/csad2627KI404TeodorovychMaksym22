# Архітектура AI-агента

## 1. Які проблеми вирішує агент

Агент `build-engineer` автоматизує створення та перевірку мінімального кросплатформного Rust/Cargo проєкту Hello World. Він вирішує такі задачі:

- створює однаковий scaffold проєкту з бібліотекою та бінарним застосунком;
- налаштовує Cargo для бібліотеки `hello_lib` і executable `hello`;
- додає юніт-тест `basic_addition`;
- генерує release-збірку та копіює executable у `dist/`;
- створює CI для Linux, Windows і macOS на GitHub Actions;
- перевіряє структуру проєкту, тести, executable, CI та Git-ігнорування;
- запобігає випадковому перезапису файлів і дублюванню конфігурації;
- зупиняє процес при помилках і не оголошує незавершену роботу успішною.

## 2. Skills та їх відповідальність

| Skill | Відповідальність |
|---|---|
| `project-scaffold` | Створення `src/lib.rs`, `src/main.rs`, порожніх `tests/` і `docs/`, а також `README.md`, якщо його немає. Визначає Rust-вхід `language=Rust`, `project_name=hello`. |
| `build-and-test` | Створення `Cargo.toml`, інтеграційного тесту `tests/test_basic.rs`, генерація `Cargo.lock`, налаштування `ci.sh` і `ci.bat`, запуск build/test перевірок. |
| `github-actions` | Створення `.github/workflows/ci.yml` з matrix для Ubuntu, Windows і macOS, запуском платформних scripts та upload artifact з `dist/`. |

## 3. Доступні commands

| Command | Призначення |
|---|---|
| `git-init` | Перевірити Git-репозиторій і створити або доповнити `.gitignore` без дублів. |
| `create-project` | Виконати `project-scaffold` для створення Rust/Cargo Hello World scaffold. |
| `create-build` | Виконати `build-and-test` для Cargo, тесту та build/test scripts. |
| `create-actions` | Виконати `github-actions` для створення кросплатформного workflow. |
| `check` | У read-only режимі перевірити файли, Cargo build/test, executable, CI та Git-ігнорування. |
| `init` | Оркеструвати всі попередні commands у фіксованому порядку та сформувати звіт. |

## 4. Дані між етапами

Потік даних має послідовну форму:

```text
project_name -> src/ -> Cargo.toml -> ci.sh/ci.bat -> ci.yml
```

1. `project_name=hello` визначає назву проєкту та очікуваний executable.
2. `project-scaffold` створює `src/lib.rs` із `hello_lib::add` і `src/main.rs`, який використовує бібліотеку та друкує `Hello, World!`.
3. `build-and-test` описує ці Rust-входи в `Cargo.toml`: пакет має назву `hello`, бібліотека `hello_lib`, а binary `hello`. На цьому етапі також з'являються тест і Cargo lock-файл.
4. `ci.sh` і `ci.bat` використовують `Cargo.toml` для release build і test, створюють `dist/` та копіюють platform-specific executable.
5. `github-actions` створює `ci.yml`, який запускає відповідний script на кожній ОС і передає вміст `dist/` до artifact з унікальним ім'ям для matrix-ОС.
6. `check` читає результати всіх етапів і підтверджує, що структура, build, test, executable, CI та Git-ігнорування відповідають критеріям.

## 5. Обробка помилок оркестратором `init`

`init` виконує п'ять prompt-команд строго послідовно:

1. `git-init`;
2. `create-project`;
3. `create-build`;
4. `create-actions`;
5. `check`.

Після кожного кроку оркестратор перевіряє результат. Якщо крок або його перевірка завершується помилкою, `init`:

- негайно зупиняється;
- не запускає наступні кроки;
- показує крок, команду, шлях і причину помилки;
- описує, що потрібно виправити;
- позначає наступні кроки як `не виконано`;
- не оголошує роботу успішною.

Фінальний статус `PASSED` дозволений лише після того, як усі кроки виконані, а read-only `check` вивів `Статус: PASSED`. Повторний запуск перевіряє наявний вміст, не видаляє файли та не дублює секції, рядки, jobs або commands.

## 6. Відповідність файлів ролям

| Роль | Каталог | Файли |
|---|---|---|
| Agent | `.github/agents/` | `build-engineer.agent.md` |
| Skills | `.github/skills/` | `project-scaffold/SKILL.md`, `build-and-test/SKILL.md`, `github-actions/SKILL.md` |
| Commands | `.github/prompts/` | `git-init.prompt.md`, `create-project.prompt.md`, `create-build.prompt.md`, `create-actions.prompt.md`, `check.prompt.md`, `init.prompt.md` |

Agent визначає роль і доступні інструменти, skills містять спеціалізовані правила, а prompt-файли є командами та оркестраційними сценаріями для користувача.

## 7. Формат VS Code Copilot

У проєкті використано формат VS Code Copilot для кастомізації агента:

- custom agent у `.github/agents/`;
- prompt files у `.github/prompts/`;
- agent skills у `.github/skills/`.

Ця структура дає змогу вибрати `build-engineer` у Copilot Chat і запустити повний сценарій командою `/init`.