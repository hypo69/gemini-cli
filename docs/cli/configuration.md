# Gemini CLI Configuration

Gemini CLI offers several ways to configure its behavior, including environment variables, command-line arguments, and settings files. This document outlines the different configuration methods and available settings.

## Configuration layers

Configuration is applied in the following order of precedence (lower numbers are overridden by higher numbers):

1.  **Default values:** Hardcoded defaults within the application.
2.  **User settings file:** Global settings for the current user.
3.  **Project settings file:** Project-specific settings.
4.  **System settings file:** System-wide settings.
5.  **Environment variables:** System-wide or session-specific variables, potentially loaded from `.env` files.
6.  **Command-line arguments:** Values passed when launching the CLI.

## Settings files

# Конфигурация Gemini CLI

Gemini CLI предлагает несколько способов настройки своего поведения, включая переменные среды, аргументы командной строки и файлы настроек. Этот документ описывает различные методы конфигурации и доступные настройки.

## Уровни конфигурации

Конфигурация применяется в следующем порядке приоритета (нижние номера переопределяются верхними):

1.  **Значения по умолчанию:** Жестко закодированные значения по умолчанию в приложении.
2.  **Файл пользовательских настроек:** Глобальные настройки для текущего пользователя.
3.  **Файл настроек проекта:** Настройки, специфичные для проекта.
4.  **Файл системных настроек:** Общесистемные настройки.
5.  **Переменные среды:** Общесистемные или специфичные для сеанса переменные, потенциально загружаемые из файлов `.env`.
6.  **Аргументы командной строки:** Значения, передаваемые при запуске CLI.

## Файлы настроек

Gemini CLI использует файлы `settings.json` для постоянной конфигурации. Существует три расположения для этих файлов:

- **Файл пользовательских настроек:**
  - **Расположение:** `~/.gemini/settings.json` (где `~` — ваш домашний каталог).
  - **Область действия:** Применяется ко всем сеансам Gemini CLI для текущего пользователя.
- **Файл настроек проекта:**
  - **Расположение:** `.gemini/settings.json` в корневом каталоге вашего проекта.
  - **Область действия:** Применяется только при запуске Gemini CLI из этого конкретного проекта. Настройки проекта переопределяют пользовательские настройки.
- **Файл системных настроек:**
  - **Расположение:** `/etc/gemini-cli/settings.json` (Linux), `C:\ProgramData\gemini-cli\settings.json` (Windows) или `/Library/Application Support/GeminiCli/settings.json` (macOS). Путь может быть переопределен с помощью переменной среды `GEMINI_CLI_SYSTEM_SETTINGS_PATH`.
  - **Область действия:** Применяется ко всем сеансам Gemini CLI в системе для всех пользователей. Системные настройки переопределяют пользовательские и проектные настройки. Может быть полезно для системных администраторов на предприятиях для контроля над настройками Gemini CLI пользователей.

**Примечание о переменных среды в настройках:** Строковые значения в ваших файлах `settings.json` могут ссылаться на переменные среды с использованием синтаксиса `$VAR_NAME` или `${VAR_NAME}`. Эти переменные будут автоматически разрешены при загрузке настроек. Например, если у вас есть переменная среды `MY_API_TOKEN`, вы можете использовать ее в `settings.json` следующим образом: `"apiKey": "$MY_API_TOKEN"`.

### Каталог `.gemini` в вашем проекте

В дополнение к файлу настроек проекта, каталог `.gemini` проекта может содержать другие файлы, специфичные для проекта, связанные с работой Gemini CLI, такие как:

- [Пользовательские профили песочницы](#sandboxing) (например, `.gemini/sandbox-macos-custom.sb`, `.gemini/sandbox.Dockerfile`).

### Доступные настройки в `settings.json`:

- **`contextFileName`** (строка или массив строк):
  - **Описание:** Указывает имя файла для файлов контекста (например, `GEMINI.md`, `AGENTS.md`). Может быть одним именем файла или списком принятых имен файлов.
  - **По умолчанию:** `GEMINI.md`
  - **Пример:** `"contextFileName": "AGENTS.md"`

- **`bugCommand`** (объект):
  - **Описание:** Переопределяет URL по умолчанию для команды `/bug`.
  - **По умолчанию:** `"urlTemplate": "https://github.com/google-gemini/gemini-cli/issues/new?template=bug_report.yml&title={title}&info={info}"`
  - **Свойства:**
    - **`urlTemplate`** (строка): URL, который может содержать заполнители `{title}` и `{info}`.
  - **Пример:**
    ```json
    "bugCommand": {
      "urlTemplate": "https://bug.example.com/new?title={title}&info={info}"
    }
    ```

- **`fileFiltering`** (объект):
  - **Описание:** Управляет поведением фильтрации файлов с учетом git для команд @ и инструментов обнаружения файлов.
  - **По умолчанию:** `"respectGitIgnore": true, "enableRecursiveFileSearch": true`
  - **Свойства:**
    - **`respectGitIgnore`** (логическое значение): Учитывать ли шаблоны .gitignore при обнаружении файлов. Если установлено `true`, файлы, игнорируемые git (например, `node_modules/`, `dist/`, `.env`), автоматически исключаются из команд @ и операций перечисления файлов.
    - **`enableRecursiveFileSearch`** (логическое значение): Включить ли рекурсивный поиск имен файлов в текущем дереве при завершении префиксов @ в запросе.
  - **Пример:**
    ```json
    "fileFiltering": {
      "respectGitIgnore": true,
      "enableRecursiveFileSearch": false
    }
    ```

- **`coreTools`** (массив строк):
  - **Описание:** Позволяет указать список имен основных инструментов, которые должны быть доступны модели. Это можно использовать для ограничения набора встроенных инструментов. См. [Встроенные инструменты](../core/tools-api.md#built-in-tools) для списка основных инструментов. Вы также можете указать ограничения для конкретных команд для инструментов, которые это поддерживают, например, `ShellTool`. Например, `"coreTools": ["ShellTool(ls -l)"]` разрешит выполнение только команды `ls -l`.
  - **По умолчанию:** Все инструменты доступны для использования моделью Gemini.
  - **Пример:** `"coreTools": ["ReadFileTool", "GlobTool", "ShellTool(ls)"]`.

- **`excludeTools`** (массив строк):
  - **Описание:** Позволяет указать список имен основных инструментов, которые должны быть исключены из модели. Инструмент, указанный как в `excludeTools`, так и в `coreTools`, исключается. Вы также можете указать ограничения для конкретных команд для инструментов, которые это поддерживают, например, `ShellTool`. Например, `"excludeTools": ["ShellTool(rm -rf)"]` заблокирует команду `rm -rf`.
  - **По умолчанию**: Инструменты не исключены.
  - **Пример:** `"excludeTools": ["run_shell_command", "findFiles"]`.
  - **Примечание по безопасности:** Ограничения для конкретных команд в
    `excludeTools` для `run_shell_command` основаны на простом сопоставлении строк и могут быть легко обойдены. Эта функция **не является механизмом безопасности** и не должна использоваться для безопасного выполнения недоверенного кода. Рекомендуется использовать `coreTools` для явного выбора команд,
    которые могут быть выполнены.

- **`allowMCPServers`** (массив строк):
  - **Описание:** Позволяет указать список имен MCP-серверов, которые должны быть доступны модели. Это можно использовать для ограничения набора MCP-серверов для подключения. Обратите внимание, что это будет проигнорировано, если установлен `--allowed-mcp-server-names`.
  - **По умолчанию:** Все MCP-серверы доступны для использования моделью Gemini.
  - **Пример:** `"allowMCPServers": ["myPythonServer"]`.
  - **Примечание по безопасности:** Это использует простое сопоставление строк по именам MCP-серверов, которые могут быть изменены. Если вы системный администратор, желающий предотвратить обход этого пользователями, рассмотрите возможность настройки `mcpServers` на уровне системных настроек таким образом, чтобы пользователь не мог настраивать свои собственные MCP-серверы. Это не должно использоваться как герметичный механизм безопасности.

- **`excludeMCPServers`** (массив строк):
  - **Описание:** Позволяет указать список имен MCP-серверов, которые должны быть исключены из модели. Сервер, указанный как в `excludeMCPServers`, так и в `allowMCPServers`, исключается. Обратите внимание, что это будет проигнорировано, если установлен `--allowed-mcp-server-names`.
  - **По умолчанию**: MCP-серверы не исключены.
  - **Пример:** `"excludeMCPServers": ["myNodeServer"]`.
  - **Примечание по безопасности:** Это использует простое сопоставление строк по именам MCP-серверов, которые могут быть изменены. Если вы системный администратор, желающий предотвратить обход этого пользователями, рассмотрите возможность настройки `mcpServers` на уровне системных настроек таким образом, чтобы пользователь не мог настраивать свои собственные MCP-серверы. Это не должно использоваться как герметичный механизм безопасности.

- **`autoAccept`** (логическое значение):
  - **Описание:** Управляет тем, будет ли CLI автоматически принимать и выполнять вызовы инструментов, которые считаются безопасными (например, операции только для чтения) без явного подтверждения пользователя. Если установлено `true`, CLI будет пропускать запрос подтверждения для инструментов, считающихся безопасными.
  - **По умолчанию:** `false`
  - **Пример:** `"autoAccept": true`

- **`theme`** (строка):
  - **Описание:** Устанавливает визуальную [тему](./themes.md) для Gemini CLI.
  - **По умолчанию:** `"Default"`
  - **Пример:** `"theme": "GitHub"`

- **`vimMode`** (логическое значение):
  - **Описание:** Включает или отключает режим vim для редактирования ввода. Когда включен, область ввода поддерживает навигацию в стиле vim и команды редактирования в режимах NORMAL и INSERT. Статус режима vim отображается в нижнем колонтитуле и сохраняется между сеансами.
  - **По умолчанию:** `false`
  - **Пример:** `"vimMode": true`

- **`sandbox`** (логическое значение или строка):
  - **Описание:** Управляет тем, использовать ли и как использовать песочницу для выполнения инструментов. Если установлено `true`, Gemini CLI использует предварительно собранный образ Docker `gemini-cli-sandbox`. Дополнительную информацию см. в разделе [Песочница](#sandboxing).
  - **По умолчанию:** `false`
  - **Пример:** `"sandbox": "docker"`

- **`toolDiscoveryCommand`** (строка):
  - **Описание:** Определяет пользовательскую команду оболочки для обнаружения инструментов из вашего проекта. Команда оболочки должна возвращать в `stdout` массив JSON [объявлений функций](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations). Обертки инструментов необязательны.
  - **По умолчанию:** Пусто
  - **Пример:** `"toolDiscoveryCommand": "bin/get_tools"`

- **`toolCallCommand`** (строка):
  - **Описание:** Определяет пользовательскую команду оболочки для вызова конкретного инструмента, который был обнаружен с помощью `toolDiscoveryCommand`. Команда оболочки должна соответствовать следующим критериям:
    - Она должна принимать имя функции (точно так же, как в [объявлении функции](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations)) в качестве первого аргумента командной строки.
    - Она должна считывать аргументы функции в формате JSON из `stdin`, аналогично [`functionCall.args`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functioncall).
    - Она должна возвращать вывод функции в формате JSON в `stdout`, аналогично [`functionResponse.response.content`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functionresponse).
  - **По умолчанию:** Пусто
  - **Пример:** `"toolCallCommand": "bin/call_tool"`

- **`mcpServers`** (объект):
  - **Описание:** Настраивает соединения с одним или несколькими серверами Model-Context Protocol (MCP) для обнаружения и использования пользовательских инструментов. Gemini CLI пытается подключиться к каждому настроенному MCP-серверу для обнаружения доступных инструментов. Если несколько MCP-серверов предоставляют инструмент с одним и тем же именем, имена инструментов будут иметь префикс с псевдонимом сервера, который вы определили в конфигурации (например, `serverAlias__actualToolName`), чтобы избежать конфликтов. Обратите внимание, что система может удалять определенные свойства схемы из определений инструментов MCP для совместимости.
  - **По умолчанию:** Пусто
  - **Свойства:**
    - **`<ИМЯ_СЕРВЕРА>`** (объект): Параметры сервера для именованного сервера.
      - `command` (строка, обязательный): Команда для запуска MCP-сервера.
      - `args` (массив строк, необязательный): Аргументы для передачи команде.
      - `env` (объект, необязательный): Переменные среды для установки для процесса сервера.
      - `cwd` (строка, необязательный): Рабочий каталог, в котором запускается сервер.
      - `timeout` (число, необязательный): Тайм-аут в миллисекундах для запросов к этому MCP-серверу.
      - `trust` (логическое значение, необязательный): Доверять этому серверу и обходить все подтверждения вызова инструмента.
      - `includeTools` (массив строк, необязательный): Список имен инструментов для включения с этого MCP-сервера. Если указано, только перечисленные здесь инструменты будут доступны с этого сервера (поведение белого списка). Если не указано, все инструменты с сервера включены по умолчанию.
      - `excludeTools` (массив строк, необязательный): Список имен инструментов для исключения с этого MCP-сервера. Инструменты, перечисленные здесь, не будут доступны модели, даже если они предоставляются сервером. **Примечание:** `excludeTools` имеет приоритет над `includeTools` - если инструмент находится в обоих списках, он будет исключен.
  - **Пример:**
    ```json
    "mcpServers": {
      "myPythonServer": {
        "command": "python",
        "args": ["mcp_server.py", "--port", "8080"],
        "cwd": "./mcp_tools/python",
        "timeout": 5000,
        "includeTools": ["safe_tool", "file_reader"],
      },
      "myNodeServer": {
        "command": "node",
        "args": ["mcp_server.js"],
        "cwd": "./mcp_tools/node",
        "excludeTools": ["dangerous_tool", "file_deleter"]
      },
      "myDockerServer": {
        "command": "docker",
        "args": ["run", "-i", "--rm", "-e", "API_KEY", "ghcr.io/foo/bar"],
        "env": {
          "API_KEY": "$MY_API_TOKEN"
        }
      }
    }
    ```

- **`checkpointing`** (объект):
  - **Описание:** Настраивает функцию контрольных точек, которая позволяет сохранять и восстанавливать состояния разговоров и файлов. Дополнительные сведения см. в [документации по контрольным точкам](../checkpointing.md).
  - **По умолчанию:** `{"enabled": false}`
  - **Свойства:**
    - **`enabled`** (логическое значение): Если `true`, команда `/restore` доступна.

- **`preferredEditor`** (строка):
  - **Описание:** Указывает предпочтительный редактор для просмотра различий.
  - **По умолчанию:** `vscode`
  - **Пример:** `"preferredEditor": "vscode"`

- **`telemetry`** (объект)
  - **Описание:** Настраивает сбор журналов и метрик для Gemini CLI. Дополнительную информацию см. в разделе [Телеметрия](../telemetry.md).
  - **По умолчанию:** `{"enabled": false, "target": "local", "otlpEndpoint": "http://localhost:4317", "logPrompts": true}`
  - **Свойства:**
    - **`enabled`** (логическое значение): Включена ли телеметрия.
    - **`target`** (строка): Назначение собранных данных телеметрии. Поддерживаемые значения: `local` и `gcp`.
    - **`otlpEndpoint`** (строка): Конечная точка для экспортера OTLP.
    - **`logPrompts`** (логическое значение): Включать ли содержимое пользовательских запросов в журналы.
  - **Пример:**
    ```json
    "telemetry": {
      "enabled": true,
      "target": "local",
      "otlpEndpoint": "http://localhost:16686",
      "logPrompts": false
    }
    ```
- **`usageStatisticsEnabled`** (логическое значение):
  - **Описание:** Включает или отключает сбор статистики использования. Дополнительную информацию см. в разделе [Статистика использования](#usage-statistics).
  - **По умолчанию:** `true`
  - **Пример:**
    ```json
    "usageStatisticsEnabled": false
    ```

- **`hideTips`** (логическое значение):
  - **Описание:** Включает или отключает полезные советы в интерфейсе CLI.
  - **По умолчанию:** `false`
  - **Пример:**

    ```json
    "hideTips": true
    ```

- **`hideBanner`** (логическое значение):
  - **Описание:** Включает или отключает стартовый баннер (логотип в виде ASCII-арта) в интерфейсе CLI.
  - **По умолчанию:** `false`
  - **Пример:**

    ```json
    "hideBanner": true
    ```

- **`maxSessionTurns`** (число):
  - **Описание:** Устанавливает максимальное количество ходов для сеанса. Если сеанс превышает этот лимит, CLI прекратит обработку и начнет новый чат.
  - **По умолчанию:** `-1` (без ограничений)
  - **Пример:**
    ```json
    "maxSessionTurns": 10
    ```

- **`summarizeToolOutput`** (объект):
  - **Описание:** Включает или отключает суммирование вывода инструмента. Вы можете указать бюджет токенов для суммирования с помощью настройки `tokenBudget`.
  - Примечание: В настоящее время поддерживается только инструмент `run_shell_command`.
  - **По умолчанию:** `{}` (Отключено по умолчанию)
  - **Пример:**
    ```json
    "summarizeToolOutput": {
      "run_shell_command": {
        "tokenBudget": 2000
      }
    }
    ```

### Пример `settings.json`:

```json
{
  "theme": "GitHub",
  "sandbox": "docker",
  "toolDiscoveryCommand": "bin/get_tools",
  "toolCallCommand": "bin/call_tool",
  "mcpServers": {
    "mainServer": {
      "command": "bin/mcp_server.py"
    },
    "anotherServer": {
      "command": "node",
      "args": ["mcp_server.js", "--verbose"]
    }
  },
  "telemetry": {
    "enabled": true,
    "target": "local",
    "otlpEndpoint": "http://localhost:4317",
    "logPrompts": true
  },
  "usageStatisticsEnabled": true,
  "hideTips": false,
  "hideBanner": false,
  "maxSessionTurns": 10,
  "summarizeToolOutput": {
    "run_shell_command": {
      "tokenBudget": 100
    }
  }
}
```

## История оболочки

CLI сохраняет историю выполненных вами команд оболочки. Чтобы избежать конфликтов между различными проектами, эта история хранится в каталоге, специфичном для проекта, в домашней папке пользователя.

- **Расположение:** `~/.gemini/tmp/<project_hash>/shell_history`
  - `<project_hash>` — это уникальный идентификатор, сгенерированный из корневого пути вашего проекта.
  - История хранится в файле с именем `shell_history`.

## Переменные среды и файлы `.env`

Переменные среды — это распространенный способ настройки приложений, особенно для конфиденциальной информации, такой как ключи API, или для настроек, которые могут меняться между средами.

CLI автоматически загружает переменные среды из файла `.env`. Порядок загрузки следующий:

1.  Файл `.env` в текущем рабочем каталоге.
2.  Если не найден, он ищет вверх по родительским каталогам, пока не найдет файл `.env` или не достигнет корня проекта (идентифицируемого папкой `.git`) или домашнего каталога.
3.  Если все еще не найден, он ищет `~/.env` (в домашнем каталоге пользователя).

- **`GEMINI_API_KEY`** (Обязательно):
  - Ваш ключ API для Gemini API.
  - **Крайне важен для работы.** CLI не будет работать без него.
  - Установите это в своем профиле оболочки (например, `~/.bashrc`, `~/.zshrc`) или в файле `.env`.
- **`GEMINI_MODEL`**:
  - Указывает модель Gemini по умолчанию для использования.
  - Переопределяет жестко закодированное значение по умолчанию
  - Пример: `export GEMINI_MODEL="gemini-2.5-flash"`
- **`GOOGLE_API_KEY`**:
  - Ваш ключ Google Cloud API.
  - Требуется для использования Vertex AI в экспресс-режиме.
  - Убедитесь, что у вас есть необходимые разрешения.
  - Пример: `export GOOGLE_API_KEY="ВАШ_КЛЮЧ_GOOGLE_API"`.
- **`GOOGLE_CLOUD_PROJECT`**:
  - Идентификатор вашего проекта Google Cloud.
  - Требуется для использования Code Assist или Vertex AI.
  - Если вы используете Vertex AI, убедитесь, что у вас есть необходимые разрешения в этом проекте.
  - **Примечание для Cloud Shell:** При работе в среде Cloud Shell эта переменная по умолчанию указывает на специальный проект, выделенный для пользователей Cloud Shell. Если у вас установлена переменная `GOOGLE_CLOUD_PROJECT` в вашей глобальной среде в Cloud Shell, она будет переопределена этим значением по умолчанию. Чтобы использовать другой проект в Cloud Shell, вы должны определить `GOOGLE_CLOUD_PROJECT` в файле `.env`.
  - Пример: `export GOOGLE_CLOUD_PROJECT="ВАШ_ИДЕНТИФИКАТОР_ПРОЕКТА"`.
- **`GOOGLE_APPLICATION_CREDENTIALS`** (строка):
  - **Описание:** Путь к вашему JSON-файлу учетных данных приложения Google.
  - **Пример:** `export GOOGLE_APPLICATION_CREDENTIALS="/путь/к/вашим/credentials.json"`
- **`OTLP_GOOGLE_CLOUD_PROJECT`**:
  - Идентификатор вашего проекта Google Cloud для телеметрии в Google Cloud
  - Пример: `export OTLP_GOOGLE_CLOUD_PROJECT="ВАШ_ИДЕНТИФИКАТОР_ПРОЕКТА"`.
- **`GOOGLE_CLOUD_LOCATION`**:
  - Расположение вашего проекта Google Cloud (например, us-central1).
  - Требуется для использования Vertex AI в неэкспресс-режиме.
  - Пример: `export GOOGLE_CLOUD_LOCATION="ВАШ_РАСПОЛОЖЕНИЕ_ПРОЕКТА"`.
- **`GEMINI_SANDBOX`**:
  - Альтернатива настройке `sandbox` в `settings.json`.
  - Принимает `true`, `false`, `docker`, `podman` или пользовательскую строку команды.
- **`SEATBELT_PROFILE`** (специфично для macOS):
  - Переключает профиль Seatbelt (`sandbox-exec`) на macOS.
  - `permissive-open`: (По умолчанию) Ограничивает запись в папку проекта (и несколько других папок, см. `packages/cli/src/utils/sandbox-macos-permissive-open.sb`), но разрешает другие операции.
  - `strict`: Использует строгий профиль, который по умолчанию отклоняет операции.
  - `<profile_name>`: Использует пользовательский профиль. Чтобы определить пользовательский профиль, создайте файл с именем `sandbox-macos-<profile_name>.sb` в каталоге `.gemini/` вашего проекта (например, `my-project/.gemini/sandbox-macos-custom.sb`).
- **`DEBUG` или `DEBUG_MODE`** (часто используется базовыми библиотеками или самим CLI):
  - Установите значение `true` или `1`, чтобы включить подробное логирование отладки, что может быть полезно для устранения неполадок.
- **`NO_COLOR`**:
  - Установите любое значение, чтобы отключить весь цветной вывод в CLI.
- **`CLI_TITLE`**:
  - Установите строку, чтобы настроить заголовок CLI.
- **`CODE_ASSIST_ENDPOINT`**:
  - Указывает конечную точку для сервера Code Assist.
  - Это полезно для разработки и тестирования.

## Аргументы командной строки

Аргументы, передаваемые непосредственно при запуске CLI, могут переопределять другие конфигурации для этого конкретного сеанса.

- **`--model <имя_модели>`** (**`-m <имя_модели>`**):
  - Указывает модель Gemini для использования в этом сеансе.
  - Пример: `npm start -- --model gemini-1.5-pro-latest`
- **`--prompt <ваш_запрос>`** (**`-p <ваш_запрос>`**):
  - Используется для передачи запроса непосредственно команде. Это вызывает Gemini CLI в неинтерактивном режиме.
- **`--prompt-interactive <ваш_запрос>`** (**`-i <ваш_запрос>`**):
  - Запускает интерактивный сеанс с предоставленным запросом в качестве начального ввода.
  - Запрос обрабатывается в интерактивном сеансе, а не до него.
  - Не может использоваться при передаче ввода из stdin.
  - Пример: `gemini -i "объясните этот код"`
- **`--sandbox`** (**`-s`**):
  - Включает режим песочницы для этого сеанса.
- **`--sandbox-image`**:
  - Устанавливает URI образа песочницы.
- **`--debug`** (**`-d`**):
  - Включает режим отладки для этого сеанса, предоставляя более подробный вывод.
- **`--all-files`** (**`-a`**):
  - Если установлено, рекурсивно включает все файлы в текущем каталоге в качестве контекста для запроса.
- **`--help`** (или **`-h`**):
  - Отображает справочную информацию об аргументах командной строки.
- **`--show-memory-usage`**:
  - Отображает текущее использование памяти.
- **`--yolo`**:
  - Включает режим YOLO, который автоматически одобряет все вызовы инструментов.
- **`--telemetry`**:
  - Включает [телеметрию](../telemetry.md).
- **`--telemetry-target`**:
  - Устанавливает цель телеметрии. Дополнительную информацию см. в разделе [телеметрия](../telemetry.md).
- **`--telemetry-otlp-endpoint`**:
  - Устанавливает конечную точку OTLP для телеметрии. Дополнительную информацию см. в разделе [телеметрия](../telemetry.md).
- **`--telemetry-log-prompts`**:
  - Включает логирование запросов для телеметрии. Дополнительную информацию см. в разделе [телеметрия](../telemetry.md).
- **`--checkpointing`**:
  - Включает [контрольные точки](../checkpointing.md).
- **`--extensions <имя_расширения ...>`** (**`-e <имя_расширения ...>`**):
  - Указывает список расширений для использования в сеансе. Если не указано, используются все доступные расширения.
  - Используйте специальный термин `gemini -e none`, чтобы отключить все расширения.
  - Пример: `gemini -e my-extension -e my-other-extension`
- **`--list-extensions`** (**`-l`**):
  - Перечисляет все доступные расширения и выходит.
- **`--proxy`**:
  - Устанавливает прокси для CLI.
  - Пример: `--proxy http://localhost:7890`.
- **`--include-directories <dir1,dir2,...>`**:
  - Включает дополнительные каталоги в рабочую область для поддержки нескольких каталогов.
  - Может быть указано несколько раз или в виде значений, разделенных запятыми.
  - Максимум можно добавить 5 каталогов.
  - Пример: `--include-directories /path/to/project1,/path/to/project2` или `--include-directories /path/to/project1 --include-directories /path/to/project2`
- **`--version`**:
  - Отображает версию CLI.

## Файлы контекста (иерархический контекст инструкций)

Хотя файлы контекста (по умолчанию `GEMINI.md`, но настраиваемые с помощью параметра `contextFileName`) не являются строго конфигурацией _поведения_ CLI, они имеют решающее значение для настройки _контекста инструкций_ (также называемого "памятью"), предоставляемого модели Gemini. Эта мощная функция позволяет вам предоставлять специфичные для проекта инструкции, руководства по стилю кодирования или любую соответствующую справочную информацию ИИ, делая его ответы более адаптированными и точными для ваших нужд. CLI включает элементы пользовательского интерфейса, такие как индикатор в нижнем колонтитуле, показывающий количество загруженных файлов контекста, чтобы держать вас в курсе активного контекста инструкций.

- **Назначение:** Эти файлы Markdown содержат инструкции, руководства или контекст, о которых вы хотите, чтобы модель Gemini знала во время ваших взаимодействий. Система предназначена для иерархического управления этим контекстом инструкций.

### Пример содержимого файла контекста (например, `GEMINI.md`)

Вот концептуальный пример того, что может содержать файл контекста в корне проекта TypeScript:

```markdown
# Проект: Моя потрясающая библиотека TypeScript

## Общие инструкции:

- При генерации нового кода TypeScript, пожалуйста, следуйте существующему стилю кодирования.
- Убедитесь, что все новые функции и классы имеют комментарии JSDoc.
- Предпочитайте парадигмы функционального программирования, где это уместно.
- Весь код должен быть совместим с TypeScript 5.0 и Node.js 20+.

## Стиль кодирования:

- Используйте 2 пробела для отступа.
- Имена интерфейсов должны иметь префикс `I` (например, `IUserService`).
- Закрытые члены класса должны иметь префикс подчеркивания (`_`).
- Всегда используйте строгое равенство (`===` и `!==`).

## Конкретный компонент: `src/api/client.ts`

- Этот файл обрабатывает все исходящие запросы API.
- При добавлении новых функций вызова API убедитесь, что они включают надежную обработку ошибок и логирование.
- Используйте существующую утилиту `fetchWithRetry` для всех GET-запросов.

## Относительно зависимостей:

- Избегайте введения новых внешних зависимостей, если это абсолютно необходимо.
- Если требуется новая зависимость, пожалуйста, укажите причину.
```

Этот пример демонстрирует, как вы можете предоставить общий контекст проекта, конкретные соглашения о кодировании и даже заметки о конкретных файлах или компонентах. Чем более релевантны и точны ваши файлы контекста, тем лучше ИИ сможет вам помочь. Файлы контекста, специфичные для проекта, настоятельно рекомендуются для установления соглашений и контекста.

- **Иерархическая загрузка и приоритет:** CLI реализует сложную иерархическую систему памяти, загружая файлы контекста (например, `GEMINI.md`) из нескольких мест. Содержимое файлов, расположенных ниже в этом списке (более специфичных), обычно переопределяет или дополняет содержимое файлов, расположенных выше (более общих). Точный порядок объединения и окончательный контекст можно проверить с помощью команды `/memory show`. Типичный порядок загрузки:
  1.  **Глобальный файл контекста:**
      - Расположение: `~/.gemini/<contextFileName>` (например, `~/.gemini/GEMINI.md` в вашем домашнем каталоге пользователя).
      - Область действия: Предоставляет инструкции по умолчанию для всех ваших проектов.
  2.  **Корневой каталог проекта и файлы контекста предков:**
      - Расположение: CLI ищет настроенный файл контекста в текущем рабочем каталоге, а затем в каждом родительском каталоге до корня проекта (идентифицируемого папкой `.git`) или вашего домашнего каталога.
      - Область действия: Предоставляет контекст, относящийся ко всему проекту или значительной его части.
  3.  **Файлы контекста подкаталогов (контекстные/локальные):**
      - Расположение: CLI также сканирует настроенный файл контекста в подкаталогах _ниже_ текущего рабочего каталога (с учетом общих шаблонов игнорирования, таких как `node_modules`, `.git` и т. д.). Ширина этого поиска ограничена 200 каталогами по умолчанию, но может быть настроена с помощью поля `memoryDiscoveryMaxDirs` в вашем файле `settings.json`.
      - Область действия: Позволяет получать очень специфичные инструкции, относящиеся к конкретному компоненту, модулю или подразделу вашего проекта.
- **Объединение и индикация пользовательского интерфейса:** Содержимое всех найденных файлов контекста объединяется (с разделителями, указывающими их происхождение и путь) и предоставляется как часть системного запроса модели Gemini. Нижний колонтитул CLI отображает количество загруженных файлов контекста, давая вам быстрый визуальный сигнал об активном контексте инструкций.
- **Импорт содержимого:** Вы можете модулировать свои файлы контекста, импортируя другие файлы Markdown с использованием синтаксиса `@path/to/file.md`. Дополнительные сведения см. в [документации по процессору импорта памяти](../core/memport.md).
- **Команды для управления памятью:**
  - Используйте `/memory refresh` для принудительного повторного сканирования и перезагрузки всех файлов контекста из всех настроенных мест. Это обновляет контекст инструкций ИИ.
  - Используйте `/memory show` для отображения объединенного контекста инструкций, загруженного в настоящее время, что позволяет вам проверять иерархию и содержимое, используемое ИИ.
  - Полные сведения о команде `/memory` и ее подкомандах (`show` и `refresh`) см. в [документации по командам](./commands.md#memory).

Понимая и используя эти уровни конфигурации и иерархическую природу файлов контекста, вы можете эффективно управлять памятью ИИ и адаптировать ответы Gemini CLI к вашим конкретным потребностям и проектам.

## Песочница

Gemini CLI может выполнять потенциально небезопасные операции (такие как команды оболочки и изменения файлов) в изолированной среде для защиты вашей системы.

Песочница по умолчанию отключена, но вы можете включить ее несколькими способами:

- Использование флага `--sandbox` или `-s`.
- Установка переменной среды `GEMINI_SANDBOX`.
- Песочница включена в режиме `--yolo` по умолчанию.

По умолчанию используется предварительно собранный образ Docker `gemini-cli-sandbox`.

Для нужд песочницы, специфичных для проекта, вы можете создать пользовательский Dockerfile по адресу `.gemini/sandbox.Dockerfile` в корневом каталоге вашего проекта. Этот Dockerfile может быть основан на базовом образе песочницы:

```dockerfile
FROM gemini-cli-sandbox

# Добавьте сюда свои пользовательские зависимости или конфигурации
# Например:
# RUN apt-get update && apt-get install -y some-package
# COPY ./my-config /app/my-config
```

Когда `.gemini/sandbox.Dockerfile` существует, вы можете использовать переменную среды `BUILD_SANDBOX` при запуске Gemini CLI для автоматической сборки пользовательского образа песочницы:

```bash
BUILD_SANDBOX=1 gemini -s
```

## Статистика использования

Чтобы помочь нам улучшить Gemini CLI, мы собираем анонимную статистику использования. Эти данные помогают нам понять, как используется CLI, выявить распространенные проблемы и определить приоритеты для новых функций.

**Что мы собираем:**

- **Вызовы инструментов:** Мы регистрируем имена вызываемых инструментов, успешно ли они выполнены или завершились сбоем, и сколько времени занимает их выполнение. Мы не собираем аргументы, передаваемые инструментам, или какие-либо данные, возвращаемые ими.
- **Запросы API:** Мы регистрируем используемую модель Gemini для каждого запроса, продолжительность запроса и его успешность. Мы не собираем содержимое запросов или ответов.
- **Информация о сеансе:** Мы собираем информацию о конфигурации CLI, такую как включенные инструменты и режим утверждения.

**Что мы НЕ собираем:**

- **Личная информация (PII):** Мы не собираем никакой личной информации, такой как ваше имя, адрес электронной почты или ключи API.
- **Содержимое запросов и ответов:** Мы не регистрируем содержимое ваших запросов или ответов от модели Gemini.
- **Содержимое файлов:** Мы не регистрируем содержимое любых файлов, которые читаются или записываются CLI.

**Как отказаться:**

Вы можете отказаться от сбора статистики использования в любое время, установив свойство `usageStatisticsEnabled` в `false` в вашем файле `settings.json`:

```json
{
  "usageStatisticsEnabled": false
}
```


### The `.gemini` directory in your project

In addition to a project settings file, a project's `.gemini` directory can contain other project-specific files related to Gemini CLI's operation, such as:

- [Custom sandbox profiles](#sandboxing) (e.g., `.gemini/sandbox-macos-custom.sb`, `.gemini/sandbox.Dockerfile`).

### Available settings in `settings.json`:

- **`contextFileName`** (string or array of strings):
  - **Description:** Specifies the filename for context files (e.g., `GEMINI.md`, `AGENTS.md`). Can be a single filename or a list of accepted filenames.
  - **Default:** `GEMINI.md`
  - **Example:** `"contextFileName": "AGENTS.md"`

- **`bugCommand`** (object):
  - **Description:** Overrides the default URL for the `/bug` command.
  - **Default:** `"urlTemplate": "https://github.com/google-gemini/gemini-cli/issues/new?template=bug_report.yml&title={title}&info={info}"`
  - **Properties:**
    - **`urlTemplate`** (string): A URL that can contain `{title}` and `{info}` placeholders.
  - **Example:**
    ```json
    "bugCommand": {
      "urlTemplate": "https://bug.example.com/new?title={title}&info={info}"
    }
    ```

- **`fileFiltering`** (object):
  - **Description:** Controls git-aware file filtering behavior for @ commands and file discovery tools.
  - **Default:** `"respectGitIgnore": true, "enableRecursiveFileSearch": true`
  - **Properties:**
    - **`respectGitIgnore`** (boolean): Whether to respect .gitignore patterns when discovering files. When set to `true`, git-ignored files (like `node_modules/`, `dist/`, `.env`) are automatically excluded from @ commands and file listing operations.
    - **`enableRecursiveFileSearch`** (boolean): Whether to enable searching recursively for filenames under the current tree when completing @ prefixes in the prompt.
  - **Example:**
    ```json
    "fileFiltering": {
      "respectGitIgnore": true,
      "enableRecursiveFileSearch": false
    }
    ```

- **`coreTools`** (array of strings):
  - **Description:** Allows you to specify a list of core tool names that should be made available to the model. This can be used to restrict the set of built-in tools. See [Built-in Tools](../core/tools-api.md#built-in-tools) for a list of core tools. You can also specify command-specific restrictions for tools that support it, like the `ShellTool`. For example, `"coreTools": ["ShellTool(ls -l)"]` will only allow the `ls -l` command to be executed.
  - **Default:** All tools available for use by the Gemini model.
  - **Example:** `"coreTools": ["ReadFileTool", "GlobTool", "ShellTool(ls)"]`.

- **`excludeTools`** (array of strings):
  - **Description:** Allows you to specify a list of core tool names that should be excluded from the model. A tool listed in both `excludeTools` and `coreTools` is excluded. You can also specify command-specific restrictions for tools that support it, like the `ShellTool`. For example, `"excludeTools": ["ShellTool(rm -rf)"]` will block the `rm -rf` command.
  - **Default**: No tools excluded.
  - **Example:** `"excludeTools": ["run_shell_command", "findFiles"]`.
  - **Security Note:** Command-specific restrictions in
    `excludeTools` for `run_shell_command` are based on simple string matching and can be easily bypassed. This feature is **not a security mechanism** and should not be relied upon to safely execute untrusted code. It is recommended to use `coreTools` to explicitly select commands
    that can be executed.

- **`allowMCPServers`** (array of strings):
  - **Description:** Allows you to specify a list of MCP server names that should be made available to the model. This can be used to restrict the set of MCP servers to connect to. Note that this will be ignored if `--allowed-mcp-server-names` is set.
  - **Default:** All MCP servers are available for use by the Gemini model.
  - **Example:** `"allowMCPServers": ["myPythonServer"]`.
  - **Security Note:** This uses simple string matching on MCP server names, which can be modified. If you're a system administrator looking to prevent users from bypassing this, consider configuring the `mcpServers` at the system settings level such that the user will not be able to configure any MCP servers of their own. This should not be used as an airtight security mechanism.

- **`excludeMCPServers`** (array of strings):
  - **Description:** Allows you to specify a list of MCP server names that should be excluded from the model. A server listed in both `excludeMCPServers` and `allowMCPServers` is excluded. Note that this will be ignored if `--allowed-mcp-server-names` is set.
  - **Default**: No MCP servers excluded.
  - **Example:** `"excludeMCPServers": ["myNodeServer"]`.
  - **Security Note:** This uses simple string matching on MCP server names, which can be modified. If you're a system administrator looking to prevent users from bypassing this, consider configuring the `mcpServers` at the system settings level such that the user will not be able to configure any MCP servers of their own. This should not be used as an airtight security mechanism.

- **`autoAccept`** (boolean):
  - **Description:** Controls whether the CLI automatically accepts and executes tool calls that are considered safe (e.g., read-only operations) without explicit user confirmation. If set to `true`, the CLI will bypass the confirmation prompt for tools deemed safe.
  - **Default:** `false`
  - **Example:** `"autoAccept": true`

- **`theme`** (string):
  - **Description:** Sets the visual [theme](./themes.md) for Gemini CLI.
  - **Default:** `"Default"`
  - **Example:** `"theme": "GitHub"`

- **`vimMode`** (boolean):
  - **Description:** Enables or disables vim mode for input editing. When enabled, the input area supports vim-style navigation and editing commands with NORMAL and INSERT modes. The vim mode status is displayed in the footer and persists between sessions.
  - **Default:** `false`
  - **Example:** `"vimMode": true`

- **`sandbox`** (boolean or string):
  - **Description:** Controls whether and how to use sandboxing for tool execution. If set to `true`, Gemini CLI uses a pre-built `gemini-cli-sandbox` Docker image. For more information, see [Sandboxing](#sandboxing).
  - **Default:** `false`
  - **Example:** `"sandbox": "docker"`

- **`toolDiscoveryCommand`** (string):
  - **Description:** Defines a custom shell command for discovering tools from your project. The shell command must return on `stdout` a JSON array of [function declarations](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations). Tool wrappers are optional.
  - **Default:** Empty
  - **Example:** `"toolDiscoveryCommand": "bin/get_tools"`

- **`toolCallCommand`** (string):
  - **Description:** Defines a custom shell command for calling a specific tool that was discovered using `toolDiscoveryCommand`. The shell command must meet the following criteria:
    - It must take function `name` (exactly as in [function declaration](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations)) as first command line argument.
    - It must read function arguments as JSON on `stdin`, analogous to [`functionCall.args`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functioncall).
    - It must return function output as JSON on `stdout`, analogous to [`functionResponse.response.content`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functionresponse).
  - **Default:** Empty
  - **Example:** `"toolCallCommand": "bin/call_tool"`

- **`mcpServers`** (object):
  - **Description:** Configures connections to one or more Model-Context Protocol (MCP) servers for discovering and using custom tools. Gemini CLI attempts to connect to each configured MCP server to discover available tools. If multiple MCP servers expose a tool with the same name, the tool names will be prefixed with the server alias you defined in the configuration (e.g., `serverAlias__actualToolName`) to avoid conflicts. Note that the system might strip certain schema properties from MCP tool definitions for compatibility.
  - **Default:** Empty
  - **Properties:**
    - **`<SERVER_NAME>`** (object): The server parameters for the named server.
      - `command` (string, required): The command to execute to start the MCP server.
      - `args` (array of strings, optional): Arguments to pass to the command.
      - `env` (object, optional): Environment variables to set for the server process.
      - `cwd` (string, optional): The working directory in which to start the server.
      - `timeout` (number, optional): Timeout in milliseconds for requests to this MCP server.
      - `trust` (boolean, optional): Trust this server and bypass all tool call confirmations.
      - `includeTools` (array of strings, optional): List of tool names to include from this MCP server. When specified, only the tools listed here will be available from this server (whitelist behavior). If not specified, all tools from the server are enabled by default.
      - `excludeTools` (array of strings, optional): List of tool names to exclude from this MCP server. Tools listed here will not be available to the model, even if they are exposed by the server. **Note:** `excludeTools` takes precedence over `includeTools` - if a tool is in both lists, it will be excluded.
  - **Example:**
    ```json
    "mcpServers": {
      "myPythonServer": {
        "command": "python",
        "args": ["mcp_server.py", "--port", "8080"],
        "cwd": "./mcp_tools/python",
        "timeout": 5000,
        "includeTools": ["safe_tool", "file_reader"],
      },
      "myNodeServer": {
        "command": "node",
        "args": ["mcp_server.js"],
        "cwd": "./mcp_tools/node",
        "excludeTools": ["dangerous_tool", "file_deleter"]
      },
      "myDockerServer": {
        "command": "docker",
        "args": ["run", "-i", "--rm", "-e", "API_KEY", "ghcr.io/foo/bar"],
        "env": {
          "API_KEY": "$MY_API_TOKEN"
        }
      }
    }
    ```

- **`checkpointing`** (object):
  - **Description:** Configures the checkpointing feature, which allows you to save and restore conversation and file states. See the [Checkpointing documentation](../checkpointing.md) for more details.
  - **Default:** `{"enabled": false}`
  - **Properties:**
    - **`enabled`** (boolean): When `true`, the `/restore` command is available.

- **`preferredEditor`** (string):
  - **Description:** Specifies the preferred editor to use for viewing diffs.
  - **Default:** `vscode`
  - **Example:** `"preferredEditor": "vscode"`

- **`telemetry`** (object)
  - **Description:** Configures logging and metrics collection for Gemini CLI. For more information, see [Telemetry](../telemetry.md).
  - **Default:** `{"enabled": false, "target": "local", "otlpEndpoint": "http://localhost:4317", "logPrompts": true}`
  - **Properties:**
    - **`enabled`** (boolean): Whether or not telemetry is enabled.
    - **`target`** (string): The destination for collected telemetry. Supported values are `local` and `gcp`.
    - **`otlpEndpoint`** (string): The endpoint for the OTLP Exporter.
    - **`logPrompts`** (boolean): Whether or not to include the content of user prompts in the logs.
  - **Example:**
    ```json
    "telemetry": {
      "enabled": true,
      "target": "local",
      "otlpEndpoint": "http://localhost:16686",
      "logPrompts": false
    }
    ```
- **`usageStatisticsEnabled`** (boolean):
  - **Description:** Enables or disables the collection of usage statistics. See [Usage Statistics](#usage-statistics) for more information.
  - **Default:** `true`
  - **Example:**
    ```json
    "usageStatisticsEnabled": false
    ```

- **`hideTips`** (boolean):
  - **Description:** Enables or disables helpful tips in the CLI interface.
  - **Default:** `false`
  - **Example:**

    ```json
    "hideTips": true
    ```

- **`hideBanner`** (boolean):
  - **Description:** Enables or disables the startup banner (ASCII art logo) in the CLI interface.
  - **Default:** `false`
  - **Example:**

    ```json
    "hideBanner": true
    ```

- **`maxSessionTurns`** (number):
  - **Description:** Sets the maximum number of turns for a session. If the session exceeds this limit, the CLI will stop processing and start a new chat.
  - **Default:** `-1` (unlimited)
  - **Example:**
    ```json
    "maxSessionTurns": 10
    ```

- **`summarizeToolOutput`** (object):
  - **Description:** Enables or disables the summarization of tool output. You can specify the token budget for the summarization using the `tokenBudget` setting.
  - Note: Currently only the `run_shell_command` tool is supported.
  - **Default:** `{}` (Disabled by default)
  - **Example:**
    ```json
    "summarizeToolOutput": {
      "run_shell_command": {
        "tokenBudget": 2000
      }
    }
    ```

### Example `settings.json`:

```json
{
  "theme": "GitHub",
  "sandbox": "docker",
  "toolDiscoveryCommand": "bin/get_tools",
  "toolCallCommand": "bin/call_tool",
  "mcpServers": {
    "mainServer": {
      "command": "bin/mcp_server.py"
    },
    "anotherServer": {
      "command": "node",
      "args": ["mcp_server.js", "--verbose"]
    }
  },
  "telemetry": {
    "enabled": true,
    "target": "local",
    "otlpEndpoint": "http://localhost:4317",
    "logPrompts": true
  },
  "usageStatisticsEnabled": true,
  "hideTips": false,
  "hideBanner": false,
  "maxSessionTurns": 10,
  "summarizeToolOutput": {
    "run_shell_command": {
      "tokenBudget": 100
    }
  }
}
```

## Shell History

The CLI keeps a history of shell commands you run. To avoid conflicts between different projects, this history is stored in a project-specific directory within your user's home folder.

- **Location:** `~/.gemini/tmp/<project_hash>/shell_history`
  - `<project_hash>` is a unique identifier generated from your project's root path.
  - The history is stored in a file named `shell_history`.

## Environment Variables & `.env` Files

Environment variables are a common way to configure applications, especially for sensitive information like API keys or for settings that might change between environments.

The CLI automatically loads environment variables from an `.env` file. The loading order is:

1.  `.env` file in the current working directory.
2.  If not found, it searches upwards in parent directories until it finds an `.env` file or reaches the project root (identified by a `.git` folder) or the home directory.
3.  If still not found, it looks for `~/.env` (in the user's home directory).

- **`GEMINI_API_KEY`** (Required):
  - Your API key for the Gemini API.
  - **Crucial for operation.** The CLI will not function without it.
  - Set this in your shell profile (e.g., `~/.bashrc`, `~/.zshrc`) or an `.env` file.
- **`GEMINI_MODEL`**:
  - Specifies the default Gemini model to use.
  - Overrides the hardcoded default
  - Example: `export GEMINI_MODEL="gemini-2.5-flash"`
- **`GOOGLE_API_KEY`**:
  - Your Google Cloud API key.
  - Required for using Vertex AI in express mode.
  - Ensure you have the necessary permissions.
  - Example: `export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"`.
- **`GOOGLE_CLOUD_PROJECT`**:
  - Your Google Cloud Project ID.
  - Required for using Code Assist or Vertex AI.
  - If using Vertex AI, ensure you have the necessary permissions in this project.
  - **Cloud Shell Note:** When running in a Cloud Shell environment, this variable defaults to a special project allocated for Cloud Shell users. If you have `GOOGLE_CLOUD_PROJECT` set in your global environment in Cloud Shell, it will be overridden by this default. To use a different project in Cloud Shell, you must define `GOOGLE_CLOUD_PROJECT` in a `.env` file.
  - Example: `export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"`.
- **`GOOGLE_APPLICATION_CREDENTIALS`** (string):
  - **Description:** The path to your Google Application Credentials JSON file.
  - **Example:** `export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/credentials.json"`
- **`OTLP_GOOGLE_CLOUD_PROJECT`**:
  - Your Google Cloud Project ID for Telemetry in Google Cloud
  - Example: `export OTLP_GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"`.
- **`GOOGLE_CLOUD_LOCATION`**:
  - Your Google Cloud Project Location (e.g., us-central1).
  - Required for using Vertex AI in non express mode.
  - Example: `export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION"`.
- **`GEMINI_SANDBOX`**:
  - Alternative to the `sandbox` setting in `settings.json`.
  - Accepts `true`, `false`, `docker`, `podman`, or a custom command string.
- **`SEATBELT_PROFILE`** (macOS specific):
  - Switches the Seatbelt (`sandbox-exec`) profile on macOS.
  - `permissive-open`: (Default) Restricts writes to the project folder (and a few other folders, see `packages/cli/src/utils/sandbox-macos-permissive-open.sb`) but allows other operations.
  - `strict`: Uses a strict profile that declines operations by default.
  - `<profile_name>`: Uses a custom profile. To define a custom profile, create a file named `sandbox-macos-<profile_name>.sb` in your project's `.gemini/` directory (e.g., `my-project/.gemini/sandbox-macos-custom.sb`).
- **`DEBUG` or `DEBUG_MODE`** (often used by underlying libraries or the CLI itself):
  - Set to `true` or `1` to enable verbose debug logging, which can be helpful for troubleshooting.
- **`NO_COLOR`**:
  - Set to any value to disable all color output in the CLI.
- **`CLI_TITLE`**:
  - Set to a string to customize the title of the CLI.
- **`CODE_ASSIST_ENDPOINT`**:
  - Specifies the endpoint for the code assist server.
  - This is useful for development and testing.

## Command-Line Arguments

Arguments passed directly when running the CLI can override other configurations for that specific session.

- **`--model <model_name>`** (**`-m <model_name>`**):
  - Specifies the Gemini model to use for this session.
  - Example: `npm start -- --model gemini-1.5-pro-latest`
- **`--prompt <your_prompt>`** (**`-p <your_prompt>`**):
  - Used to pass a prompt directly to the command. This invokes Gemini CLI in a non-interactive mode.
- **`--prompt-interactive <your_prompt>`** (**`-i <your_prompt>`**):
  - Starts an interactive session with the provided prompt as the initial input.
  - The prompt is processed within the interactive session, not before it.
  - Cannot be used when piping input from stdin.
  - Example: `gemini -i "explain this code"`
- **`--sandbox`** (**`-s`**):
  - Enables sandbox mode for this session.
- **`--sandbox-image`**:
  - Sets the sandbox image URI.
- **`--debug`** (**`-d`**):
  - Enables debug mode for this session, providing more verbose output.
- **`--all-files`** (**`-a`**):
  - If set, recursively includes all files within the current directory as context for the prompt.
- **`--help`** (or **`-h`**):
  - Displays help information about command-line arguments.
- **`--show-memory-usage`**:
  - Displays the current memory usage.
- **`--yolo`**:
  - Enables YOLO mode, which automatically approves all tool calls.
- **`--telemetry`**:
  - Enables [telemetry](../telemetry.md).
- **`--telemetry-target`**:
  - Sets the telemetry target. See [telemetry](../telemetry.md) for more information.
- **`--telemetry-otlp-endpoint`**:
  - Sets the OTLP endpoint for telemetry. See [telemetry](../telemetry.md) for more information.
- **`--telemetry-log-prompts`**:
  - Enables logging of prompts for telemetry. See [telemetry](../telemetry.md) for more information.
- **`--checkpointing`**:
  - Enables [checkpointing](../checkpointing.md).
- **`--extensions <extension_name ...>`** (**`-e <extension_name ...>`**):
  - Specifies a list of extensions to use for the session. If not provided, all available extensions are used.
  - Use the special term `gemini -e none` to disable all extensions.
  - Example: `gemini -e my-extension -e my-other-extension`
- **`--list-extensions`** (**`-l`**):
  - Lists all available extensions and exits.
- **`--proxy`**:
  - Sets the proxy for the CLI.
  - Example: `--proxy http://localhost:7890`.
- **`--include-directories <dir1,dir2,...>`**:
  - Includes additional directories in the workspace for multi-directory support.
  - Can be specified multiple times or as comma-separated values.
  - 5 directories can be added at maximum.
  - Example: `--include-directories /path/to/project1,/path/to/project2` or `--include-directories /path/to/project1 --include-directories /path/to/project2`
- **`--version`**:
  - Displays the version of the CLI.

## Context Files (Hierarchical Instructional Context)

While not strictly configuration for the CLI's _behavior_, context files (defaulting to `GEMINI.md` but configurable via the `contextFileName` setting) are crucial for configuring the _instructional context_ (also referred to as "memory") provided to the Gemini model. This powerful feature allows you to give project-specific instructions, coding style guides, or any relevant background information to the AI, making its responses more tailored and accurate to your needs. The CLI includes UI elements, such as an indicator in the footer showing the number of loaded context files, to keep you informed about the active context.

- **Purpose:** These Markdown files contain instructions, guidelines, or context that you want the Gemini model to be aware of during your interactions. The system is designed to manage this instructional context hierarchically.

### Example Context File Content (e.g., `GEMINI.md`)

Here's a conceptual example of what a context file at the root of a TypeScript project might contain:

```markdown
# Project: My Awesome TypeScript Library

## General Instructions:

- When generating new TypeScript code, please follow the existing coding style.
- Ensure all new functions and classes have JSDoc comments.
- Prefer functional programming paradigms where appropriate.
- All code should be compatible with TypeScript 5.0 and Node.js 20+.

## Coding Style:

- Use 2 spaces for indentation.
- Interface names should be prefixed with `I` (e.g., `IUserService`).
- Private class members should be prefixed with an underscore (`_`).
- Always use strict equality (`===` and `!==`).

## Specific Component: `src/api/client.ts`

- This file handles all outbound API requests.
- When adding new API call functions, ensure they include robust error handling and logging.
- Use the existing `fetchWithRetry` utility for all GET requests.

## Regarding Dependencies:

- Avoid introducing new external dependencies unless absolutely necessary.
- If a new dependency is required, please state the reason.
```

This example demonstrates how you can provide general project context, specific coding conventions, and even notes about particular files or components. The more relevant and precise your context files are, the better the AI can assist you. Project-specific context files are highly encouraged to establish conventions and context.

- **Hierarchical Loading and Precedence:** The CLI implements a sophisticated hierarchical memory system by loading context files (e.g., `GEMINI.md`) from several locations. Content from files lower in this list (more specific) typically overrides or supplements content from files higher up (more general). The exact concatenation order and final context can be inspected using the `/memory show` command. The typical loading order is:
  1.  **Global Context File:**
      - Location: `~/.gemini/<contextFileName>` (e.g., `~/.gemini/GEMINI.md` in your user home directory).
      - Scope: Provides default instructions for all your projects.
  2.  **Project Root & Ancestors Context Files:**
      - Location: The CLI searches for the configured context file in the current working directory and then in each parent directory up to either the project root (identified by a `.git` folder) or your home directory.
      - Scope: Provides context relevant to the entire project or a significant portion of it.
  3.  **Sub-directory Context Files (Contextual/Local):**
      - Location: The CLI also scans for the configured context file in subdirectories _below_ the current working directory (respecting common ignore patterns like `node_modules`, `.git`, etc.). The breadth of this search is limited to 200 directories by default, but can be configured with a `memoryDiscoveryMaxDirs` field in your `settings.json` file.
      - Scope: Allows for highly specific instructions relevant to a particular component, module, or subsection of your project.
- **Concatenation & UI Indication:** The contents of all found context files are concatenated (with separators indicating their origin and path) and provided as part of the system prompt to the Gemini model. The CLI footer displays the count of loaded context files, giving you a quick visual cue about the active instructional context.
- **Importing Content:** You can modularize your context files by importing other Markdown files using the `@path/to/file.md` syntax. For more details, see the [Memory Import Processor documentation](../core/memport.md).
- **Commands for Memory Management:**
  - Use `/memory refresh` to force a re-scan and reload of all context files from all configured locations. This updates the AI's instructional context.
  - Use `/memory show` to display the combined instructional context currently loaded, allowing you to verify the hierarchy and content being used by the AI.
  - See the [Commands documentation](./commands.md#memory) for full details on the `/memory` command and its sub-commands (`show` and `refresh`).

By understanding and utilizing these configuration layers and the hierarchical nature of context files, you can effectively manage the AI's memory and tailor the Gemini CLI's responses to your specific needs and projects.

## Sandboxing

The Gemini CLI can execute potentially unsafe operations (like shell commands and file modifications) within a sandboxed environment to protect your system.

Sandboxing is disabled by default, but you can enable it in a few ways:

- Using `--sandbox` or `-s` flag.
- Setting `GEMINI_SANDBOX` environment variable.
- Sandbox is enabled in `--yolo` mode by default.

By default, it uses a pre-built `gemini-cli-sandbox` Docker image.

For project-specific sandboxing needs, you can create a custom Dockerfile at `.gemini/sandbox.Dockerfile` in your project's root directory. This Dockerfile can be based on the base sandbox image:

```dockerfile
FROM gemini-cli-sandbox

# Add your custom dependencies or configurations here
# For example:
# RUN apt-get update && apt-get install -y some-package
# COPY ./my-config /app/my-config
```

When `.gemini/sandbox.Dockerfile` exists, you can use `BUILD_SANDBOX` environment variable when running Gemini CLI to automatically build the custom sandbox image:

```bash
BUILD_SANDBOX=1 gemini -s
```

## Usage Statistics

To help us improve the Gemini CLI, we collect anonymized usage statistics. This data helps us understand how the CLI is used, identify common issues, and prioritize new features.

**What we collect:**

- **Tool Calls:** We log the names of the tools that are called, whether they succeed or fail, and how long they take to execute. We do not collect the arguments passed to the tools or any data returned by them.
- **API Requests:** We log the Gemini model used for each request, the duration of the request, and whether it was successful. We do not collect the content of the prompts or responses.
- **Session Information:** We collect information about the configuration of the CLI, such as the enabled tools and the approval mode.

**What we DON'T collect:**

- **Personally Identifiable Information (PII):** We do not collect any personal information, such as your name, email address, or API keys.
- **Prompt and Response Content:** We do not log the content of your prompts or the responses from the Gemini model.
- **File Content:** We do not log the content of any files that are read or written by the CLI.

**How to opt out:**

You can opt out of usage statistics collection at any time by setting the `usageStatisticsEnabled` property to `false` in your `settings.json` file:

```json
{
  "usageStatisticsEnabled": false
}
```
