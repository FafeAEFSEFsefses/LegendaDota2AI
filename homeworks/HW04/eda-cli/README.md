# S03 – eda_cli: мини-EDA для CSV

Небольшое CLI-приложение для базового анализа CSV-файлов.
Используется в рамках Семинара 03 курса «Инженерия ИИ».

## Требования

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) установлен в систему

## Инициализация проекта

В корне проекта (S03):

```bash
uv sync
```

Эта команда:

- создаст виртуальное окружение `.venv`;
- установит зависимости из `pyproject.toml`;
- установит сам проект `eda-cli` в окружение.

## Запуск CLI

### Краткий обзор

```bash
uv run eda-cli overview data/example.csv
```

Параметры:

- `--sep` – разделитель (по умолчанию `,`);
- `--encoding` – кодировка (по умолчанию `utf-8`).

### Полный EDA-отчёт

```bash
uv run eda-cli report data/example.csv --out-dir reports
```

Дополнительные параметры отчёта:

- `--title` — задаёт заголовок отчёта (`report.md`);
- `--top-k-categories` — сколько top-значений сохранять для категориальных колонок;

Пример запуска с параметрами:

```bash
uv run eda-cli report data/example.csv --out-dir reports_cli --title "HW03 report" --top-k-categories 3
```

В результате в каталоге `reports/` появятся:

- `report.md` – основной отчёт в Markdown;
- `summary.csv` – таблица по колонкам;
- `missing.csv` – пропуски по колонкам;
- `correlation.csv` – корреляционная матрица (если есть числовые признаки);
- `top_categories/*.csv` – top-k категорий по строковым признакам;
- `hist_*.png` – гистограммы числовых колонок;
- `missing_matrix.png` – визуализация пропусков;
- `correlation_heatmap.png` – тепловая карта корреляций.

## Тесты

```bash
uv run pytest -q
```

## HTTP Dataset Quality API (HW04)

В рамках HW04 проект `eda-cli` дополнен HTTP-сервисом на FastAPI.

### Запуск сервиса

```bash
uv sync
uv run uvicorn eda_cli.api:app --reload --port 8000

## HTTP API

Сервис предоставляет HTTP-интерфейс для оценки качества датасетов.

### POST /quality-flags-from-csv

Дополнительный эндпоинт, реализованный в рамках HW04.

Принимает CSV-файл и возвращает полный набор флагов качества данных,
используя эвристики, реализованные в HW03.

Описание:
- вход: CSV-файл (multipart/form-data)
- обработка: summarize_dataset, missing_table, compute_quality_flags
- выход: JSON со всеми булевыми флагами качества

Пример ответа:
`json
{
  "flags": {
    "too_few_rows": true,
    "too_many_missing": false,
    "has_constant_columns": false
  }
}
