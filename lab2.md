 [← Назад к лабораторным работам](readme.md)

# Лабораторная работа №2

## Ветвление и слияние (fast-forward, трёхстороннее слияние, конфликты)

**Время:** 40–50 минут

* * *

## Теоретическая справка

*   **Ветка (branch)** – подвижный указатель на коммит.
*   **HEAD** – указатель на текущую ветку/коммит.
*   **Fast-forward merge** – перемещение указателя вперёд (линейная история).
*   **Трёхстороннее слияние** – создание отдельного коммита слияния.
*   **Конфликт** – два изменения в одной и той же строке.

* * *

## Ход работы

### Шаг 1. Подготовка (5 мин)

```
mkdir lab2_branching
cd lab2_branching
git init
echo "Строка 1" > file.txt
echo "Основной контент" > main.txt
git add .
git commit -m "Начальный коммит"
```

### Шаг 2. Создание ветки feature (3 мин)

```
git checkout -b feature
```

### Шаг 3. Работа в feature (5 мин)

```
echo "Строка из feature" >> file.txt
echo "Данные фичи" > feature.txt
git add .
git commit -m "Добавить feature.txt и изменить file.txt"
```

### Шаг 4. Работа в main (5 мин)

```
git checkout main
echo "Строка из main" >> file.txt
echo "Важный файл main" > main_data.txt
git add .
git commit -m "Добавить main_data.txt и изменить file.txt"
```

### Шаг 5. Слияние (3 мин)

```
git merge feature
```

### Шаг 6. Создание конфликта (8 мин)

```
git checkout -b bugfix
echo "Исправление bugfix: первая строка" > file.txt
git commit -am "Bugfix: изменить первую строку"

git checkout main
echo "Версия main: первая строка" > file.txt
git commit -am "Main: изменить первую строку по-другому"
```

### Шаг 7. Разрешение конфликта (8 мин)

```
git merge bugfix   # КОНФЛИКТ
# Отредактируйте file.txt вручную, уберите маркеры <<<<<<<, =======, >>>>>>>
git add file.txt
git commit -m "Слияние bugfix: разрешить конфликт"
```

### Шаг 8. Просмотр графа (3 мин)

```
git log --graph --oneline --all --decorate
```

* * *

## Самостоятельное задание

Создайте ветку `experiment` от `main`, добавьте файл `exp.log`, в `main` измените `main_data.txt`, выполните слияние с сообщением `"Merge experiment branch"`.

* * *

## Контрольные вопросы

1.  Что такое HEAD?
2.  Когда Git не может выполнить слияние автоматически?
3.  Как прервать слияние?
