# Отчёт о проверке по [quality-checklist.md](quality-checklist.md)

**Дата проверки:** 2026-07-20  
**Архитектура ФОС:** вариант A  
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
| КИМ: условия, формат, критерии, шкала | ✅ | ЛР1–8, ПЗ1–5, COP, PRC — в файлах модулей и Project/ |
| Баллы рубрик согласованы с итоговой таблицей | ✅ | 40+10+15+30+5=100; PRC rubrics §1 = 100 %; ЛР × 5 % |
| Правила LLM и внешних ресурсов | ✅ | [students/README.md](../methodical-guidelines/students/README.md), [project_guidelines.md](../Project/project_guidelines.md) §10 |
| Методические указания | ✅ | [methodical-guidelines/](../methodical-guidelines/) — заполнены |
| Ресурсы с аннотациями и ссылками | ✅ | [resources/README.md](../resources/README.md) |
| Авторы и вклад | ✅ | [team/README.md](../team/README.md) — 4 автора (КубГУ, ПГТУ, РУДН, ФУ) |
| Лицензия | ✅ | [LICENSE.md](../LICENSE.md) — CC BY 4.0 |

---

## Техническая проверка

| Критерий | Статус | Комментарий |
|----------|:------:|-------------|
| Нет `[ЗАПОЛНИТЬ]` в публикуемых md | ✅ | Устранено в rpd.md; TODO в practice01 — учебный skeleton кода, не маркер ФОС |
| Относительные Markdown-ссылки | ✅ | 275 проверено; 0 битых (после добавления resources/README, правки LICENSE) |
| Временные файлы Office | ✅ | `~$*`, `*.tmp` не найдены |
| .DS_Store | ⚠️ | Добавлен [.gitignore](../.gitignore); локальные `.DS_Store` удалить из git при коммите |
| Крупные файлы | ✅ | presentation_itmo.pdf ~3.7 MB (< лимита GitHub 100 MB) |

---

## Согласованность варианта A

| Элемент | Статус |
|---------|:------:|
| ЛР = milestones команды | ✅ |
| project_guidelines + charter + milestone_report | ✅ |
| peer-review + grading | ✅ |
| Exam/ помечен неиспользуемым | ✅ |
| instructor_guide календарь ЛР1–8 | ✅ |

---

## Выборочная проверка КИМ

| КИМ | Условия | Формат сдачи | Критерии Б/С/П | Шкала в grading |
|-----|---------|--------------|----------------|-----------------|
| ЛР1 | lab01_eda.md | notebook + report | § Критерии | 5 % |
| ПЗ1 | quiz_pz01_structure.md | 10 мин LMS | quiz_pz01_key.md | 2 % |
| COP | competition.md | submission + отчёт | scoring.md | 15 % |
| PRC | rubrics.md | demo + PDF | §1 A–F | 30 % |

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
