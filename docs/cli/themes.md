# Темы

Gemini CLI поддерживает различные темы для настройки цветовой схемы и внешнего вида. Вы можете изменить тему в соответствии со своими предпочтениями с помощью команды `/theme` или параметра конфигурации `"theme":`.

## Доступные темы

Gemini CLI поставляется с набором предопределенных тем, которые вы можете перечислить с помощью команды `/theme` в Gemini CLI:

- **Темные темы:**
  - `ANSI`
  - `Atom One`
  - `Ayu`
  - `Default`
  - `Dracula`
  - `GitHub`
- **Светлые темы:**
  - `ANSI Light`
  - `Ayu Light`
  - `Default Light`
  - `GitHub Light`
  - `Google Code`
  - `Xcode`

### Изменение тем

1.  Введите `/theme` в Gemini CLI.
2.  Появится диалоговое окно или запрос выбора, перечисляющий доступные темы.
3.  Используя клавиши со стрелками, выберите тему. Некоторые интерфейсы могут предлагать предварительный просмотр в реальном времени или выделение при выборе.
4.  Подтвердите свой выбор, чтобы применить тему.

### Сохранение темы

Выбранные темы сохраняются в [конфигурации](./configuration.md) Gemini CLI, поэтому ваши предпочтения запоминаются между сеансами.

---

## Пользовательские цветовые темы

Gemini CLI позволяет создавать собственные цветовые темы, указывая их в файле `settings.json`. Это дает вам полный контроль над цветовой палитрой, используемой в CLI.

### Как определить пользовательскую тему

Добавьте блок `customThemes` в файл `settings.json` пользователя, проекта или системы. Каждая пользовательская тема определяется как объект с уникальным именем и набором цветовых ключей. Например:

```json
{
  "customThemes": {
    "MyCustomTheme": {
      "name": "MyCustomTheme",
      "type": "custom",
      "Background": "#181818",
      "Foreground": "#F8F8F2",
      "LightBlue": "#82AAFF",
      "AccentBlue": "#61AFEF",
      "AccentPurple": "#C678DD",
      "AccentCyan": "#56B6C2",
      "AccentGreen": "#98C379",
      "AccentYellow": "#E5C07B",
      "AccentRed": "#E06C75",
      "Comment": "#5C6370",
      "Gray": "#ABB2BF"
    }
  }
}
```

**Цветовые ключи:**

- `Background`
- `Foreground`
- `LightBlue`
- `AccentBlue`
- `AccentPurple`
- `AccentCyan`
- `AccentGreen`
- `AccentYellow`
- `AccentRed`
- `Comment`
- `Gray`

**Обязательные свойства:**

- `name` (должно соответствовать ключу в объекте `customThemes` и быть строкой)
- `type` (должно быть строкой `"custom"`)
- `Background`
- `Foreground`
- `LightBlue`
- `AccentBlue`
- `AccentPurple`
- `AccentCyan`
- `AccentGreen`
- `AccentYellow`
- `AccentRed`
- `Comment`
- `Gray`

Вы можете использовать либо шестнадцатеричные коды (например, `#FF0000`), **либо** стандартные названия цветов CSS (например, `coral`, `teal`, `blue`) для любого значения цвета. Полный список поддерживаемых названий см. в разделе [Названия цветов CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#color_keywords).

Вы можете определить несколько пользовательских тем, добавив больше записей в объект `customThemes`.

### Пример пользовательской темы

<img src="../assets/theme-custom.png" alt="Пример пользовательской темы" width="600" />

### Использование вашей пользовательской темы

- Выберите свою пользовательскую тему с помощью команды `/theme` в Gemini CLI. Ваша пользовательская тема появится в диалоговом окне выбора темы.
- Или установите ее по умолчанию, добавив `"theme": "MyCustomTheme"` в ваш `settings.json`.
- Пользовательские темы могут быть установлены на уровне пользователя, проекта или системы и следуют тому же [приоритету конфигурации](./configuration.md), что и другие настройки.

---

## Темные темы

### ANSI

<img src="../assets/theme-ansi.png" alt="Тема ANSI" width="600" />

### Atom OneDark

<img src="../assets/theme-atom-one.png" alt="Тема Atom One" width="600">

### Ayu

<img src="../assets/theme-ayu.png" alt="Тема Ayu" width="600">

### Default

<img src="../assets/theme-default.png" alt="Тема Default" width="600">

### Dracula

<img src="../assets/theme-dracula.png" alt="Тема Dracula" width="600">

### GitHub

<img src="../assets/theme-github.png" alt="Тема GitHub" width="600">

## Светлые темы

### ANSI Light

<img src="../assets/theme-ansi-light.png" alt="Тема ANSI Light" width="600">

### Ayu Light

<img src="../assets/theme-ayu-light.png" alt="Тема Ayu Light" width="600">

### Default Light

<img src="../assets/theme-default-light.png" alt="Тема Default Light" width="600">

### GitHub Light

<img src="../assets/theme-github-light.png" alt="Тема GitHub Light" width="600">

### Google Code

<img src="../assets/theme-google-light.png" alt="Тема Google Code" width="600">

### Xcode

<img src="../assets/theme-xcode-light.png" alt="Тема Xcode Light" width="600">
