# Система итогового оценивания

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Архитектура ФОС:** лабораторные работы как **milestones единого группового проекта** (3–4 человека)  
**Форма промежуточной аттестации:** дифференцированный зачёт

> Актуальная тематика ЛР: [labs-update.pdf](../labs-update.pdf)

---

## 1. Структура итоговой оценки

| Компонент | Вес | КИМ в репозитории | Примечание |
|-----------|-----|-------------------|------------|
| **Лабораторные работы** (10 milestones) | **40 %** | [M1](../M1-analysis-and-preparation/) … [M4](../M4-infrastructure-and-deployment/) | По **4 %** за каждую ЛР; сдаёт **команда** |
| **Практические задания / квизы** (ПЗ1–ПЗ5) | **10 %** | [ПЗ1–ПЗ5](#2-практические-задания-пз1пз5) | По **2 %** каждое |
| **Соревнование (COP)** | **15 %** | [Forecast Cup](../M3-neural-networks/competition.md) | 50 % качество + 50 % отчёт |
| **Командный проект (PRC)** — финальная защита MVP | **29 %** | [Project](../Project/) | См. [rubrics.md](../Project/rubrics.md) |
| **Peer-review** | **6 %** | [peer_review.md](../Project/peer_review.md) | Коэффициент 0,7–1,3 к части PRC |
| **ИТОГО** | **100 %** | | |

> **Промежуточная аттестация — дифференцированный зачёт.** Итоговая оценка складывается из компонентов таблицы выше; отдельный экзамен не проводится. Минимальные пороги — [§6](#6-минимальные-пороги-зачёта).

---

## 2. Лабораторные работы (40 %)

Каждая ЛР — **milestone** сквозного проекта. Отчёт = раздел общего отчёта; код = модуль репозитория `team-<name>/`.

| ЛР | Название | Модуль | Вес | Артефакт |
|----|----------|--------|-----|----------|
| ЛР1 | EDA временного ряда | M1 | 4 % | `notebooks/eda.ipynb`, `docs/report/01_eda.md` |
| ЛР2 | Предобработка и декомпозиция | M1 | 4 % | `src/preprocessing.py`, `data/processed/` |
| ЛР3 | Классические статистические модели | M2 | 4 % | `src/stat_models.py`, `models/sarima/` |
| ЛР4 | Подбор параметров и диагностика | M2 | 4 % | `src/stat_models_param.py`, параметры, диагностика |
| ЛР5 | Регрессионные и факторные модели | M2 | 4 % | `src/r_models.py` |
| ЛР6 | ML-модели для временных рядов | M2 | 4 % | `src/ml_models.py`, сравнение RF/XGBoost |
| ЛР7 | Нейросетевые модели (LSTM/GRU) | M3 | 4 % | `src/neural_models.py`, `models/lstm/` |
| ЛР8 | Валидация, HPO, дрейф | M3 | 4 % | `docs/validation.md`, Optuna, `src/drift_analysis.py` |
| ЛР9 | Контейнеризация и CI/CD | M4 | 4 % | `services/ml_service/`, Dockerfile, docker-compose, MLflow |
| ЛР10 | Мониторинг прогнозной системы | M4 | 4 % | `monitoring/`, Evidently, DAG ретренинга, `docs/monitoring.md` |

### Оценивание одной ЛР

| Уровень | % | Описание |
|---------|---|----------|
| Базовый | 60 | Milestone сдан, минимальные артефакты |
| Средний | 80 | Полный объём, корректные выводы, код в repo |
| Продвинутый | 100 | Глубина, воспроизводимость, готовность к следующему этапу |

$$\text{Балл}_{ЛР_i} = 4\% \times \frac{\text{Уровень \%}}{100}$$

---

## 3. Практические задания (ПЗ1–ПЗ5) — 10 %

| ПЗ | Неделя | Название | Файл |
|----|--------|----------|------|
| ПЗ1 | 1 | Квиз «Остаточные знания по ML» | [quiz_pz1](../M1-analysis-and-preparation/quiz_pz1_ml_basics.md) |
| ПЗ2 | 2 | Квиз «Структура ряда» | [quiz_pz01](../M1-analysis-and-preparation/quiz_pz01_structure.md) |
| ПЗ3 | 4 | Квиз «Стационарность» | [quiz_pz02](../M1-analysis-and-preparation/quiz_pz02_stationarity.md) |
| ПЗ4 | 7 | Квиз «Метрики качества» | [quiz_pz03](../M2-statistical-and-ML-models/quiz_pz03_metrics.md) |
| ПЗ5 | 15 | Кейс «Сломай модель» | [pz05](../M4-infrastructure-and-deployment/pz05_break_the_model.md) |

> Квиз «Дрейф» (бывший ПЗ4) включён в содержание **ЛР8**. Формативный [M2/quiz.md](../M2-statistical-and-ML-models/quiz.md) — к ЛР5–6.

---

## 4. COP (15 %)

50 % leaderboard + 50 % отчёт. См. [competition.md](../M3-neural-networks/competition.md).

---

## 5. PRC (29 %) и peer-review (6 %)

См. [Project/rubrics.md](../Project/rubrics.md), [peer_review.md](../Project/peer_review.md).

---

## 6. Минимальные пороги зачёта

- [ ] Сдано **≥ 7 из 10** milestones ЛР (уровень ≥ «Базовый»)
- [ ] Участие в COP
- [ ] Защита PRC (demo)
- [ ] Peer-review заполнен

---

## 7. Связанные документы

- [measurement_model.md](measurement_model.md)
- [practical_assignments.md](practical_assignments.md)
- [labs-update.pdf](../labs-update.pdf)
