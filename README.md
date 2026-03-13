# LLM Benchmarking: Track A — Question Answering

Сравнительная оценка качества языковых моделей на задаче Question Answering (ответы на вопросы) с использованием датасета SberQuAD.

## Модели

| Модель | Тип | Описание |
|--------|-----|----------|
| Qwen 3.5 9B | Локальная | Квантизация Q4_K_M, llama.cpp сервер через Docker |
| GPT-4o | Облачная | OpenAI API |

## Метрики

- **Exact Match** — точное совпадение с эталоном
- **Token F1 Score** — пересечение токенов (SQuAD-стиль)
- **BLEU** — совпадение n-грамм (sacrebleu)
- **Semantic Similarity** — косинусная близость эмбеддингов (sentence-transformers)
- **Время генерации** — секунды на ответ
- **Длина ответа** — символы и токены
- **Answer Containment** — кастомная метрика: доля эталонных токенов в ответе

## Эксперименты

1. Zero-shot baseline (обе модели)
2. Few-shot с 3 примерами
3. Влияние длины контекста на качество
4. A/B-тестирование форматов промптов (baseline / XML / инструкция)
5. Влияние температуры генерации (0.0, 0.3, 0.7, 1.0)
6. Качественный анализ ошибок + классификация типов

## Требования

- Python 3.10+
- Docker с поддержкой NVIDIA GPU
- API-ключ OpenAI
- ~6 ГБ дискового пространства для модели

## Установка и запуск

```bash
# 1. Клонировать репозиторий
git clone <url>
cd llm_benchmarking

# 2. Создать виртуальное окружение
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # Linux/Mac

# 3. Установить зависимости
pip install -r requirements.txt

# 4. Задать API-ключ OpenAI (создать файл .env)
echo OPENAI_API_KEY=your-key-here > .env

# 5. Запустить локальную модель через Docker
docker-compose up -d

# 6. Дождаться загрузки модели (проверить логи)
docker logs llm_benchmarking-llama-1

# 7. Открыть и запустить ноутбук
jupyter notebook benchmark.ipynb
```

## Структура проекта

```
llm_benchmarking/
├── benchmark.ipynb        # Основной ноутбук с кодом и экспериментами
├── requirements.txt       # Python-зависимости
├── docker-compose.yml     # Конфигурация llama.cpp сервера
├── .env                   # API-ключ OpenAI (не включён в репозиторий)
├── models/                # GGUF модели (не включены в репозиторий)
│   └── Qwen3.5-9B-Q4_K_M.gguf
├── results/               # Результаты экспериментов (генерируются)
│   ├── all_results.csv
│   └── summary_report.csv
├── plots/                 # Графики (генерируются)
│   ├── model_comparison.png
│   ├── temperature_impact.png
│   └── ...
└── README.md
```

## Датасет

**SberQuAD** (kuznetsoffandrey/sberquad) — русскоязычный QA датасет.
- Источник: [HuggingFace](https://huggingface.co/datasets/kuznetsoffandrey/sberquad)
- Используется: 100 случайных примеров из validation split
- Seed: 42 (для воспроизводимости)
