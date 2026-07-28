# Методические рекомендации по ключевым вехам сквозного проекта

Полные регламенты выполнения, распределение ролей, LLM-канон и рубрикаторы приёмки (DoD) — в PDF-документах ниже.

| Веха | Документ | Milestones ЛР | Содержание (кратко) |
|------|----------|---------------|---------------------|
| **1** | [p_v1.pdf](p_v1.pdf) | ЛР1–2 | EDA, предобработка, STL, ADF/KPSS, `preprocessing.py` |
| **2** | [p_v2.pdf](p_v2.pdf) | ЛР3–6 | ARIMA/SARIMA, feature engineering, CatBoost/LightGBM, Optuna |
| **3** | [p_v3.pdf](p_v3.pdf) | ЛР7–8, COP | LSTM/GRU, многошаговый прогноз, стресс-тест drift |
| **4** | [p_v4.pdf](p_v4.pdf) | ЛР9–10, PRC | FastAPI, Docker, MLflow, мониторинг и автоматический retrain |

> Вехи проходят **последовательно**: выход предыдущей (очищенный датасет, baseline, best_model, сервис) — вход следующей.

См. также: [project_guidelines.md](../project_guidelines.md) · [Project/README.md](../README.md) · [instructor_guide.md](../instructor_guide.md).
