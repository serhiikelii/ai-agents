# Git Workflow - Обязательные правила
---
## 🎯 КЛЮЧЕВОЕ ПРАВИЛО
**Git commit ТОЛЬКО после успешного build** - НЕ коммитить breaking changes.
**Push НЕМЕДЛЕННО после коммита** - сразу после КАЖДОГО коммита.
---
## 📋 Стандартный workflow
### 1. Завершил задачу → проверка build
**ОБЯЗАТЕЛЬНАЯ проверка перед коммитом:**
```bash
# Python projects
pytest
# ИЛИ
python -m pytest
# Node.js projects
npm test && npm run build
# Go projects
go test ./... && go build
# TypeScript projects
npm run typecheck && npm run build
```
**Если build FAILED:**
- ❌ НЕ коммитить
- Исправить ошибки
- Запустить build снова
- Только после SUCCESS → commit
### 2. Build SUCCESS → commit
```bash
git add .
git commit -m "feat: добавлена функция X
- Реализовал Y
- Добавил тесты для Z
- Обновил документацию"
```
**Структура commit message:**
```
<type>: <краткое описание>
<пустая строка>
- <детальный список изменений>
- <что добавлено/изменено/исправлено>
```
**Types:**
- `feat`: новая функциональность
- `fix`: исправление бага
- `refactor`: рефакторинг без изменения функциональности
- `test`: добавление тестов
- `docs`: обновление документации
- `chore`: вспомогательные изменения (deps, config)
### 3. Commit SUCCESS → push немедленно
```bash
git push
```
**Правило:** `git commit && git push` сразу после КАЖДОГО коммита.
**Почему немедленный push:**
- Синхронизация с командой
- Backup кода
- CI/CD запускается сразу
- Избежание merge conflicts
---
## 🚫 Исключение: Pattern агенты
**Pattern агенты НЕ пушить:**
- Pattern агенты остаются локальными
- Workflow: `git add . && git commit -m "..." # STOP - НЕ push`
---
## ⚠️ Обработка ошибок
### Ошибка 1: Build failed перед коммитом
```bash
# ❌ НЕПРАВИЛЬНО
git add .
git commit -m "..."  # БЕЗ проверки build
# ✅ ПРАВИЛЬНО
pytest  # проверка
# Если FAILED - исправить
# Запустить pytest снова
# Только после SUCCESS:
git add .
git commit -m "..."
```
### Ошибка 2: Push failed (conflicts)
```bash
# Push failed: remote changes
git pull --rebase
# Решить conflicts если есть
# Запустить build снова (после merge!)
pytest
# Push снова
git push
```
### Ошибка 3: Забыл push после commit
```bash
# Проверить unpushed commits:
git status
# Если есть unpushed:
git push
```
---
## 🔄 Workflow с ветками
### Создание feature branch
```bash
git checkout -b feature/payment-integration
# Работа над задачей
# Commit + push
git add .
git commit -m "feat: stripe integration"
git push origin feature/payment-integration
```
### Merge в main
```bash
# Проверить что все tests passing
pytest
# Merge в main
git checkout main
git pull
git merge feature/payment-integration
# Запустить build снова (после merge!)
pytest
git push
```
---
## ✅ Checklist перед коммитом
```
□ Build запущен и SUCCESS?
□ Все tests passing?
□ Нет breaking changes?
□ Commit message понятный?
□ Готов к push сразу после commit?
```
---
## 🎓 Примеры правильного workflow
### Пример 1: Добавление новой функции
```bash
# 1. Реализовал функцию
# 2. Проверка build
pytest
# ✅ All tests passed
# 3. Commit
git add .
git commit -m "feat: добавлена функция calculate_discount
- Реализована логика расчета скидок
- Добавлены unit tests (5 tests, 100% coverage)
- Обновлена документация API"
# 4. Push немедленно
git push
```
### Пример 2: Исправление бага
```bash
# 1. Исправил bug
# 2. Проверка build
npm test && npm run build
# ✅ Build successful
# 3. Commit
git add .
git commit -m "fix: исправлена ошибка валидации email
- Добавлена проверка на пустую строку
- Обновлен regex для email validation
- Добавлен test case для edge case"
# 4. Push немедленно
git push
```
### Пример 3: Рефакторинг
```bash
# 1. Рефакторинг
# 2. Проверка build (КРИТИЧНО!)
pytest
# ✅ All tests passed
# 3. Commit
git add .
git commit -m "refactor: оптимизирована загрузка данных
- Убран N+1 query problem
- Добавлен eager loading
- Performance улучшен на 10x"
# 4. Push немедленно
git push
```
---
## 🚨 Частые ошибки
### Ошибка 1: Коммит без build проверки
```bash
# ❌ НЕПРАВИЛЬНО
git add .
git commit -m "..."
git push
# Потом выясняется что build broken
# ✅ ПРАВИЛЬНО
pytest  # СНАЧАЛА build
git add .
git commit -m "..."
git push
```
### Ошибка 2: Накопление unpushed commits
```bash
# ❌ НЕПРАВИЛЬНО
git commit -m "feat: A"
git commit -m "feat: B"
git commit -m "feat: C"
# ... несколько commits накопилось
git push  # ПОЗДНО
# ✅ ПРАВИЛЬНО
git commit -m "feat: A" && git push
git commit -m "feat: B" && git push
git commit -m "feat: C" && git push
```
### Ошибка 3: Игнорирование failed tests
```bash
# ❌ НЕПРАВИЛЬНО
pytest
# 2 tests failed
git add .  # игнорирую failed tests
git commit -m "..."
# ✅ ПРАВИЛЬНО
pytest
# 2 tests failed
# ИСПРАВИТЬ failed tests
pytest  # снова проверить
# ✅ All tests passed
git add .
git commit -m "..."
```
---
## 📋 Последняя микрозадача TodoWrite
**Правильная формулировка:**
```json
{
  "content": "Git commit + проверка build/tests + push",
  "status": "pending",
  "activeForm": "Выполняю git commit с проверкой"
}
```
**Workflow выполнения этой микрозадачи:**
1. Запустить build (pytest/npm test/go test)
2. Если SUCCESS → commit
3. Если FAILED → исправить и повторить
4. Push немедленно после commit
5. Проверить что push успешен
