# Информационные ресурсы

Аннотированный список материалов для дисциплины «Прогнозирование временных рядов: от статистики до MLOps».  
Условия использования — в [CONTRIBUTING.md](../CONTRIBUTING.md) и разделе о LLM в [methodical-guidelines/students/README.md](../methodical-guidelines/students/README.md).

---

## Нормативная база

| Ресурс | Назначение | Ссылка |
|--------|------------|--------|
| КРМ v3.0 (исходник) | Коды competence и индикаторов | [data/krm-v3.0.xlsx](../data/krm-v3.0.xlsx) |
| Рабочая программа | Цели, задачи, объём | [docs/rpd.md](../docs/rpd.md), [docs/rpd.docx](../docs/rpd.docx) |
| Модель измерения | Комpetence × КИМ | [docs/measurement_model.md](../docs/measurement_model.md) |

---

## Данные для КИМ

| Ресурс | Назначение | Ссылка |
|--------|------------|--------|
| ENTSO-E (суточная нагрузка) | Сквозной учебный ряд M1–M4 | [M1/data_sources.md](../M1-analysis-and-preparation/data_sources.md) |
| Шаблон данных COP | Описание формата соревнования | [M3/competition/data/README_data_template.md](../M3-neural-networks/competition/data/README_data_template.md) |

---

## Учебная литература и документация

| Ресурс | Тип | Аннотация |
|--------|-----|----------|
| [Forecasting: Principles and Practice](https://otexts.com/fpp3/) (Hyndman) | Учебник (online) | Классические методы прогнозирования, ARIMA, сезонность |
| [Statsmodels Time Series](https://www.statsmodels.org/stable/tsa.html) | Документация | ARIMA, SARIMA, сезонные модели в Python |
| [Scikit-learn — Time series](https://scikit-learn.org/stable/modules/time_series.html) | Документация | Feature engineering, pipeline |
| [PyTorch tutorials](https://pytorch.org/tutorials/) | Документация | RNN/LSTM для последовательностей |
| [FastAPI documentation](https://fastapi.tiangolo.com/) | Документация | REST API для MVP (M4) |
| [Docker Docs](https://docs.docker.com/) | Документация | Контейнеризация сервиса |
| [MLflow Documentation](https://mlflow.org/docs/latest/index.html) | Документация | Трекинг экспериментов и моделей |

---

## Инструменты (рекомендуемые версии)

| Инструмент | Использование в курсе |
|------------|----------------------|
| Python ≥ 3.10 | Все лабораторные и проект |
| pandas, numpy, matplotlib | EDA, preprocessing |
| statsmodels | ARIMA/SARIMA, ACF/PACF |
| scikit-learn, xgboost / lightgbm | ML-модели |
| PyTorch или TensorFlow | LSTM/GRU |
| Optuna | HPO (ЛР8) |
| FastAPI, uvicorn | REST API |
| Docker, docker compose | Деплой MVP |

Конкретные версии фиксируются в `requirements.txt` командного репозитория.

---

## Материалы репозитория ФОС

| Раздел | Содержание |
|--------|------------|
| [M1–M4](../README.md#3-контрольно-измерительные-материалы) | Лабораторные, квизы, рубрики |
| [Project/](../Project/) | PRC, peer-review, шаблоны |
| [methodical-guidelines/](../methodical-guidelines/) | Указания студентам и преподавателям |

---

## Добавление ресурса

При добавлении файла или ссылки укажите: **назначение**, **автор/источник**, **лицензию**, **связь с модулем/КИМ**. Внешние URL проверяйте перед релизом семестра.
