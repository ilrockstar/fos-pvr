# Отчёт о проверке по [quality-checklist.md](quality-checklist.md)

**Дата проверки:** 2026-07-20  
**Архитектура ФОС:** групповой сквозной проект, ЛР = milestones  
**Статус:** готов к пилотному семестру с оговорками (см. § «Остаточные действия»)

---

## Содержание

| Критерий | Статус | Комментарий |
|----------|:------:|-------------|
| Название дисциплины и аннотация | ✅ | [README.md](../README.md) §1 |
| РПД: цель, задачи, пререквизиты, результаты | ✅ | [rpd.md](rpd.md), [rpd.docx](rpd.docx) |
| Целевые роли КРМ | ✅ | [measurement_model.md](measurement_model.md) §1 |
| Коды competence сверены с КРМ 3.0 | ⚠️ | Матрица заполнена; финальная сверка с [krm-v3.0.xlsx](../data/krm-v3.0.xlsx) — на кафедре |
| Модель измерения для всех модулей и форм аттестации | ✅ | [measurement_model.md](measurement_model.md), [grading.md](grading.md) |
| Каждый результат обучения → ≥1 КИМ | ✅ | README §1 + матрица measurement_model |
| КИМ: условия, формат, критерии, шкала | ✅ | ЛР1–10, ПЗ1–5, COP, PRC — в файлах модулей и Project/ |
| Баллы рубрик согласованы с итоговой таблицей | ✅ | 40+10+15+35=100; PRC rubrics §1 = 100 %; ЛР × 4 % |
| Правила LLM и внешних ресурсов | ✅ | [students/README.md](../methodical-guidelines/students/README.md), [project_guidelines.md](../Project/project_guidelines.md) §10 |
| Методические указания | ✅ | [methodical-guidelines/](../methodical-guidelines/) — заполнены |
| Ресурсы с аннотациями и ссылками | ✅ | [resources/README.md](../resources/README.md) |
| Авторы и вклад | ✅ | [team/README.md](../team/README.md) — 4 автора (КубГУ, ПГТУ, РУДН, ФУ) |
| Лицензия | ✅ | [LICENSE.md](../LICENSE.md) — CC BY 4.0 |

---

## Техническая проверка

| Критерий | Статус | Комментарий |
|----------|:------:|-------------|
| Нет `[ЗАПОЛНИТЬ]` в публикуемых md | ✅ | Устранено в rpd.md |
| Относительные Markdown-ссылки | ✅ | 275 проверено; 0 битых (после добавления resources/README, правки LICENSE) |
| Временные файлы Office | ✅ | `~$*`, `*.tmp` не найдены |
| .DS_Store | ⚠️ | Добавлен [.gitignore](../.gitignore); локальные `.DS_Store` удалить из git при коммите |
| Крупные файлы | ✅ | Самый крупный: `data/krm-v3.0.xlsx` (~413 KB, < лимита GitHub 100 MB) |

---

## Согласованность ФОС

| Элемент | Статус |
|---------|:------:|
| ЛР = milestones команды | ✅ |
| project_guidelines + charter + milestone_report | ✅ |
| peer-review + grading | ✅ |
| Промежуточная аттестация — дифференцированный зачёт (без экзамена) | ✅ |
| instructor_guide календарь ЛР1–10 | ✅ |

---

## Выборочная проверка КИМ

| КИМ | Условия | Формат сдачи | Критерии Б/С/П | Шкала в grading |
|-----|---------|--------------|----------------|-----------------|
| ЛР1 | lab01_eda.md | notebook + report | § Критерии | 4 % |
| ПЗ1 | quiz_pz1_ml_basics.md | 10 мин LMS | quiz_pz1_key.md | 2 % |
| ПЗ2 | quiz_pz01_structure.md | 10 мин LMS | quiz_pz01_key.md | 2 % |
| COP | competition.md | submission + отчёт | scoring.md | 15 % |
| PRC | rubrics.md | demo + PDF | §1 A–F | 35 % |

---

## Остаточные действия перед официальной публикацией

1. ~~**team/README.md** — добавить соавторов~~ — выполнено.
2. **Сверка competence** с актуальной редакцией КРМ на кафедре.
3. **Удалить `.DS_Store`** из индекса git (`git rm --cached`).
4. **Согласовать LICENSE** с правообладателем (вуз).
5. **COP vs presentation** — при необходимости унифицировать метрику (SMAPE/RMSE) и формат команд в [competition.md](../M3-neural-networks/competition.md).
6. **rpd.docx** — синхронизировать с rpd.md при изменении объёма часов в учебном плане.

---

## История проверки

| Дата | Действие |
|------|----------|
| 2026-07-20 | Первичный аудит; исправлены rpd, resources, methodical-guidelines, LICENSE, links, rubrics LR1–8 |
| 2026-07-21 | Обновление до 10 ЛР; grading 10×4%, новые lab03–lab10 |
