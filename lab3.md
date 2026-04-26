 [← Назад к лабораторным работам](readme.md)
 
 # Лабораторная работа №3

## Удалённые репозитории (GitHub)

**Время:** 40–50 минут

* * *

## Теоретическая справка

*   **origin** – стандартное имя удалённого репозитория.
*   **git clone** – копирование удалённого репозитория.
*   **git push** – отправка изменений.
*   **git pull = git fetch + git merge**.
*   **git fetch** – загрузка изменений без слияния.

* * *

## Ход работы

### Шаг 1. Создание репозитория на GitHub (5 мин)

Создайте новый репозиторий `lab3_remote` (пустой, без README).

### Шаг 2. Локальная инициализация и связь (5 мин)

```
mkdir lab3_remote && cd lab3_remote
git init
echo "# Lab3 Remote" > README.md
git add README.md
git commit -m "Initial commit"
git remote add origin https://github.com/ваш_логин/lab3_remote.git
git push -u origin main
```

### Шаг 3. Клонирование (вторая копия) (10 мин)

```
cd ..
git clone https://github.com/ваш_логин/lab3_remote.git lab3_remote_clone
cd lab3_remote_clone
echo "От второго разработчика" > dev2.txt
git add dev2.txt
git commit -m "Add dev2.txt"
git push
```

### Шаг 4. Стягивание в оригинал (3 мин)

```
cd ../lab3_remote
git pull origin main
```

### Шаг 5. Работа с удалённой веткой (10 мин)

```
git checkout -b feature-remote
echo "Удалённая фича" > feature.txt
git add .
git commit -m "Add feature file"
git push -u origin feature-remote
```

### Шаг 6. fetch vs pull (5 мин)

```
# на GitHub через веб-интерфейс измените README.md
git checkout main
git fetch origin
git status   # "Your branch is behind"
git merge origin/main
```

### Шаг 7. Удаление удалённой ветки (3 мин)

```
git push origin --delete feature-remote
```

* * *

## Самостоятельное задание

Создайте ветку `dev`, запушьте её на GitHub, затем удалите её через веб-интерфейс. После этого выполните `git remote prune origin` и убедитесь, что локальная ссылка на удалённую ветку исчезла.

* * *

## Контрольные вопросы

1.  Что такое `origin/main`?
2.  Опасен ли `git pull` при незакоммиченных изменениях?
3.  Как посмотреть все удалённые ветки?
