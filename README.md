csad2627KI404TeodorovychMaksym22

ПІП: [ПІП]
Група: [група]
Варіант: 4 (Rust, Cargo, cargo test)

## Короткий опис проєкту

Проєкт містить конфігурацію AI-агента для VS Code Copilot, який послідовно генерує кросплатформний Rust/Cargo Hello World проєкт, налаштовує юніт-тест `basic_addition` і GitHub Actions CI для Linux, Windows та macOS.

## Запуск агента

1. Відкрити папку репозиторію у VS Code.
2. Відкрити Copilot Chat.
3. Вибрати агента `build-engineer`.
4. Виконати команду `/init`.

## Commands

- `git-init` — перевірити Git-репозиторій і налаштувати `.gitignore`.
- `create-project` — створити Rust/Cargo scaffold Hello World.
- `create-build` — налаштувати Cargo, тест `basic_addition` і build/test-скрипти.
- `create-actions` — створити GitHub Actions CI для Linux, Windows і macOS.
- `check` — виконати повну read-only перевірку проєкту та CI.
- `init` — виконати всі команди послідовно й сформувати підсумковий звіт.