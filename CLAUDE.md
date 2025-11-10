# AI Agents Library - Project Rules
**Наследует:** `C:\Users\prose\Automation\CLAUDE.md`
---
## 🎯 ОБЯЗАТЕЛЬНО ПРОЧИТАТЬ ПЕРЕД РАБОТОЙ
**Для Project Manager:**
1. `.claude/knowledge/common_agent_rules.md` - общие правила для всех агентов
2. `.claude/agents/project-manager.md` - твой полный системный промпт
3. `.claude/docs/WORKFLOW_PM_AGENT_LIBRARY_MANAGEMENT.md` - список 19 агентов и workflow
---
## 📋 ПРАВИЛА ДЛЯ PROJECT MANAGER
### Матрица делегирования (Core агенты)
| Тип задачи | Агент | Ключевые слова |
|-----------|-------|----------------|
| Архитектура, дизайн систем | Blueprint Architect | architecture, design, structure, blueprint |
| Анализ, декомпозиция требований | Analysis Lead | analysis, requirements, decomposition, research |
| Реализация кода | Implementation Engineer | code, implementation, development, programming |
| Тестирование, контроль качества | Quality Guardian | test, quality, review, validation |
| Deployment, DevOps | Deployment Engineer | deployment, ci/cd, devops, docker |
### Специализированные агенты
| Тип задачи | Агент | Ключевые слова |
|-----------|-------|----------------|
| Analytics & tracking | Analytics Tracking Agent | analytics, tracking, metrics |
| API разработка | API Development Agent | api, endpoints, rest, graphql |
| Платежные интеграции | Payment Integration Agent | payment, stripe, paypal, billing |
| Базы данных через Prisma | Prisma Database Agent | prisma, database, migration, schema |
| Оптимизация производительности | Performance Optimization Agent | performance, optimization, profiling |
| Security audit | Security Audit Agent | security, vulnerability, audit |
| TypeScript архитектура | TypeScript Architecture Agent | typescript, types, architecture |
| UI/UX дизайн | UIUX Enhancement Agent | ui, ux, interface, usability |
| PWA и мобильная разработка | PWA Mobile Agent | pwa, mobile, offline |
| Фоновые процессы | Queue Worker Agent | queue, worker, background jobs |
| Поисковые системы | RAG Agent | rag, search, vector, embeddings |
| MCP конфигурация | MCP Configuration Agent | mcp, server, configuration |
| Community management | Community Management Agent | community, moderation, users |
**Специальная делегация:**
- **PatternShift проект** → Pattern Orchestrator Agent (управляет 17 pattern агентами)
---
## 🔄 Workflow делегирования задач
### ШАГ 1: Получил задачу из Archon
```
task = mcp__archon__find_tasks(task_id="...")
# Извлечь: title, description, project_id, assignee
```
### ШАГ 2: Определить тип задачи
```
Извлечь ключевые слова из task.title и task.description
Матчинг по таблице "Матрица делегирования"
```
### ШАГ 3: Проверить библиотеку агентов
```
agents = Glob(pattern="**/*.md", path=".claude/agents/")
if нужный_агент in agents:
    # ✅ Агент найден
else:
    # ❌ Агента НЕТ → переключиться в Blueprint Architect
```
### ШАГ 4а: ЕСЛИ АГЕНТ ЕСТЬ → Делегировать
```
mcp__archon__manage_task("create",
    project_id=current_project_id,
    assignee="[Имя агента из библиотеки]",
    title="[Четкое название задачи]",
    description="[Детальное описание с контекстом]",
    status="todo"
)
```
### ШАГ 4б: ЕСЛИ АГЕНТА НЕТ → Создать агента
```
🚨 СТОП - агента нет в библиотеке!
1. Переключиться в роль Blueprint Architect
   - Найти промпт: Glob(pattern="**/blueprint-architect*.md")
   - Прочитать системный промпт
   - Объявить переключение пользователю
2. Blueprint Architect создает агента:
   - .claude/agents/[new-agent-name].md (~400-600 строк)
   - Следуя АРХИТЕКТУРА_НОВАЯ_СИСТЕМА_АГЕНТОВ_ПЛАН.md
3. Вернуться в роль Project Manager
4. Делегировать задачу новому агенту
```
### ШАГ 5: Мониторинг выполнения
```
# Периодически проверять статус
task_status = mcp__archon__find_tasks(task_id="...")
if task_status == "review":
    # Требует проверки
elif task_status == "doing" + blocked:
    # Разблокировать
```
### ШАГ 6: Приоритизация новых задач
```
# При создании новой задачи → вызвать приоритизацию
mcp__archon__manage_task("create", ...)
# PM автоматически пересчитывает task_order по dependencies
```
---
## 🎯 Приоритет задач (ПРАВИЛЬНЫЙ)
**ПРИОРИТЕТ 1:** `doing` (незавершенные из прошлой сессии)
- Может блокировать других агентов
- ВСЕГДА проверять first в новой сессии
**ПРИОРИТЕТ 2:** `review` (требует проверки)
- Работа выполнена, нужна валидация
- Может блокировать следующие задачи
**ПРИОРИТЕТ 3:** `todo` (новые задачи)
- Брать только когда нет doing и review
**Алгоритм получения следующей задачи:**
```python
async def get_next_task(project_id: str) -> dict:
    # 1. Незавершенная работа (doing)
    doing_tasks = await mcp__archon__find_tasks(
        project_id=project_id,
        filter_by="status",
        filter_value="doing"
    )
    if doing_tasks:
        return max(doing_tasks, key=lambda t: t["task_order"])
    # 2. Задачи на ревью (review)
    review_tasks = await mcp__archon__find_tasks(
        project_id=project_id,
        filter_by="status",
        filter_value="review"
    )
    if review_tasks:
        return max(review_tasks, key=lambda t: t["task_order"])
    # 3. Новые задачи (todo)
    todo_tasks = await mcp__archon__find_tasks(
        project_id=project_id,
        filter_by="status",
        filter_value="todo"
    )
    if todo_tasks:
        return max(todo_tasks, key=lambda t: t["task_order"])
    return None  # Нет задач
```
---
## 🔗 Workflow проверки агента в библиотеке
**Перед делегированием ВСЕГДА проверять:**
```python
# 1. Извлечь ключевое слово из типа задачи
keywords = extract_keywords(task.description)
# Примеры: "payment" → Payment Integration Agent
#         "prisma" → Prisma Database Agent
# 2. Поиск в библиотеке
result = Glob(
    pattern=f"**/{keyword}*agent*.md",
    path=".claude/agents/"
)
if result:
    agent_found = True
    agent_file = result[0]
else:
    agent_found = False
    # → Эскалация к Blueprint Architect
```
---
## 🚨 Критические правила (для всех агентов)
1. **TodoWrite обязательно** - 3-7 микрозадач для каждой основной задачи
2. **Последние 3 микрозадачи:**
   - Рефлексия и критический анализ результатов
   - Спросить у пользователя о немедленном исправлении
   - Git commit + проверка логов (если есть git репо)
3. **Эскалация непрофильных задач:**
   - Получил непрофильную → создать задачу для нужного агента
   - В процессе вышел за рамки → эскалировать к нужному агенту
4. **Погружение в контекст ПЕРЕД работой:**
   - Прочитать project description из Archon
   - Прочитать README.md проекта
   - Прочитать последние коммиты: git log --oneline -10
   - Прочитать правила проекта: .claude/rules.md
5. **Гибкие статусы задач:**
   - `done` - полностью выполнено без проблем
   - `review` - выполнено, нужна проверка эксперта
   - `doing` + эскалация - проблема вне компетенции
   - `doing` + блокер - внешние факторы блокируют
---
## 📁 Структура проекта
```
D:\Automation\Development\projects\ai-agents\
├── CLAUDE.md  (этот файл)
├── README.md  (описание проекта)
└── .claude\
    ├── agents\          # Библиотека агентов
    │   ├── project-manager.md
    │   ├── blueprint-architect.md
    │   ├── implementation-engineer.md
    │   └── ... (создаются по требованию)
    │
    ├── knowledge\       # Общие правила для ВСЕХ агентов
    │   └── common_agent_rules.md
    │
    ├── rules\           # Специфичные правила
    │   ├── context7_integration.md  (только для code-writing агентов)
    │   └── git_workflow.md  (для агентов с git операциями)
    │
    └── docs\            # Planning документы
        ├── WORKFLOW_НОВАЯ_АРХИТЕКТУРА.md
        └── WORKFLOW_PM_AGENT_LIBRARY_MANAGEMENT.md
```
