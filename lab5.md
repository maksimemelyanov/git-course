# Лабораторная работа №5

## Модель ветвления Git Flow

**Время:** 40–50 минут

* * *

## Теоретическая справка

*   **main** – релизная ветка.
*   **develop** – интеграционная ветка.
*   **feature/\*** – ветки новых возможностей (от develop).
*   **release/\*** – подготовка к релизу (от develop).
*   **hotfix/\*** – срочные исправления (от main).

Все слияния выполняются с `--no-ff` для сохранения истории.

* * *

## Ход работы

### Шаг 1. Инициализация (5 мин)

```
mkdir lab5_gitflow && cd lab5_gitflow
git init
echo "# Git Flow" > README.md; git add .; git commit -m "Initial main"
git checkout -b develop
```

### Шаг 2. Feature-ветка (8 мин)

```
git checkout -b feature/login develop
echo "login: admin" > auth.cfg
git add auth.cfg; git commit -m "Add auth config"
git checkout develop
git merge --no-ff feature/login -m "Merge feature/login"
```

### Шаг 3. Ещё одна feature (6 мин)

```
git checkout -b feature/db develop
echo "db_host=localhost" > db.cfg
git add .; git commit -m "Add DB config"
git checkout develop
git merge --no-ff feature/db -m "Merge DB feature"
```

### Шаг 4. Release-ветка (8 мин)

```
git checkout -b release/v1.0 develop
echo "version=1.0" > version.txt
git add version.txt; git commit -m "Bump to 1.0"
git checkout main
git merge --no-ff release/v1.0 -m "Release v1.0"
git tag -a v1.0 -m "Version 1.0"
git checkout develop
git merge --no-ff release/v1.0 -m "Back-merge release"
```

### Шаг 5. Hotfix (8 мин)

```
git checkout main
git checkout -b hotfix/critical main
echo "patch applied" > patch.log
git add .; git commit -m "Critical security fix"
git checkout main
git merge --no-ff hotfix/critical -m "Hotfix critical"
git tag v1.0.1
git checkout develop
git merge --no-ff hotfix/critical -m "Merge hotfix into develop"
```

* * *

## Самостоятельное задание

Нарисуйте схему Git Flow. Объясните, почему `release` сливается и в `main`, и в `develop`.

* * *

## Контрольные вопросы

1.  Почему нужен `--no-ff` в Git Flow?
2.  Можно ли коммитить прямо в `develop`? Почему это плохая практика?
3.  Что будет, если забыть слить `hotfix` в `develop`?
