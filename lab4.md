# Лабораторная работа №4

## Коррекция истории: amend, reset, revert, stash

**Время:** 40–50 минут

* * *

## Теоретическая справка

*   `git commit --amend` – изменить последний коммит.
*   `git reset --soft/mixed/hard` – откат коммитов (локально!).
*   `git revert` – безопасная отмена опубликованного коммита.
*   `git stash` – временное сохранение незакоммиченных изменений.

* * *

## Ход работы

### Шаг 1. Подготовка истории (5 мин)

```
mkdir lab4_history && cd lab4_history
git init
echo "A" > a.txt; git add .; git commit -m "A"
echo "B" > b.txt; git add .; git commit -m "B"
echo "C" > c.txt; git add .; git commit -m "C"
```

### Шаг 2. amend (5 мин)

```
echo "D" > d.txt
git add d.txt
git commit --amend -m "C и D (забыл D)"
```

### Шаг 3. soft reset (5 мин)

```
git reset --soft HEAD~1
git commit -m "C и D правильно"
```

### Шаг 4. mixed reset (5 мин)

```
echo "E" > e.txt; git add e.txt; git commit -m "E"
git reset HEAD~1
```

### Шаг 5. hard reset (3 мин)

```
git reset --hard HEAD~1
```

### Шаг 6. revert (6 мин)

```
echo "Ошибочный коммит" > mistake.txt
git add mistake.txt
git commit -m "Добавить mistake.txt"
git revert HEAD --no-edit
```

### Шаг 7. stash (8 мин)

```
echo "Недоделанная работа" > wip.txt
git add wip.txt
git stash save "WIP перед переключением"
git status
echo "Другое изменение" > other.txt; git add .; git commit -m "Other"
git stash pop
```

* * *

## Самостоятельное задание

Сделайте 3 коммита, измените сообщение второго через `git rebase -i`, спрячьте файл, сделайте `git reset --hard` на два коммита назад, затем найдите потерянный коммит через `git reflog` и вернитесь к нему.

* * *

## Контрольные вопросы

1.  Можно ли восстановить данные после `git reset --hard`? Как?
2.  Когда использовать `git revert` вместо `git reset`?
3.  Чем отличается `stash pop` от `stash apply`?
