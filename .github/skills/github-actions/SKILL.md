---
name: github-actions
description: Застосовуй для створення та перевірки GitHub Actions CI для Rust/Cargo проєкту на Linux, Windows і macOS.
---

# GitHub Actions

## Вхід

Перед початком перевір, що в корені проєкту існують `ci.sh` і `ci.bat`, створені skill `build-and-test`. Перевір, що `ci.sh` має executable-біт.

Якщо `ci.sh` не має прав на виконання, зупинись, повідом про проблему та виправ режим командою:

```text
git update-index --chmod=+x ci.sh
```

## Вихід

Створи `.github/workflows/ci.yml` з таким контрактом:

```yaml
name: CI

on:
  push:
    branches: [develop, master]
  pull_request:
    branches: [develop, master]

jobs:
  build:
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Run Unix CI
        if: runner.os != 'Windows'
        shell: bash
        run: ./ci.sh

      - name: Run Windows CI
        if: runner.os == 'Windows'
        shell: cmd
        run: ci.bat

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: hello-${{ matrix.os }}
          path: dist/
          if-no-files-found: error
```

Workflow має містити одну job із matrix `os`, `fail-fast: false`, checkout, платформний запуск CI та upload artifact. Не додавай конфіденційні значення або їхні посилання.

## Правила workflow

1. Тригери `push` і `pull_request` мають працювати лише для гілок `develop` і `master`.
2. Для `ubuntu-latest` і `macos-latest` запускай `./ci.sh` із `shell: bash`.
3. Для `windows-latest` запускай `ci.bat` із `shell: cmd`.
4. У значеннях `run` файлу `ci.yml` не використовуй команди `cargo` або `rustc` напряму.
5. Не дублюй у workflow логіку build, test або копіювання executable зі скриптів.
6. Використовуй `actions/upload-artifact@v4` з унікальним ім'ям `hello-${{ matrix.os }}`.
7. Для artifact встанови `path: dist/` і `if-no-files-found: error`.
8. Не додавай конфіденційні значення, облікові дані або зовнішню публікацію даних.

## Перевірки

Перевір:

- YAML валідний командою `python -c "import yaml, pathlib; yaml.safe_load(pathlib.Path('.github/workflows/ci.yml').read_text())"` або за допомогою `actionlint`, якщо він доступний;
- у workflow присутні `ubuntu-latest`, `windows-latest` і `macos-latest`;
- присутні тригери `push` і `pull_request` для `develop` та `master`;
- є `fail-fast: false`;
- є `actions/checkout@v4` та `actions/upload-artifact@v4`;
- artifact завантажує `dist/` і має `if-no-files-found: error`;
- Unix-крок має `shell: bash` і `run: ./ci.sh`;
- Windows-крок має `shell: cmd` і `run: ci.bat`;
- у `ci.yml` немає прямих команд `cargo` або `rustc` у полях `run`;
- у файлі немає слів `token`, `password` або `secret`;
- `ci.sh` має executable-біт.

## Типова помилка

`ci.sh` не має прав на виконання. У такому випадку workflow на Unix завершується помилкою ще до запуску Rust-команд. Виправ режим файлу через `git update-index --chmod=+x ci.sh` і повтори перевірки.

## Приклад

### Вхід

```yaml
ci.sh: існує та має executable-біт
ci.bat: існує
branches: [develop, master]
```

### Очікуваний результат

```text
.github/workflows/ci.yml created
one matrix job runs on ubuntu-latest, windows-latest and macos-latest
Unix runs ./ci.sh with bash
Windows runs ci.bat with cmd
dist/ is uploaded as hello-${{ matrix.os }}
YAML validation passes
```