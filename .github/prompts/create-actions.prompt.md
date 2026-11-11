---
description: Створити та перевірити GitHub Actions CI для Linux, Windows і macOS за правилами skill github-actions.
agent: build-engineer
---

# Create Actions

## Передумови

Перед початком перевір, що в корені проєкту існують:

- `ci.sh`;
- `ci.bat`.

Перевір, що `ci.sh` має executable-біт. Якщо він відсутній, виправ режим командою `git update-index --chmod=+x ci.sh` і лише після цього продовжуй.

Якщо будь-який скрипт відсутній або режим `ci.sh` неможливо виправити, зупинись і повідом причину.

## Дії

1. Прочитай інструкції зі skill `.github/skills/github-actions/SKILL.md`.
2. Виконай усі інструкції цього skill.
3. Створи або перевір `.github/workflows/ci.yml` за контрактом skill.
4. Не додавай у workflow прямих команд `cargo` або `rustc`, а також конфіденційних значень.
5. Після завершення покажи список створених, перевірених і конфліктних шляхів.

## Очікувані файли

Після успішного виконання має існувати:

- `.github/workflows/ci.yml`.

Workflow має містити тригери `push` і `pull_request` для `develop` та `master`, одну matrix-job з `ubuntu-latest`, `windows-latest` і `macos-latest`, запуск `./ci.sh` на Unix, запуск `ci.bat` на Windows і завантаження `dist/` як artifact.

## Команди перевірки

Перевір YAML командою:

```text
python -c "import yaml, pathlib; yaml.safe_load(pathlib.Path('.github/workflows/ci.yml').read_text())"
```

Якщо `actionlint` доступний, також виконай:

```text
actionlint .github/workflows/ci.yml
```

Додатково перевір, що:

- у файлі є `ubuntu-latest`, `windows-latest` і `macos-latest`;
- у Unix-кроці `shell: bash` і `run: ./ci.sh`;
- у Windows-кроці `shell: cmd` і `run: ci.bat`;
- значення `run` не містять прямих команд `cargo` або `rustc`;
- `actions/checkout@v4` і `actions/upload-artifact@v4` присутні;
- artifact використовує `dist/` та `if-no-files-found: error`.

## Повідомлення про помилку

Якщо передумова, створення workflow, YAML-перевірка або перевірка структури завершується помилкою:

1. зупинись;
2. покажи команду, шлях і причину помилки;
3. не перезаписуй конфліктний файл;
4. не повідомляй про успішне завершення.

Якщо `ci.sh` не має executable-біта, повідом про це та виправ режим через `git update-index --chmod=+x ci.sh` перед повторною перевіркою.

## Ідемпотентність

Повторний запуск має перевіряти наявний `.github/workflows/ci.yml` і не дублювати тригери, job, steps або artifact-конфігурацію. Якщо workflow має інший вміст, не перезаписуй його: повідом про конфлікт і вважай операцію неуспішною.