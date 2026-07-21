# Методические рекомендации к выполнению проектной работы

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Архитектура ФОС:** ЛР1–10 = milestones **одного** группового проекта; PRC = финальная защита MVP  
**Формат:** командный кейс (PRC) на данных индустриального партнёра (или согласованном учебном ряде)  
**Команда:** 3–4 обучающихся · **один репозиторий** · общий отчёт

---

## 1. Цель и задачи проекта

### Цель

Сформировать у обучающихся навык **полного цикла разработки прогнозной системы** на реальных данных: от постановки задачи и работы с данными до развёртывания MVP и обоснования решений перед индустриальным партнёром и комиссией.

### Задачи

1. Согласовать с партнёром **предметную постановку**: что прогнозируем, зачем, какой горизонт и допустимая ошибка.
2. Получить или собрать данные, провести **EDA и подготовку** с учётом ограничений партнёра.
3. Построить и сравнить **несколько классов моделей** (статистические, ML, нейросетевые).
4. Организовать **валидацию**, устойчивую к утечкам и переобучению.
5. Развернуть **MVP** — сервис, демонстрирующий прогноз на новых данных.
6. Подготовить **протокол мониторинга** и план развития после MVP.
7. Защитить проект: показать работающий прототип; пройти [peer-review](peer_review.md).

---

## 2. Проект и лабораторные

**Нет отдельных индивидуальных лабораторных.** **Десять** ЛР — **контрольные точки (milestones)** одного сквозного проекта.

| Аспект | Как устроено |
|--------|--------------|
| Репозиторий | Один `team-<name>/` на всю команду |
| ЛР1–10 | Milestones: раздел отчёта + модуль кода (см. [grading.md](../docs/grading.md)) |
| ПЗ1–5 | **Индивидуально** — личный вклад в зачёт (10 %) |
| COP (Forecast Cup) | Соревнование; команда 2–4 чел. (может совпадать с проектной) |
| PRC | Финальная защита **целого** MVP (29 %) |
| Peer-review | Взаимооценка вклада по ролям (6 % + \(k_{peer}\)) |

```
Устав → ЛР1…ЛР10 (milestones, 40%) → COP (15%) → PRC + peer-review (35%)
              ↑
         ПЗ1–5 параллельно (индивидуально, 10%)
```

**Данные:** рекомендуется единый ряд на весь семестр (ENTSO-E для старта или кейс партнёра после kick-off). Смена датасета mid-semester — только с согласования преподавателя.

---

## 3. Формирование команды и роли

### 3.1. Состав

- **3–4 человека** — оптимально для параллельной работы и измеримого вклада каждого.
- Команда формируется в **недели 1–2**; роли фиксируются в [уставе](templates/project_charter.md).
- Один кейс / один временной ряд — **одна команда**.

### 3.2. Четыре роли КРМ

Роли согласованы с [peer-review](peer_review.md) и матрицей компетенций [measurement_model.md](../docs/measurement_model.md).

| Роль | Код | Зона ответственности | Типичные артефакты |
|------|-----|----------------------|-------------------|
| **Team Lead** | Lead | Постановка, сроки, коммуникация с партнёром и преподавателем, сборка презентаций и финального отчёта | `docs/project_charter.md`, `docs/meetings/`, слайды PRC |
| **Data** | Data | Загрузка, качество, EDA, предобработка, feature store, документация источников | `data/`, `notebooks/01_*`, `src/preprocessing.py` |
| **ML** | ML | Модели, эксперименты, метрики, валидация, выбор `best_model`, COP-submission | `src/*_models.py`, `docs/validation.md`, `models/` |
| **MLOps** | MLOps | API, Docker, CI/CD, деплой, мониторинг drift, playbook retraining | `app/`, `Dockerfile`, `.github/workflows/`, `docs/monitoring.md` |

**Правила:**

- Каждый участник имеет **одну основную роль** (можно вторую вспомогательную).
- На защите каждого milestone и PRC участник отвечает **минимум за свою зону** (2–3 мин).
- Вклад фиксируется в Git: **≥ 15 % commits** от участника за семестр (ориентир; проверяется при peer-review).
- Роли указываются в README репозитория и на титульном слайде PRC.

### 3.3. Распределение при 3 и 4 участниках

**Команда из 4 человек** (рекомендуется):

| Участник | Основная роль | Вспомогательная |
|----------|---------------|-----------------|
| A | Team Lead | — |
| B | Data | — |
| C | ML | — |
| D | MLOps | — |

**Команда из 3 человек** (одна роль совмещается):

| Вариант | Участник A | Участник B | Участник C |
|---------|------------|------------|------------|
| **A** (партнёр активен) | Lead | Data + ML | MLOps |
| **B** (сильный ML) | Lead + MLOps (API позже) | Data | ML |
| **C** (сильный DevOps) | Lead | Data + ML (baseline) | MLOps |

Совмещение фиксируется в уставе: кто **R** (Responsible) за артефакт каждого milestone.

### 3.4. Матрица RACI: роли × milestones

**R** — исполнитель · **A** — ответственный за сдачу · **C** — консультант · **I** — в курсе

| Milestone | Lead | Data | ML | MLOps | Артефакты |
|-----------|:----:|:----:|:--:|:-----:|-----------|
| **ЛР1** EDA | A | **R** | C | I | `notebooks/01_eda.ipynb`, `docs/report/01_eda.md` |
| **ЛР2** Preprocessing | A | **R** | C | I | `data/processed/`, `src/preprocessing.py` |
| **ЛР3** Statistical | A | C | **R** | I | `src/stat_models.py`, `models/sarima/` |
| **ЛР4** Stat diagnostics | A | C | **R** | I | `src/stat_models_param.py` |
| **ЛР5** Regression | A | C | **R** | I | `src/r_models.py`, `data/features/` |
| **ЛР6** ML | A | C | **R** | I | `src/ml_models.py` |
| **ЛР7** Neural | A | C | **R** | I | `src/neural_models.py`, `models/lstm/` |
| **ЛР8** Validation / drift | A | C | **R** | **R** | `docs/validation.md`, `src/drift_analysis.py`, `models/best_model/` |
| **ЛР9** Deploy / CI | A | I | C | **R** | `Dockerfile`, `.github/workflows/`, MLflow, Airflow DAG |
| **ЛР10** Monitoring | A | C | **R** | **R** | `src/mon.py`, `docs/monitoring.md` |
| **COP** | A | C | **R** | I | submission + отчёт COP |
| **PRC** | **R** (сборка) | C | C | **R** (demo) | MVP + презентация |

На **ЛР8** Data помогает с симуляцией сдвига; на **ЛР10** MLOps ведёт observability, ML — пороги drift из ЛР8.

### 3.5. Еженедельный ритм по ролям

| Роль | Еженедельный минимум |
|------|---------------------|
| **Lead** | Stand-up (30 мин), обновление board/issues, статус партнёру раз в 2 нед. |
| **Data** | PR по данным / EDA; актуальный `data/README.md` |
| **ML** | PR по моделям; строка в таблице экспериментов |
| **MLOps** | PR по инфраструктуре; `DEPLOY.md` актуален после ЛР9 |

**Code review:** каждый PR ревьюит **не автор**; желательно ревью от роли, которой затронут модуль (Data ревьюит ML-features и т.д.).

### 3.6. Защита milestone: кто говорит

| Milestone | Докладчик (5–7 мин) | Поддержка |
|-----------|---------------------|-----------|
| ЛР1–2 | **Data** | Lead — постановка; ML — что нужно для моделей |
| ЛР3–6 | **ML** | Data — качество данных; Lead — метрики vs партнёр |
| ЛР7 | **ML** | Data — sequence pipeline |
| ЛР8 | **ML** + **MLOps** (по 2–3 мин) | Data — сценарий сдвига |
| ЛР9–10 / PRC | **MLOps** (demo) | Lead — итоги; ML — модель в production |

Преподаватель может задать вопрос **любому** участнику — ответ за пределами своей роли снижает оценку milestone.

---

## 4. Работа с индустриальным партнёром

### Стартовая встреча (kick-off)

**Team Lead** организует встречу; **Data** готовит вопросы по данным; **ML** — по метрикам и горизонту.

1. Какой **процесс** улучшаем прогнозом?
2. Какие **данные** доступны: история, частота, горизонт, признаки?
3. **Ограничения** (NDA, обезличивание)?
4. **Метрика успеха** для бизнеса?
5. **Контактное лицо** партнёра?

Зафиксируйте ответы в [уставе проекта](templates/project_charter.md).

### Регулярная коммуникация

- **Lead** — не реже 1 раза в 2 недели статус партнёру.
- После каждого milestone — **демо** (Data/ML на ранних этапах, MLOps на поздних).
- Протоколы встреч — `docs/meetings/YYYY-MM-DD.md`.

### Этика и данные

- NDA по требованию партнёра; сырые данные не в публичный GitHub.
- **Data** отвечает за `.gitignore` и синтетические сэмплы для CI.
- Не загружать данные партнёра в публичные LLM.

---

## 5. Этапы проекта и milestones ЛР1–10

### Этап 0. Инициация (недели 1–2)

| Действие | Ответственный | Результат |
|----------|---------------|-----------|
| Формирование команды, роли | Lead + преподаватель | Таблица ролей в уставе |
| Kick-off с партнёром | Lead | Постановка, метрика |
| Репозиторий | MLOps (scaffold) + все | README, структура, branch policy |
| Устав | Lead | [project_charter.md](templates/project_charter.md) |

---

### ЛР1 — EDA (недели 2–3) · 4 %

Опирается на [ЛР1](../M1-analysis-and-preparation/lab01_eda.md). **Data — R.**

- [ ] `notebooks/eda.ipynb`, `docs/report/01_eda.md`
- [ ] Графики ряда, сезонности, ACF/PACF
- [ ] Lead: партнёр подтвердил соответствие ряда задаче

---

### ЛР2 — Preprocessing (недели 3–4) · 4 %

[ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md). **Data — R**, ML — C.

- [ ] `data/processed/`, `src/preprocessing.py`, `preprocessing_meta.json`
- [ ] STL, ADF/KPSS; документированы пропуски и выбросы

**ПЗ3** (стационарность) — **индивидуально**, не заменяет milestone.

---

### ЛР3 — Statistical models (недели 5–6) · 4 %

[ЛР3](../M2-statistical-and-ML-models/lab03_classical_statistical_models.md). **ML — R.**

- [ ] Baseline + SARIMA/ES; `src/stat_models.py`
- [ ] ACF/PACF на train; кандидат для ЛР4

---

### ЛР4 — Stat diagnostics (недели 6–7) · 4 %

[ЛР4](../M2-statistical-and-ML-models/lab04_statistical_diagnostics.md). **ML — R.**

- [ ] `src/stat_models_param.py`; подбор (p,d,q)(P,D,Q,s)
- [ ] Диагностика остатков; финальная SARIMA

**ПЗ4** (метрики) — индивидуально.

---

### ЛР5 — Regression (недели 7–8) · 4 %

[ЛР5](../M2-statistical-and-ML-models/lab05_regression_factor_models.md). **ML — R**, Data — C.

- [ ] `src/r_models.py`, `data/features/`
- [ ] Лаги, календарь, exog; сравнение с SARIMA

---

### ЛР6 — ML models (недели 8–9) · 4 %

[ЛР6](../M2-statistical-and-ML-models/lab06_ml_models.md). **ML — R.**

- [ ] `src/ml_models.py`; RF / XGBoost
- [ ] Сравнение с ЛР3–5; лучший табличный baseline

---

### ЛР7 — Neural nets (недели 9–10) · 4 %

[ЛР7](../M3-neural-networks/lab07_neural_networks.md). **ML — R.**

- [ ] LSTM/GRU pipeline; `models/lstm/`

---

### ЛР8 — Validation / drift (недели 10–11) · 4 %

[ЛР8](../M3-neural-networks/lab08_validation_drift.md). **ML — R**, MLOps — C.

- [ ] Walk-forward, Optuna; `docs/validation.md`, `models/best_model/`
- [ ] `src/drift_analysis.py`; правила retraining

**COP (Forecast Cup)** — **ML — R** submission; Lead — отчёт COP.

---

### ЛР9 — Deploy / CI (недели 12–13) · 4 %

[ЛР9](../M4-infrastructure-and-deployment/lab09_containerization_cicd.md). **MLOps — R.**

- [ ] REST API, Docker, CI; MLflow; `services/ml_service/` или `app/`
- [ ] Smoke-test: преподаватель запускает за ≤ 5 мин

---

### ЛР10 — Monitoring (недели 13–14) · 4 %

[ЛР10](../M4-infrastructure-and-deployment/lab10_monitoring.md). **MLOps + ML — R.**

- [ ] `monitoring/`, Evidently drift-report, DAG/Prefect flow, `docs/monitoring.md`
- [ ] Метрики inference, алерты drift (пороги из ЛР8)

**ПЗ5** («сломай модель») — индивидуально.

---

### PRC — финальная защита (недели 15–16) · 29 %

| Артефакт | Ответственный |
|----------|---------------|
| Презентация 12–15 слайдов | Lead (сборка), все — спикеры |
| Live demo MVP | MLOps |
| Итоговый отчёт 15–25 стр. | Lead + все разделы по ролям |
| Peer-review | каждый — анкеты на n−1 коллег | 

Структура слайдов: [defense_outline.md](templates/defense_outline.md).

---

## 6. Структура репозитория проекта

```
project-team-name/
├── README.md                 # Кейс, команда, роли, quick start
├── docs/
│   ├── project_charter.md    # Устав + таблица ролей
│   ├── report/               # 01_eda.md … 10_monitoring.md (milestones)
│   ├── validation.md
│   ├── monitoring.md
│   └── meetings/
├── data/
│   ├── raw/
│   ├── processed/
│   └── README.md             # Data owner
├── notebooks/
├── src/
│   ├── preprocessing.py      # Data
│   ├── stat_models.py        # ML
│   ├── stat_models_param.py
│   ├── r_models.py
│   ├── ml_models.py
│   ├── neural_models.py
│   ├── drift_analysis.py
│   └── mon.py                # MLOps
├── app/                        # MLOps
├── models/best_model/
├── tests/
├── Dockerfile
├── DEPLOY.md
└── .github/workflows/ci.yml
```

---

## 7. Минимальные требования к MVP

MVP считается выполненным, если на защите **MLOps** (с поддержкой ML) может:

1. Запустить сервис за ≤ 5 мин по `DEPLOY.md`.
2. Принять историю ряда (JSON / CSV) и вернуть прогноз на горизонт $h$.
3. Показать версию модели и дату обучения (`/health`).
4. Объяснить процедуру retraining (см. `docs/monitoring.md`).

**Lead** обеспечивает связность demo с постановкой партнёра; **Data** — что входные данные соответствуют обучающему pipeline.

---

## 8. Управление проектом

### Инструменты

- **Git** — feature branches, PR, обязательный review.
- **Issues** — метки: `role:data`, `role:ml`, `role:mlops`, `milestone:LR03` …
- **Stand-up** — еженедельно, 30 мин, фасилитация **Lead**.

### Контрольные точки

| Checkpoint | Вопрос | Кто отвечает |
|------------|--------|--------------|
| После ЛР1 | Достаточно ли данных для горизонта $h$? | Data |
| После ЛР4 | Beat baseline? | ML |
| После ЛР8 | Модель зафиксирована для deploy? | ML + MLOps |
| Перед PRC | Demo без ручных правок? | MLOps |

### Типичные риски

| Риск | Митигация |
|------|-----------|
| Free-rider | RACI + peer-review + git log |
| «Lead делает всё» | Lead только A, не R на всех ЛР |
| Data bottleneck | ML помогает на ЛР2; ранний synthetic fallback |
| MLOps только в конце | MLOps участвует с ЛР8 (контракт API, формат model artifact) |
| Переобучение на test | ML ведёт единый `docs/validation.md` |

---

## 9. Формат сдачи

### Milestones ЛР1–10

По [шаблону](templates/milestone_report.md): 3–5 стр. на milestone + commit hash. Раздел подписывается **ответственным (R)**; Lead проверяет целостность перед сдачей.

### Финальная сдача (PRC)

| Компонент | Формат | Роль |
|-----------|--------|------|
| Презентация | PDF, 12–15 слайдов | Lead + все |
| Итоговый отчёт | PDF, 15–25 стр. | Lead (сборка) |
| Репозиторий | Доступ преподавателю | все |
| Demo | Live ≤ 7 мин | MLOps |
| Peer-review | LMS, 48–72 ч после защиты | все |

### Защита

- **15 мин** — презентация (каждый ≥ 2 мин, по зоне роли).
- **5–10 мин** — вопросы комиссии / партнёра.
- **5 мин** — live demo (**MLOps**).

---

## 10. Использование LLM и внешних ресурсов

- LLM — для изучения API, черновиков, отладки.
- Код и текст на защите должны быть **понятны исполнителю роли**.
- README: раздел «Использованные инструменты».
- Данные партнёра не в публичные LLM.

---

## 11. Критерии оценки

Подробно: [rubrics.md](rubrics.md), веса: [grading.md](../docs/grading.md).

| Уровень | Суть |
|---------|------|
| **Базовый** | Все milestones сданы; роли соблюдены минимально; MVP работает |
| **Средний** | Сравнение моделей, Docker + API, осмысленный вклад каждой роли |
| **Продвинутый** | CI/CD, мониторинг drift, согласованный roadmap с партнёром |

Индивидуальный вклад учитывается через [peer-review](peer_review.md) (\(k_{peer}\) 0,7–1,3).

---

## 12. Полезные ссылки

- Milestones: [M1](../M1-analysis-and-preparation/) · [M2](../M2-statistical-and-ML-models/) · [M3](../M3-neural-networks/) · [M4](../M4-infrastructure-and-deployment/)
- [Руководство преподавателя](instructor_guide.md)
- [Peer-review](peer_review.md)
- [Шаблон устава](templates/project_charter.md)
