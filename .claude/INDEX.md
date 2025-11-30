# 📚 AI Agents Factory - Навигация по документации

**Цель:** Быстрый доступ к ключевым правилам и промптам для всех агентов

---

## 🚨 ОБЯЗАТЕЛЬНОЕ ЧТЕНИЕ (для всех агентов)

### 1. Общие правила (ЧИТАТЬ ПЕРВЫМ!)
📄 **[common_agent_rules.md](.claude/knowledge/common_agent_rules.md)**
- ✅ Стандартная структура выполнения задач
- ✅ TodoWrite обязательно (3-7 микрозадач)
- ✅ Post-Task Checklist (рефлексия + спросить пользователя + git)
- ✅ **Context7 интеграция (ШАГ 2!)**
- ✅ Эскалация непрофильных задач
- ✅ Погружение в контекст перед работой

### 2. Context7 Integration (для code-writing агентов)
📄 **[context7_integration.md](.claude/rules/context7_integration.md)**
- Интеграция с MCP Server Context7
- Поиск актуальной документации библиотек
- **ВАЖНО:** Вызывать на шаге 2 (после анализа задачи)

### 3. Git Workflow
📄 **[git_workflow.md](.claude/rules/git_workflow.md)**
- Правила коммитов
- Pre-commit checklist
- Push workflow

---

## 🎭 CORE АГЕНТЫ (Archon Core Team)

| Роль | Файл | Когда использовать |
|------|------|-------------------|
| **Project Manager** | [project-manager.md](.claude/agents/project-manager.md) | Оркестрация задач, делегирование |
| **Blueprint Architect** | [blueprint-architect.md](.claude/agents/blueprint-architect.md) | Архитектура, дизайн систем |
| **Implementation Engineer** | [implementation-engineer.md](.claude/agents/implementation-engineer.md) | Написание кода |
| **Quality Guardian** | [quality-guardian.md](.claude/agents/quality-guardian.md) | Тестирование, код-ревью |
| **Deployment Engineer** | [deployment-engineer.md](.claude/agents/deployment-engineer.md) | CI/CD, развертывание |

---

## 🔧 СПЕЦИАЛИЗИРОВАННЫЕ АГЕНТЫ (Universal Agents)

| Специализация | Файл | Ключевые слова |
|--------------|------|----------------|
| API Development | [api-development-agent.md](.claude/agents/api-development-agent.md) | api, endpoints, rest, graphql |
| UI/UX Design | [uiux-design-agent.md](.claude/agents/uiux-design-agent.md) | ui, ux, interface, usability |
| Security Audit | [security-audit-agent.md](.claude/agents/security-audit-agent.md) | security, vulnerability, audit |

---

## 🔍 БЫСТРЫЙ ПОИСК

### Я code-writing агент, что читать?
1. ✅ [common_agent_rules.md](.claude/knowledge/common_agent_rules.md) - **ШАГ 2: Context7!**
2. ✅ [context7_integration.md](.claude/rules/context7_integration.md)
3. ✅ [git_workflow.md](.claude/rules/git_workflow.md)
4. ✅ Свой промпт: `.claude/agents/[role-name].md`

### Я Project Manager, что читать?
1. ✅ [common_agent_rules.md](.claude/knowledge/common_agent_rules.md)
2. ✅ [project-manager.md](.claude/agents/project-manager.md)

### Я создаю нового агента
1. ✅ [blueprint-architect.md](.claude/agents/blueprint-architect.md) - промпт создателя агентов
2. ✅ [common_agent_rules.md](.claude/knowledge/common_agent_rules.md) - правила для наследования

---

## 🎯 КРИТИЧЕСКИЕ ПРАВИЛА (КРАТКАЯ ВЫЖИМКА)

1. **ВСЕГДА** переключаться в роль (не работать как Claude Code)
2. **ОБЯЗАТЕЛЬНО** TodoWrite (3-7 микрозадач)
3. **ПОСЛЕДНИЕ 3 микрозадачи:**
   - Рефлексия
   - Спросить пользователя
   - Git commit + проверка логов
4. **Code-writing агенты:** Context7 на **ШАГЕ 2** (после анализа задачи)
5. **Эскалация:** Непрофильная задача → создать задачу для нужного агента

---

## 📂 Структура проекта

```
C:\Users\prose\Automation\ai-agents\
├── CLAUDE.md           # Правила проекта
├── INDEX.md            # (этот файл) Навигация
└── .claude\
    ├── agents\         # Библиотека агентов
    ├── knowledge\      # common_agent_rules.md (ОБЯЗАТЕЛЬНО!)
    ├── rules\          # context7_integration.md, git_workflow.md
    
```

---

**Версия:** 1.0
**Обновлено:** 2025-11-15
**Автор:** Project Manager (AI Agent Factory)
