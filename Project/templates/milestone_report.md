# Шаблон отчёта по milestone (ЛР1–8)

> **Milestone:** ЛР__ — *(EDA / Preprocessing / Statistical / ML / Neural / Validation / Drift / Deploy)*  
> **Команда:**  
> **Ответственный (R):** *(Data / ML / MLOps)*  
> **Дата сдачи:**  
> **Commit:** `git rev-parse HEAD`

Сохраните как `docs/report/0X_<название>.md` в репозитории команды.  
Общий шаблон: [project_guidelines.md](../project_guidelines.md) · [grading.md](../../docs/grading.md)

---

## 1. Статус milestone

| Поле | Значение |
|------|----------|
| ЛР / milestone | ЛР__ |
| Срок (план) | |
| Срок (факт) | |
| Статус | 🟢 / 🟡 / 🔴 |
| Докладчик на защите | |
| Блокеры | |

---

## 2. Выполнено (кратко)

1. 
2. 
3. 

---

## 3. Содержание по типу milestone

*(Оставьте один блок, соответствующий текущей ЛР; остальные удалите.)*

### ЛР1 — EDA · ответственный: **Data**

- Источник данных: 
- $N$ = , период: , частота: 
- Тренд / сезонность / аномалии: 
- ACF/PACF — выводы: 
- Артефакты: `notebooks/01_eda.ipynb`, `docs/report/01_eda.md`
- Ссылка на ключевые графики: 

### ЛР2 — Preprocessing · ответственный: **Data**

- Пропуски: метод, доля затронутых точек
- Выбросы: правило, сколько удалено/скорректировано
- STL / декомпозиция: период $s$ = 
- Стационарность: ADF/KPSS, $d$ = 
- Артефакты: `data/processed/`, `src/preprocessing.py`, `preprocessing_meta.json`

### ЛР3 — Statistical models · ответственный: **ML**

| Модель | MAE | RMSE | SMAPE | Примечание |
|--------|-----|------|-------|------------|
| Baseline (naive / mean) | | | | |
| ES / SARIMA | | | | |
| **Выбранная** | | | | |

- Диагностика остатков: 
- Артефакты: `src/stat_models.py`, `models/sarima/`

### ЛР4 — ML models · ответственный: **ML**

| Модель | MAE | RMSE | SMAPE | Примечание |
|--------|-----|------|-------|------------|
| RF / XGBoost / … | | | | |
| vs ЛР3 | | | | |

- Признаки: лаги, календарь, exog
- Артефакты: `src/ml_models.py`, `data/features/`

### ЛР5 — Neural nets · ответственный: **ML**

- Архитектура: LSTM / GRU, окно, units
- Метрики vs ЛР3–4: 
- Артефакты: `src/neural_models.py`, `models/lstm/`

### ЛР6 — Validation / HPO · ответственный: **ML**

- Протокол: walk-forward, split, горизонт
- HPO (Optuna / grid): лучшие гиперпараметры
- **Финальная модель** и обоснование выбора
- Артефакты: `docs/validation.md`, `models/best_model/`

### ЛР7 — Drift · ответственные: **ML + MLOps**

- Сценарий деградации (data / concept drift): 
- Метрики до/после: 
- PSI / скользящий MAE: порог алерта
- План retraining: окно, триггер
- Артефакты: `src/drift_analysis.py`, `docs/monitoring.md`

### ЛР8 — Deploy / CI · ответственный: **MLOps**

- [ ] `GET /health`, `POST /predict` (или `/forecast`)
- [ ] Docker build OK
- [ ] CI: тесты + сборка образа
- [ ] `DEPLOY.md` — команда запуска ≤ 5 мин

```bash
docker compose up --build
curl http://localhost:8000/health
curl -X POST http://localhost:8000/predict -H "Content-Type: application/json" -d '{ ... }'
```

---

## 4. Вклад участников (для peer-review)

| Участник | Роль | Commits / PR (кратко) | Основной вклад в этот milestone |
|----------|------|----------------------|----------------------------------|
| | Lead | | |
| | Data | | |
| | ML | | |
| | MLOps | | |

---

## 5. Обратная связь партнёра / преподавателя

- Дата: 
- Комментарии: 

---

## 6. План на следующий milestone

| Задача | R (роль) | Срок |
|--------|----------|------|
| | | |
| | | |

---

## 7. Риски

| Риск | Вероятность | Митигация | Владелец |
|------|-------------|-----------|----------|
| | | | |

---

## 8. Приложения

- Репозиторий: 
- Ссылка на PR / notebook: 
