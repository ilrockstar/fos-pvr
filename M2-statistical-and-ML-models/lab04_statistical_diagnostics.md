# Лабораторная работа 4. Подбор параметров и диагностика моделей

**Модуль 2.** Статистические и ML-модели прогнозирования  
**Ролевой фокус:** ML Engineer, ML Researcher  
**Компетенции:** ML-2.2, ML-2.3, ML-3.2 (С)  
**Время:** 2 ч лекция + 2 ч лабораторная (+ 5 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** `src/stat_models_param.py`, `models/sarima/params.json`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

Выполнить **системный подбор параметров** SARIMA (AIC/BIC, сетка) и **диагностику остатков**; зафиксировать финальную статистическую модель для сравнения с регрессией и ML (ЛР5–6).

---

## 2. Входные требования и пререквизиты

- [ЛР3](lab03_classical_statistical_models.md): кандидат SARIMA, train/test split.
- Понимание теста Льюнга–Бокса и интерпретации остатков.

**Стек:** statsmodels (SARIMAX, acorr_ljungbox), itertools.

---

## 3. Задание

1. Сетка $(p,d,q)(P,D,Q,s)$ — 3–10 конфигураций ($p,q,P,Q \leq 2$).
2. Таблица AIC/BIC; выбор финальной модели.
3. Диагностика остатков: графики, Ljung-Box.
4. Сравнение с ES из ЛР3 на том же test.

**Ожидаемые артефакты:** `src/stat_models_param.py`, `models/sarima/params.json`, `docs/report/04_stat_diagnostics.md`.

---

## 4. Теоретическая справка

**Диагностика остатков** проверяет, что модель «выжала» всю предсказуемую структуру:

- Остатки ≈ белый шум (нет автокорреляции).
- **Ljung-Box:** H₀ — нет автокорреляции; $p > 0.05$ — модель адекватна.
- **AIC vs BIC:** BIC сильнее штрафует сложность.

**Компромисс:** минимальный AIC vs интерпретируемость vs скорость inference.

---

## 5. Ход работы

### Этап 1. Подбор параметров

1. Grid search по $(p,d,q)(P,D,Q,s)$.
2. Таблица AIC/BIC; выбор с учётом простоты.
3. Сохранение: `models/sarima/params.json`.

### Этап 2. Диагностика остатков

1. График остатков, гистограмма, ACF остатков.
2. Ljung-Box на лагах 10–20.
3. Вывод: адекватна / требуется доработка.

### Этап 3. Интерпретация

1. Обоснование порядка (ЛР1–2, ACF/PACF).
2. Сравнение финальной SARIMA vs ES (MAE, RMSE, SMAPE).

### Фрагменты кода

```python
import itertools
from statsmodels.tsa.statespace.sarimax import SARIMAX
from statsmodels.stats.diagnostic import acorr_ljungbox

p = q = range(0, 3)
best_aic, best_order, best_seasonal = float("inf"), None, None
for order in itertools.product(p, [1], q):
    for seasonal in itertools.product([0, 1], [0], [0, 1], [7]):
        try:
            fit = SARIMAX(train, order=order, seasonal_order=seasonal).fit(disp=False)
            if fit.aic < best_aic:
                best_aic, best_order, best_seasonal = fit.aic, order, seasonal
        except Exception:
            continue

fit = SARIMAX(train, order=best_order, seasonal_order=best_seasonal).fit(disp=False)
lb = acorr_ljungbox(fit.resid, lags=[10], return_df=True)
print(lb)
```

---

## 6. Требования к отчёту

1. Таблица grid search (top-5 по AIC).
2. Графики диагностики остатков.
3. Результат Ljung-Box и интерпретация.
4. Финальные параметры и метрики на test.
5. Сравнение с baseline из ЛР3.

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| Grid search, AIC/BIC | 0,8 |
| Диагностика остатков (графики) | 0,6 |
| Ljung-Box и интерпретация | 0,6 |
| `stat_models_param.py`, params.json | 0,4 |
| Сравнение с ES / naive | 0,4 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Сетка параметров; AIC/BIC; график остатков |
| **Средний** | Ljung-Box + интерпретация; `stat_models_param.py` |
| **Продвинутый** | Компромисс AIC vs простота; параметры для pipeline ЛР5–6 |

---

## 8. Контрольные вопросы

### Блок 1. Подбор и диагностика

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Что такое AIC и BIC? Чем они отличаются? |
| 2 | Зачем нужна диагностика остатков? |
| 3 | Что проверяет тест Льюнга–Бокса? |
| 4 | Какие параметры $(p,d,q)(P,D,Q,s)$ вы выбрали? |
| 5 | Как финальная SARIMA сравнилась с ES? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Почему grid search ограничивают малыми $p,q$? |
| 7 | Как интерпретировать ACF остатков? |
| 8 | Что делать, если Ljung-Box отвергает H₀? |
| 9 | Как overfitting проявляется в SARIMA? |
| 10 | Зачем сохранять `params.json` для команды? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как auto_arima (pmdarima) соотносится с ручным grid? |
| 12 | Когда seasonal order $(P,D,Q,s)$ нестабилен? |
| 13 | Как rolling-origin validation применить к SARIMA? |
| 14 | Какие остаточные паттерны указывают на пропущенную сезонность? |
| 15 | Как результаты ЛР4 используются как baseline в ЛР5–7? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР3](lab03_classical_statistical_models.md) | Вход: кандидат SARIMA |
| [ЛР5](lab05_regression_factor_models.md) | Сравнение stat vs regression |
| [ПЗ4](../M2-statistical-and-ML-models/quiz_pz03_metrics.md) | Метрики качества |
| [Project](../Project/project_guidelines.md) | ML — R за milestone |
