# Використання

## Запуск агента

1. Відкрийте кореневу папку репозиторію у VS Code:

   ```text
   code .
   ```

2. Відкрийте **Copilot Chat**.
3. У списку агентів виберіть `build-engineer`.
4. У Copilot Chat виконайте:

   ```text
   /init
   ```

   Команда послідовно виконає `git-init`, `create-project`, `create-build`, `create-actions` і `check`.
5. Для повторної повної перевірки у Copilot Chat виконайте:

   ```text
   /check
   ```

## Локальна збірка і перевірка

### Linux/macOS

У корені репозиторію виконайте:

```bash
./ci.sh
```

Скрипт виконує release build, release-тести, створює `dist/` і копіює executable у `dist/hello`.

### Windows

У корені репозиторію виконайте:

```bat
ci.bat
```

Скрипт виконує release build, release-тести, створює `dist\` і копіює executable у `dist\hello.exe`.

## Запуск hello

### Linux/macOS

```bash
./dist/hello
```

Очікуваний вивід:

```text
Hello, World!
```

### Windows

```bat
dist\hello.exe
```

Очікуваний вивід:

```text
Hello, World!
```

## Запуск тестів

Для окремого запуску release-тестів виконайте:

```text
cargo test --release
```

Успішний результат має містити:

```text
test basic_addition ... ok
```