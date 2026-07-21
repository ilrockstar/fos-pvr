# Шаблон отчёта по milestone (ЛР1–10)

> **Milestone:** ЛР__ — *(EDA / Preprocessing / Statistical / Stat diagnostics / Regression / ML / Neural / Validation / Deploy / Monitoring)*  
> **Команда:**  
> **Ответственный (R):** *(Data Analyst / ML Engineer / ML Researcher / MLOps)*  
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

### ЛР1 — EDA · ответственный: **Data Analyst**

- Источник данных: 
- $N$ = , период: , частота: 
- Тренд / сезонность / аномалии: 
- ACF/PACF — выводы: 
- Артефакты: `notebooks/01_eda.ipynb`, `docs/report/01_eda.md`
- Ссылка на ключевые графики: 

### ЛР2 — Preprocessing · ответственный: **Data Analyst**

- Пропуски: метод, доля затронутых точек
- Выбросы: правило, сколько удалено/скорректировано
- STL / декомпозиция: период $s$ = 
- Стационарность: ADF/KPSS, $d$ = 
- Артефакты: `data/processed/`, `src/preprocessing.py`, `preprocessing_meta.json`

### ЛР3 — Statistical models · ответственный: **ML Engineer**

| Модель | MAE | RMSE | SMAPE | Примечание |
|--------|-----|------|-------|------------|
| Baseline (naive / mean) | | | | |
| ES / SARIMA | | | | |
| **Выбранная** | | | | |

- Диагностика остатков: *(→ ЛР4)*
- Артефакты: `src/stat_models.py`, `models/sarima/`

### ЛР4 — Stat diagnostics · ответственный: **ML Researcher**

| Модель | MAE | RMSE | SMAPE | Примечание |
|--------|-----|------|-------|------------|
| Кандидаты SARIMA | | | | |
| **Финальная SARIMA** | | | | |

- Подбор (p,d,q)(P,D,Q,s): AIC/BIC
- Артефакты: `src/stat_models_param.py`

### ЛР5 — Regression · ответственный: **ML Engineer**

| Модель | MAE | RMSE | SMAPE | Примечание |
|--------|-----|------|-------|------------|
| Linear / Ridge | | | | |
| vs SARIMA (ЛР4) | | | | |

- Признаки: лаги, календарь, exog
- Артефакты: `src/r_models.py`, `data/features/`

### ЛР6 — ML models · ответственный: **ML Engineer**

| Модель | MAE | RMSE | SMAPE | Примечание |
|--------|-----|------|-------|------------|
| RF / XGBoost | | | | |
| vs ЛР3–5 | | | | |

- Артефакты: `src/ml_models.py`

### ЛР7 — Neural nets · ответственный: **ML Engineer**

- Архитектура: LSTM / GRU, окно, units
- Метрики vs ЛР3–6: 
- Артефакты: `src/neural_models.py`, `models/lstm/`

### ЛР8 — Validation / drift · ответственные: **ML Researcher + MLOps**

- Протокол: walk-forward, split, горизонт
- HPO (Optuna): лучшие гиперпараметры
- **Финальная модель** и обоснование выбора
- Drift: сценарий, PSI, plan retraining
- Артефакты: `docs/validation.md`, `models/best_model/`, `src/drift_analysis.py`

### ЛР9 — Deploy / CI · ответственный: **MLOps**

- [ ] `GET /health`, `POST /predict` (или `/forecast`)
- [ ] `docker-compose.yml`: FastAPI + MLflow
- [ ] CI: тесты + сборка образа
- [ ] MLflow run; черновик Airflow DAG
- [ ] `DEPLOY.md` — команда запуска ≤ 5 мин

### ЛР10 — Monitoring · ответственные: **MLOps + ML Researcher**

- [ ] Метрики inference (latency, throughput)
- [ ] Drift alerts по правилам ЛР8
- [ ] Артефакты: `monitoring/`, `docs/monitoring.md`, скрипт симуляции drift

```bash
docker compose up --build
curl http://localhost:8000/health
curl -X POST http://localhost:8000/predict -H "Content-Type: application/json" -d '{ ... }'
```

---

## 4. Вклад участников (для peer-review)

| Участник | Роль | Commits / PR (кратко) | Основной вклад в этот milestone |
|----------|------|----------------------|----------------------------------|
| | Data Analyst | | |
| | ML Engineer | | |
| | ML Researcher | | |
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
