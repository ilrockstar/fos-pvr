# Чек-лист submission — Forecast Cup

Команда: _______________  
Финальный сабмит: v___  
Дата: _______________

---

## Файлы

- [ ] `teamname_final.csv` — столбцы `date`, `load_mwh`
- [ ] Число строк = числу строк в `sample_submission.csv`
- [ ] Даты совпадают с sample **побайтно**
- [ ] Нет NaN, нет отрицательных / нулевых (>10 %)
- [ ] `teamname_report.pdf` (2–4 стр.)
- [ ] Commit SHA / ссылка на notebook

---

## Модель

- [ ] Использована **нейросеть** (LSTM / GRU / MLP-on-seq / …)
- [ ] Scaler fit **только на train** (не на test dates)
- [ ] Early stopping по validation
- [ ] `random_seed` зафиксирован в ноутбуке

---

## Validation (отчёт)

- [ ] Описан train/val split или walk-forward
- [ ] Указаны local SMAPE, MAE, RMSE
- [ ] Нет подбора lookback **по private test**
- [ ] Сравнение с ≥1 baseline (naive / seasonal naive)

---

## Метрики (тема «Метрики качества»)

- [ ] SMAPE — primary, формула понятна команде
- [ ] MAE интерпретирован в МВт·ч
- [ ] Объяснено, почему не MAPE как primary (если обсуждали)

---

## Команда

| Участник | Вклад |
|----------|-------|
| | |
| | |

---

## Self-score (до проверки преподавателем)

| Метрика | Local val | Ожидание private |
|---------|-----------|------------------|
| SMAPE | | |
| MAE | | |

Подпись team lead: _______________
