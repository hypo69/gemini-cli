# Gemini CLI Observability Guide

Telemetry provides data about Gemini CLI's performance, health, and usage. By enabling it, you can monitor operations, debug issues, and optimize tool usage through traces, metrics, and structured logs.

Gemini CLI's telemetry system is built on the **[OpenTelemetry] (OTEL)** standard, allowing you to send data to any compatible backend.

[OpenTelemetry]: https://opentelemetry.io/

## Enabling telemetry

You can enable telemetry in multiple ways. Configuration is primarily managed via the [`.gemini/settings.json` file](./cli/configuration.md) and environment variables, but CLI flags can override these settings for a specific session.

### Order of precedence

The following lists the precedence for applying telemetry settings, with items listed higher having greater precedence:

1.  **CLI flags (for `gemini` command):**
    - `--telemetry` / `--no-telemetry`: Overrides `telemetry.enabled`.
    - `--telemetry-target <local|gcp>`: Overrides `telemetry.target`.
    - `--telemetry-otlp-endpoint <URL>`: Overrides `telemetry.otlpEndpoint`.
    - `--telemetry-log-prompts` / `--no-telemetry-log-prompts`: Overrides `telemetry.logPrompts`.
    - `--telemetry-outfile <path>`: Redirects telemetry output to a file. See [Exporting to a file](#exporting-to-a-file).

1.  **Environment variables:**
    - `OTEL_EXPORTER_OTLP_ENDPOINT`: Overrides `telemetry.otlpEndpoint`.

1.  **Workspace settings file (`.gemini/settings.json`):** Values from the `telemetry` object in this project-specific file.

1.  **User settings file (`~/.gemini/settings.json`):** Values from the `telemetry` object in this global user file.

1.  **Defaults:** applied if not set by any of the above.
    - `telemetry.enabled`: `false`
    - `telemetry.target`: `local`
    - `telemetry.otlpEndpoint`: `http://localhost:4317`
    - `telemetry.logPrompts`: `true`

**For the `npm run telemetry -- --target=<gcp|local>` script:**
The `--target` argument to this script _only_ overrides the `telemetry.target` for the duration and purpose of that script (i.e., choosing which collector to start). It does not permanently change your `settings.json`. The script will first look at `settings.json` for a `telemetry.target` to use as its default.

### Example settings

The following code can be added to your workspace (`.gemini/settings.json`) or user (`~/.gemini/settings.json`) settings to enable telemetry and send the output to Google Cloud:

```json
{
  "telemetry": {
    "enabled": true,
    "target": "gcp"
  },
  "sandbox": false
}
```

### Exporting to a file

You can export all telemetry data to a file for local inspection.

To enable file export, use the `--telemetry-outfile` flag with a path to your desired output file. This must be run using `--telemetry-target=local`.

```bash
# Руководство по наблюдаемости Gemini CLI

Телеметрия предоставляет данные о производительности, работоспособности и использовании Gemini CLI. Включив ее, вы можете отслеживать операции, отлаживать проблемы и оптимизировать использование инструментов с помощью трассировок, метрик и структурированных журналов.

Система телеметрии Gemini CLI построена на стандарте **[OpenTelemetry] (OTEL)**, что позволяет отправлять данные в любую совместимую серверную часть.

[OpenTelemetry]: https://opentelemetry.io/

## Включение телеметрии

Вы можете включить телеметрию несколькими способами. Конфигурация в основном управляется через файл [`.gemini/settings.json`](./cli/configuration.md) и переменные среды, но флаги CLI могут переопределять эти настройки для конкретного сеанса.

### Порядок приоритета

Ниже приведен список приоритетов для применения настроек телеметрии, причем элементы, перечисленные выше, имеют больший приоритет:

1.  **Флаги CLI (для команды `gemini`):**
    - `--telemetry` / `--no-telemetry`: Переопределяет `telemetry.enabled`.
    - `--telemetry-target <local|gcp>`: Переопределяет `telemetry.target`.
    - `--telemetry-otlp-endpoint <URL>`: Переопределяет `telemetry.otlpEndpoint`.
    - `--telemetry-log-prompts` / `--no-telemetry-log-prompts`: Переопределяет `telemetry.logPrompts`.
    - `--telemetry-outfile <path>`: Перенаправляет вывод телеметрии в файл. См. [Экспорт в файл](#exporting-to-a-file).

1.  **Переменные среды:**
    - `OTEL_EXPORTER_OTLP_ENDPOINT`: Переопределяет `telemetry.otlpEndpoint`.

1.  **Файл настроек рабочей области (`.gemini/settings.json`):** Значения из объекта `telemetry` в этом файле, специфичном для проекта.

1.  **Файл пользовательских настроек (`~/.gemini/settings.json`):** Значения из объекта `telemetry` в этом глобальном пользовательском файле.

1.  **Значения по умолчанию:** применяются, если не установлены ни одним из вышеперечисленных.
    - `telemetry.enabled`: `false`
    - `telemetry.target`: `local`
    - `telemetry.otlpEndpoint`: `http://localhost:4317`
    - `telemetry.logPrompts`: `true`

**Для скрипта `npm run telemetry -- --target=<gcp|local>`:**
Аргумент `--target` для этого скрипта _только_ переопределяет `telemetry.target` на время и для целей этого скрипта (т.е. выбор коллектора для запуска). Он не изменяет постоянно ваш `settings.json`. Скрипт сначала будет искать `telemetry.target` в `settings.json` для использования в качестве значения по умолчанию.

### Пример настроек

Следующий код может быть добавлен в настройки вашей рабочей области (`.gemini/settings.json`) или пользователя (`~/.gemini/settings.json`), чтобы включить телеметрию и отправить вывод в Google Cloud:

```json
{
  "telemetry": {
    "enabled": true,
    "target": "gcp"
  },
  "sandbox": false
}
```

### Экспорт в файл

Вы можете экспортировать все данные телеметрии в файл для локальной проверки.

Чтобы включить экспорт файлов, используйте флаг `--telemetry-outfile` с путем к желаемому выходному файлу. Это должно быть запущено с использованием `--telemetry-target=local`.

```bash
gemini --telemetry --telemetry-target=local --telemetry-outfile=/path/to/telemetry.log "ваш запрос"
```

## Запуск коллектора OTEL

Коллектор OTEL — это служба, которая получает, обрабатывает и экспортирует данные телеметрии.
CLI отправляет данные с использованием протокола OTLP/gRPC.

Узнайте больше о стандартной конфигурации экспортера OTEL в [документации][otel-config-docs].

[otel-config-docs]: https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter/

### Локально

Используйте команду `npm run telemetry -- --target=local` для автоматизации процесса настройки локального конвейера телеметрии, включая настройку необходимых параметров в файле `.gemini/settings.json`. Базовый скрипт устанавливает `otelcol-contrib` (коллектор OpenTelemetry) и `jaeger` (пользовательский интерфейс Jaeger для просмотра трассировок). Чтобы использовать его:

1.  **Запустите команду**:
    Выполните команду из корня репозитория:

    ```bash
    npm run telemetry -- --target=local
    ```

    Скрипт:
    - Загрузит Jaeger и OTEL, если необходимо.
    - Запустит локальный экземпляр Jaeger.
    - Запустит коллектор OTEL, настроенный на получение данных от Gemini CLI.
    - Автоматически включит телеметрию в настройках вашей рабочей области.
    - При выходе отключит телеметрию.

1.  **Просмотр трассировок**:
    Откройте веб-браузер и перейдите по адресу **http://localhost:16686**, чтобы получить доступ к пользовательскому интерфейсу Jaeger. Здесь вы можете просмотреть подробные трассировки операций Gemini CLI.

1.  **Проверка журналов и метрик**:
    Скрипт перенаправляет вывод коллектора OTEL (который включает журналы и метрики) в `~/.gemini/tmp/<projectHash>/otel/collector.log`. Скрипт предоставит ссылки для просмотра и команду для отслеживания ваших данных телеметрии (трассировок, метрик, журналов) локально.

1.  **Остановка служб**:
    Нажмите `Ctrl+C` в терминале, где запущен скрипт, чтобы остановить службы OTEL Collector и Jaeger.

### Google Cloud

Используйте команду `npm run telemetry -- --target=gcp` для автоматизации настройки локального коллектора OpenTelemetry, который пересылает данные в ваш проект Google Cloud, включая настройку необходимых параметров в файле `.gemini/settings.json`. Базовый скрипт устанавливает `otelcol-contrib`. Чтобы использовать его:

1.  **Предварительные требования**:
    - Иметь идентификатор проекта Google Cloud.
    - Экспортировать переменную среды `GOOGLE_CLOUD_PROJECT`, чтобы сделать ее доступной для коллектора OTEL.
      ```bash
      export OTLP_GOOGLE_CLOUD_PROJECT="ваш-идентификатор-проекта"
      ```
    - Пройти аутентификацию в Google Cloud (например, запустить `gcloud auth application-default login` или убедиться, что `GOOGLE_APPLICATION_CREDENTIALS` установлен).
    - Убедитесь, что ваша учетная запись Google Cloud/служебная учетная запись имеет необходимые роли IAM: "Cloud Trace Agent", "Monitoring Metric Writer" и "Logs Writer".

1.  **Запустите команду**:
    Выполните команду из корня репозитория:

    ```bash
    npm run telemetry -- --target=gcp
    ```

    Скрипт:
    - Загрузит бинарный файл `otelcol-contrib`, если необходимо.
    - Запустит коллектор OTEL, настроенный на получение данных от Gemini CLI и экспорт их в указанный вами проект Google Cloud.
    - Автоматически включит телеметрию и отключит режим песочницы в настройках вашей рабочей области (`.gemini/settings.json`).
    - Предоставит прямые ссылки для просмотра трассировок, метрик и журналов в вашей консоли Google Cloud.
    - При выходе (Ctrl+C) попытается восстановить ваши исходные настройки телеметрии и песочницы.

1.  **Запустите Gemini CLI:**
    В отдельном терминале запустите команды Gemini CLI. Это генерирует данные телеметрии, которые собирает коллектор.

1.  **Просмотр телеметрии в Google Cloud**:
    Используйте ссылки, предоставленные скриптом, чтобы перейти в консоль Google Cloud и просмотреть свои трассировки, метрики и журналы.

1.  **Проверка журналов локального коллектора**:
    Скрипт перенаправляет вывод локального коллектора OTEL в `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log`. Скрипт предоставляет ссылки для просмотра и команду для отслеживания журналов коллектора локально.

1.  **Остановка службы**:
    Нажмите `Ctrl+C` в терминале, где запущен скрипт, чтобы остановить коллектор OTEL.

## Справочник по журналам и метрикам

В следующем разделе описывается структура журналов и метрик, генерируемых для Gemini CLI.

- `sessionId` включается как общий атрибут для всех журналов и метрик.

### Журналы

Журналы — это записи о конкретных событиях с отметками времени. Для Gemini CLI регистрируются следующие события:

- `gemini_cli.config`: Это событие происходит один раз при запуске с конфигурацией CLI.
  - **Атрибуты**:
    - `model` (строка)
    - `embedding_model` (строка)
    - `sandbox_enabled` (логическое значение)
    - `core_tools_enabled` (строка)
    - `approval_mode` (строка)
    - `api_key_enabled` (логическое значение)
    - `vertex_ai_enabled` (логическое значение)
    - `code_assist_enabled` (логическое значение)
    - `log_prompts_enabled` (логическое значение)
    - `file_filtering_respect_git_ignore` (логическое значение)
    - `debug_mode` (логическое значение)
    - `mcp_servers` (строка)

- `gemini_cli.user_prompt`: Это событие происходит, когда пользователь отправляет запрос.
  - **Атрибуты**:
    - `prompt_length`
    - `prompt` (этот атрибут исключается, если `log_prompts_enabled` настроен как `false`)
    - `auth_type`

- `gemini_cli.tool_call`: Это событие происходит для каждого вызова функции.
  - **Атрибуты**:
    - `function_name`
    - `function_args`
    - `duration_ms`
    - `success` (логическое значение)
    - `decision` (строка: "accept", "reject" или "modify", если применимо)
    - `error` (если применимо)
    - `error_type` (если применимо)

- `gemini_cli.api_request`: Это событие происходит при выполнении запроса к Gemini API.
  - **Атрибуты**:
    - `model`
    - `request_text` (если применимо)

- `gemini_cli.api_error`: Это событие происходит, если запрос API завершается сбоем.
  - **Атрибуты**:
    - `model`
    - `error`
    - `error_type`
    - `status_code`
    - `duration_ms`
    - `auth_type`

- `gemini_cli.api_response`: Это событие происходит при получении ответа от Gemini API.
  - **Атрибуты**:
    - `model`
    - `status_code`
    - `duration_ms`
    - `error` (необязательно)
    - `input_token_count`
    - `output_token_count`
    - `cached_content_token_count`
    - `thoughts_token_count`
    - `tool_token_count`
    - `response_text` (если применимо)
    - `auth_type`

- `gemini_cli.flash_fallback`: Это событие происходит, когда Gemini CLI переключается на flash в качестве запасного варианта.
  - **Атрибуты**:
    - `auth_type`

- `gemini_cli.slash_command`: Это событие происходит, когда пользователь выполняет слэш-команду.
  - **Атрибуты**:
    - `command` (строка)
    - `subcommand` (строка, если применимо)

### Метрики

Метрики — это числовые измерения поведения во времени. Для Gemini CLI собираются следующие метрики:

- `gemini_cli.session.count` (Счетчик, Целое число): Увеличивается на единицу при каждом запуске CLI.

- `gemini_cli.tool.call.count` (Счетчик, Целое число): Подсчитывает вызовы инструментов.
  - **Атрибуты**:
    - `function_name`
    - `success` (логическое значение)
    - `decision` (строка: "accept", "reject" или "modify", если применимо)

- `gemini_cli.tool.call.latency` (Гистограмма, мс): Измеряет задержку вызова инструмента.
  - **Атрибуты**:
    - `function_name`
    - `decision` (строка: "accept", "reject" или "modify", если применимо)

- `gemini_cli.api.request.count` (Счетчик, Целое число): Подсчитывает все запросы API.
  - **Атрибуты**:
    - `model`
    - `status_code`
    - `error_type` (если применимо)

- `gemini_cli.api.request.latency` (Гистограмма, мс): Измеряет задержку запроса API.
  - **Атрибуты**:
    - `model`

- `gemini_cli.token.usage` (Счетчик, Целое число): Подсчитывает количество использованных токенов.
  - **Атрибуты**:
    - `model`
    - `type` (строка: "input", "output", "thought", "cache" или "tool")

- `gemini_cli.file.operation.count` (Счетчик, Целое число): Подсчитывает операции с файлами.
  - **Атрибуты**:
    - `operation` (строка: "create", "read", "update"): Тип операции с файлом.
    - `lines` (Целое число, если применимо): Количество строк в файле.
    - `mimetype` (строка, если применимо): Тип MIME файла.
    - `extension` (строка, если применимо): Расширение файла.

```

## Running an OTEL Collector

An OTEL Collector is a service that receives, processes, and exports telemetry data.
The CLI sends data using the OTLP/gRPC protocol.

Learn more about OTEL exporter standard configuration in [documentation][otel-config-docs].

[otel-config-docs]: https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter/

### Local

Use the `npm run telemetry -- --target=local` command to automate the process of setting up a local telemetry pipeline, including configuring the necessary settings in your `.gemini/settings.json` file. The underlying script installs `otelcol-contrib` (the OpenTelemetry Collector) and `jaeger` (The Jaeger UI for viewing traces). To use it:

1.  **Run the command**:
    Execute the command from the root of the repository:

    ```bash
    npm run telemetry -- --target=local
    ```

    The script will:
    - Download Jaeger and OTEL if needed.
    - Start a local Jaeger instance.
    - Start an OTEL collector configured to receive data from Gemini CLI.
    - Automatically enable telemetry in your workspace settings.
    - On exit, disable telemetry.

1.  **View traces**:
    Open your web browser and navigate to **http://localhost:16686** to access the Jaeger UI. Here you can inspect detailed traces of Gemini CLI operations.

1.  **Inspect logs and metrics**:
    The script redirects the OTEL collector output (which includes logs and metrics) to `~/.gemini/tmp/<projectHash>/otel/collector.log`. The script will provide links to view and a command to tail your telemetry data (traces, metrics, logs) locally.

1.  **Stop the services**:
    Press `Ctrl+C` in the terminal where the script is running to stop the OTEL Collector and Jaeger services.

### Google Cloud

Use the `npm run telemetry -- --target=gcp` command to automate setting up a local OpenTelemetry collector that forwards data to your Google Cloud project, including configuring the necessary settings in your `.gemini/settings.json` file. The underlying script installs `otelcol-contrib`. To use it:

1.  **Prerequisites**:
    - Have a Google Cloud project ID.
    - Export the `GOOGLE_CLOUD_PROJECT` environment variable to make it available to the OTEL collector.
      ```bash
      export OTLP_GOOGLE_CLOUD_PROJECT="your-project-id"
      ```
    - Authenticate with Google Cloud (e.g., run `gcloud auth application-default login` or ensure `GOOGLE_APPLICATION_CREDENTIALS` is set).
    - Ensure your Google Cloud account/service account has the necessary IAM roles: "Cloud Trace Agent", "Monitoring Metric Writer", and "Logs Writer".

1.  **Run the command**:
    Execute the command from the root of the repository:

    ```bash
    npm run telemetry -- --target=gcp
    ```

    The script will:
    - Download the `otelcol-contrib` binary if needed.
    - Start an OTEL collector configured to receive data from Gemini CLI and export it to your specified Google Cloud project.
    - Automatically enable telemetry and disable sandbox mode in your workspace settings (`.gemini/settings.json`).
    - Provide direct links to view traces, metrics, and logs in your Google Cloud Console.
    - On exit (Ctrl+C), it will attempt to restore your original telemetry and sandbox settings.

1.  **Run Gemini CLI:**
    In a separate terminal, run your Gemini CLI commands. This generates telemetry data that the collector captures.

1.  **View telemetry in Google Cloud**:
    Use the links provided by the script to navigate to the Google Cloud Console and view your traces, metrics, and logs.

1.  **Inspect local collector logs**:
    The script redirects the local OTEL collector output to `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log`. The script provides links to view and command to tail your collector logs locally.

1.  **Stop the service**:
    Press `Ctrl+C` in the terminal where the script is running to stop the OTEL Collector.

## Logs and metric reference

The following section describes the structure of logs and metrics generated for Gemini CLI.

- A `sessionId` is included as a common attribute on all logs and metrics.

### Logs

Logs are timestamped records of specific events. The following events are logged for Gemini CLI:

- `gemini_cli.config`: This event occurs once at startup with the CLI's configuration.
  - **Attributes**:
    - `model` (string)
    - `embedding_model` (string)
    - `sandbox_enabled` (boolean)
    - `core_tools_enabled` (string)
    - `approval_mode` (string)
    - `api_key_enabled` (boolean)
    - `vertex_ai_enabled` (boolean)
    - `code_assist_enabled` (boolean)
    - `log_prompts_enabled` (boolean)
    - `file_filtering_respect_git_ignore` (boolean)
    - `debug_mode` (boolean)
    - `mcp_servers` (string)

- `gemini_cli.user_prompt`: This event occurs when a user submits a prompt.
  - **Attributes**:
    - `prompt_length`
    - `prompt` (this attribute is excluded if `log_prompts_enabled` is configured to be `false`)
    - `auth_type`

- `gemini_cli.tool_call`: This event occurs for each function call.
  - **Attributes**:
    - `function_name`
    - `function_args`
    - `duration_ms`
    - `success` (boolean)
    - `decision` (string: "accept", "reject", or "modify", if applicable)
    - `error` (if applicable)
    - `error_type` (if applicable)

- `gemini_cli.api_request`: This event occurs when making a request to Gemini API.
  - **Attributes**:
    - `model`
    - `request_text` (if applicable)

- `gemini_cli.api_error`: This event occurs if the API request fails.
  - **Attributes**:
    - `model`
    - `error`
    - `error_type`
    - `status_code`
    - `duration_ms`
    - `auth_type`

- `gemini_cli.api_response`: This event occurs upon receiving a response from Gemini API.
  - **Attributes**:
    - `model`
    - `status_code`
    - `duration_ms`
    - `error` (optional)
    - `input_token_count`
    - `output_token_count`
    - `cached_content_token_count`
    - `thoughts_token_count`
    - `tool_token_count`
    - `response_text` (if applicable)
    - `auth_type`

- `gemini_cli.flash_fallback`: This event occurs when Gemini CLI switches to flash as fallback.
  - **Attributes**:
    - `auth_type`

- `gemini_cli.slash_command`: This event occurs when a user executes a slash command.
  - **Attributes**:
    - `command` (string)
    - `subcommand` (string, if applicable)

### Metrics

Metrics are numerical measurements of behavior over time. The following metrics are collected for Gemini CLI:

- `gemini_cli.session.count` (Counter, Int): Incremented once per CLI startup.

- `gemini_cli.tool.call.count` (Counter, Int): Counts tool calls.
  - **Attributes**:
    - `function_name`
    - `success` (boolean)
    - `decision` (string: "accept", "reject", or "modify", if applicable)

- `gemini_cli.tool.call.latency` (Histogram, ms): Measures tool call latency.
  - **Attributes**:
    - `function_name`
    - `decision` (string: "accept", "reject", or "modify", if applicable)

- `gemini_cli.api.request.count` (Counter, Int): Counts all API requests.
  - **Attributes**:
    - `model`
    - `status_code`
    - `error_type` (if applicable)

- `gemini_cli.api.request.latency` (Histogram, ms): Measures API request latency.
  - **Attributes**:
    - `model`

- `gemini_cli.token.usage` (Counter, Int): Counts the number of tokens used.
  - **Attributes**:
    - `model`
    - `type` (string: "input", "output", "thought", "cache", or "tool")

- `gemini_cli.file.operation.count` (Counter, Int): Counts file operations.
  - **Attributes**:
    - `operation` (string: "create", "read", "update"): The type of file operation.
    - `lines` (Int, if applicable): Number of lines in the file.
    - `mimetype` (string, if applicable): Mimetype of the file.
    - `extension` (string, if applicable): File extension of the file.
