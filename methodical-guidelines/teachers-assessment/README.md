# Методические указания преподавателям по оцениванию

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Архитектура:** вариант A

---

## 1. Система оценивания

| Компонент | Вес | Документ |
|-----------|-----|----------|
| ЛР1–8 (milestones) | 40 % (×5 %) | [docs/grading.md](../../docs/grading.md) |
| ПЗ1–5 | 10 % | [practical_assignments.md](../../docs/practical_assignments.md) + ключи `*_key.md` |
| COP | 15 % | [M3/competition/](../../M3-neural-networks/competition/) |
| PRC | 30 % | [Project/rubrics.md](../../Project/rubrics.md) |
| Peer-review | 5 % + \(k_{peer}\) | [peer_review.md](../../Project/peer_review.md) |

Шкала уровней КИМ: **Базовый 60 % · Средний 80 % · Продвинутый 100 %**.

---

## 2. Оценивание milestones ЛР

1. Проверьте артефакты из [grading.md](../../docs/grading.md) (пути в репозитории команды).
2. Сверьте [milestone_report.md](../../Project/templates/milestone_report.md) и commit hash.
3. Выставьте уровень Б/С/П по критериям в файле соответствующей ЛР.
4. На защите задайте вопрос **не только докладчику**, но и другим ролям.

**Формула:** вклад ЛР = 5 % × (уровень % / 100).

---

## 3. Оценивание PRC

Используйте [Project/rubrics.md](../../Project/rubrics.md) (критерии A–F, сумма весов 100 %).

После командной оценки примените **\(k_{peer}\)** из peer-review (0,7–1,3) к части PRC.

Красные флаги: free-rider, NDA, demo не запускается — см. [instructor_guide.md](../../Project/instructor_guide.md).

---

## 4. Обратная связь и апелляции

**На milestone (15 мин/команда):**

1. Соответствие шаблону — да/нет.
2. Две сильные стороны.
3. Два обязательных исправления до следующего milestone.
4. Предварительный уровень Б/С/П.

**Апелляции:** в течение 5 рабочих дней после публикации баллов; студент указывает конкретный КИМ и аргумент; пересмотр по git log и rubrics.

---

## 5. LLM и академическая честность

При подозрении на непонимание сгенерированного кода — углублённый опрос на защите по зоне роли.  
Снижение оценки за PRC / peer-review при подтверждённом free-riding.

Единая политика для студентов: [students/README.md](../students/README.md).

---

## 6. Ссылки

- [Project/instructor_guide.md](../../Project/instructor_guide.md) — сопровождение проекта
- [M1–M4 instructor guides](../../M1-analysis-and-preparation/instructor_guide.md) — по модулям
- [measurement_model.md](../../docs/measurement_model.md) — competence × КИМ
