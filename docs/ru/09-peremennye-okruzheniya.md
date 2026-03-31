# Справочник переменных окружения

Все переменные окружения, влияющие на расход токенов в Claude Code.

## Окно контекста и компактификация

| Переменная | Тип | Описание |
|------------|-----|----------|
| `CLAUDE_CODE_MAX_CONTEXT_TOKENS` | число | Жёсткий override размера окна |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW` | число | Ограничить эффективное окно |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | boolean | Отключить 1M контекст (HIPAA) |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | число (0-100) | Авто-компакт при N% контекста |
| `DISABLE_AUTO_COMPACT` | boolean | Отключить авто-компакт |
| `DISABLE_COMPACT` | boolean | Отключить ВСЮ компактификацию |
| `CLAUDE_CODE_BLOCKING_LIMIT_OVERRIDE` | число | Override блокирующего лимита |

## Кэширование промптов

| Переменная | Тип | Описание |
|------------|-----|----------|
| `DISABLE_PROMPT_CACHING` | boolean | Отключить кэширование глобально |
| `DISABLE_PROMPT_CACHING_HAIKU` | boolean | Отключить кэш только для Haiku |
| `DISABLE_PROMPT_CACHING_SONNET` | boolean | Отключить кэш только для Sonnet |

## Мышление и рассуждения

| Переменная | Тип | Описание |
|------------|-----|----------|
| `CLAUDE_CODE_DISABLE_THINKING` | boolean | Отключить расширенное мышление |
| `DISABLE_INTERLEAVED_THINKING` | boolean | Отключить перемежающееся мышление |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | boolean | Отключить адаптивное мышление |

## Память сессии

| Переменная | Тип | Описание |
|------------|-----|----------|
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | boolean | Отключить авто-извлечение памяти |
| `ENABLE_CLAUDE_CODE_SM_COMPACT` | boolean | Включить session memory compaction |
| `DISABLE_CLAUDE_CODE_SM_COMPACT` | boolean | Отключить session memory compaction |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | boolean | Отключить фоновые задачи |

## Стриминг и таймауты

| Переменная | Тип | Описание |
|------------|-----|----------|
| `CLAUDE_ENABLE_STREAM_WATCHDOG` | boolean | Включить watchdog стриминга |
| `CLAUDE_STREAM_IDLE_TIMEOUT_MS` | число | Таймаут простоя (по умолч. 90000мс) |

## API

| Переменная | Тип | Описание |
|------------|-----|----------|
| `CLAUDE_CODE_EXTRA_BODY` | JSON строка | Дополнительные параметры для API |

## Примеры использования

### Агрессивная компактификация
```bash
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=30
```

### Экономия на мышлении для простых задач
```bash
# Токены мышления — это output-токены ($15-150/Mtok!)
export CLAUDE_CODE_DISABLE_THINKING=true
```

### Ограничить контекст для тестирования
```bash
export CLAUDE_CODE_AUTO_COMPACT_WINDOW=50000
```

### Отключить фоновое потребление
```bash
export CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=true
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=true
```

## Далее

- [Руководство по CLAUDE.md](10-rukovodstvo-claudemd.md) — экономия через файл инструкций
- [Мониторинг затрат](11-monitoring-zatrat.md) — отслеживание расходов
