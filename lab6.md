# Лабораторная работа №6

## Интерактивный rebase, cherry-pick, теги, reflog

**Время:** 40–50 минут

* * *

## Теоретическая справка

*   `git rebase -i` – интерактивное переписывание истории (squash, reword, edit, drop).
*   `git cherry-pick` – копирование коммита из одной ветки в другую.
*   `git tag` – метка на коммите (лёгкая / аннотированная).
*   `git reflog` – журнал перемещений HEAD (спасательный круг).

* * *

**⚠️ ВАЖНО: Настройка редактора перед rebase**  
Команда `git rebase -i` открывает редактор. Если вы не знакомы с `vim` (он часто стоит по умолчанию), лучше сменить редактор на более простой:

```
# Установить редактор nano (проще для новичков)
git config --global core.editor "nano"

# Или для VS Code:
git config --global core.editor "code --wait"

# Или для Sublime Text:
git config --global core.editor "subl -w"
```

Если вы застряли в vim: нажмите `Esc`, затем введите `:q!` и нажмите `Enter` для выхода без сохранения.

* * *

## Ход работы

### Шаг 1. Настройка редактора (1 мин)

```
git config --global core.editor "nano"
```

Если nano не нравится, используйте любой другой редактор.

### Шаг 2. Подготовка репозитория (5 мин)

```
mkdir lab6_advanced && cd lab6_advanced
git init
for i in {1..5}; do echo "Commit $i" > f$i.txt; git add .; git commit -m "Step $i"; done
git log --oneline
```

### Шаг 3. Создание ветки для rebase (4 мин)

```
git checkout -b feature-rebase
echo "Feature A" > fa.txt; git add .; git commit -m "Feature A"
echo "Feature B" > fb.txt; git add .; git commit -m "Feature B"
echo "Fix" >> f3.txt; git commit -am "Fix typo"
git log --oneline
```

### Шаг 4. Интерактивный rebase (12 мин)

Запустите интерактивный rebase для трёх последних коммитов:

```
git rebase -i HEAD~3
```

Откроется редактор (nano, если настроили). Вы увидите примерно такое:

```
pick a1b2c3d Feature A
pick e4f5g6h Feature B
pick i7j8k9l Fix typo
```

**Инструкция для nano:**

*   Стрелками перемещайтесь по строкам.
*   Измените `pick` на `squash` (или просто `s`) для строки `Feature B`.
*   Измените `pick` на `reword` (или просто `r`) для строки `Fix typo`.
*   Должно получиться:

```
pick a1b2c3d Feature A
squash e4f5g6h Feature B
reword i7j8k9l Fix typo
```

*   Нажмите `Ctrl+O` (сохранить), затем `Enter`, затем `Ctrl+X` (выход).

**Что произойдёт дальше:**

1.  Git объединит коммиты Feature A и Feature B (squash) – откроется редактор для составления нового сообщения коммита. Объедините сообщения или напишите новое, например: `Feature A and B combined`. Сохраните и выйдите.
2.  Затем Git попросит изменить сообщение для коммита `Fix typo`. Напишите новое сообщение: `Fix critical typo in f3.txt`. Сохраните и выйдите.

**✅ Готово!** После успешного rebase выполните:

```
git log --oneline
```

Теперь коммитов должно стать меньше, а сообщения – изменены.

### Шаг 5. Cherry-pick (6 мин)

```
git checkout main
git log --oneline feature-rebase   # найти хэш последнего коммита (исправленный Fix critical typo)
git cherry-pick <хэш>
git log --oneline
```

### Шаг 6. Создание тегов (6 мин)

```
git tag v1.0.0                           # легковесный тег
git tag -a v1.1.0 -m "Аннотированный тег"  # аннотированный
git tag -n                                 # посмотреть все теги
```

### Шаг 7. Переключение по тегу (3 мин)

```
git checkout v1.0.0       # detached HEAD
git checkout -b hotfix-from-tag   # создать ветку от тега
```

### Шаг 8. Спасательный круг — reflog (6 мин)

```
git checkout main
git reset --hard HEAD~3
git log --oneline         # коммитов нет
git reflog                # покажет всю историю перемещений HEAD
git reset --hard HEAD@{1} # вернуться обратно
```

* * *

**💡 Если вы застряли в vim:**

*   Нажмите `Esc` (чтобы выйти из режима редактирования)
*   Введите `:q!` и нажмите `Enter` (выйти без сохранения)
*   Или `:wq` и нажмите `Enter` (сохранить и выйти)

Либо заранее смените редактор: `git config --global core.editor "nano"`

* * *

## Самостоятельное задание

Создайте ветку `messy`, сделайте 4 коммита. С помощью `git rebase -i HEAD~4` объедините первые два коммита и переименуйте третий. Перенесите один коммит из `messy` в `main` через `cherry-pick`. Создайте аннотированный тег `exercise-v1`.

* * *

## Контрольные вопросы

1.  Почему rebase опасен для публичных веток (которые уже отправлены на удалённый репозиторий)?
2.  В чём разница между `git cherry-pick` и `git merge`?
3.  Как восстановить потерянные коммиты после случайного `git reset --hard`?
4.  Как сменить редактор по умолчанию для Git?

* * *

## Типичные ошибки и их решение

Ошибка

Решение

`Waiting for your editor to close the file...` зависло

Закройте редактор (в nano: Ctrl+X, в vim: Esc → :q!). Или убейте процесс в другом терминале.

`error: There was a problem with the editor 'vi'`

Смените редактор: `git config --global core.editor "nano"`

После rebase пропали коммиты

Используйте `git reflog` и `git reset --hard <хэш>`

Конфликт при cherry-pick

Разрешите конфликт, затем `git add` → `git cherry-pick --continue`

* * *
