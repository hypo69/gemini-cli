# Удаление CLI

Метод удаления зависит от того, как вы запускали CLI. Следуйте инструкциям для npx или глобальной установки npm.

## Метод 1: Использование npx

npx запускает пакеты из временного кэша без постоянной установки. Чтобы "удалить" CLI, вы должны очистить этот кэш, что удалит gemini-cli и любые другие пакеты, ранее выполненные с помощью npx.

Кэш npx — это каталог с именем `_npx` внутри вашей основной папки кэша npm. Вы можете найти путь к кэшу npm, выполнив `npm config get cache`.

**Для macOS / Linux**

```bash
# Путь обычно ~/.npm/_npx
rm -rf "$(npm config get cache)/_npx"
```

**Для Windows**

_Командная строка_

```cmd
:: Путь обычно %LocalAppData%\npm-cache\_npx
rmdir /s /q "%LocalAppData%\npm-cache\_npx"
```

_PowerShell_

```powershell
# Путь обычно $env:LocalAppData\npm-cache\_npx
Remove-Item -Path (Join-Path $env:LocalAppData "npm-cache\_npx") -Recurse -Force
```

## Метод 2: Использование npm (глобальная установка)

Если вы установили CLI глобально (например, `npm install -g @google/gemini-cli`), используйте команду `npm uninstall` с флагом `-g` для его удаления.

```bash
npm uninstall -g @google/gemini-cli
```

Эта команда полностью удаляет пакет из вашей системы.
