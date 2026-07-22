## 1. Основы

### Что такое Git

Git — распределенная система контроля версий.

Нужен, чтобы хранить историю изменений, откатываться назад и работать в команде.

---

### Git vs GitHub

**Git** — локальный инструмент для контроля версий.

**GitHub** — онлайн-сервис, где хранят Git-репозитории.

Git можно использовать без GitHub.

---

### Система контроля версий

Система, которая хранит историю изменений файлов.

Позволяет понять:

- что изменилось;
    
- кто изменил;
    
- когда изменил;
    
- как откатиться назад.
    

---

### Распределенная система контроля версий

В Git каждый разработчик имеет полную копию репозитория с историей.

Коммиты можно делать локально, даже без интернета.

---

### Репозиторий

Репозиторий — проект вместе с историей Git.

Обычно это папка с кодом и скрытой директорией `.git`.

---

### Локальный репозиторий

Репозиторий на твоем компьютере.

В нем ты делаешь изменения, коммиты, ветки и т.д.

---

### Удаленный репозиторий

Репозиторий на сервере.

Например:

```bash
GitHub
GitLab
Bitbucket
```

Нужен для совместной работы и хранения кода.

---

### Рабочая директория

Рабочая директория — обычные файлы проекта, которые ты редактируешь.

Например:

```bash
main.py
README.md
Dockerfile
```

---

### Staging area / Index

Staging area — промежуточная область перед коммитом.

```bash
working directory -> git add -> staging -> git commit -> repository
```

В коммит попадает только то, что добавлено в staging.

---

### Директория `.git`

`.git` — скрытая директория, где Git хранит всю внутреннюю информацию.

Там лежат:

- коммиты;
    
- ветки;
    
- настройки;
    
- история;
    
- объекты Git.
    

---

### Коммит

Коммит — сохраненное состояние проекта.

Можно думать как о точке сохранения.

```bash
git commit -m "add nginx config"
```

---

### Хэш коммита

Хэш коммита — уникальный ID коммита.

Например:

```bash
a1b2c3d
```

По нему можно найти конкретный коммит.

---

### Ветка

Ветка — отдельная линия разработки.

Например:

```bash
main
feature/login
hotfix/nginx-config
```

Позволяет работать над задачей отдельно от основной ветки.

---

### HEAD

HEAD — указатель на текущий коммит.

Обычно показывает, где ты сейчас находишься в истории Git.

```bash
HEAD -> main
```

---

### Ветка main / master

`main` или `master` — основная ветка проекта.

Раньше чаще использовали `master`, сейчас обычно используют `main`.

---

## 2. Настройка Git

### `git config`

Команда для настройки Git.

Пример:

```bash
git config --global user.name "Ivan"
git config --global user.email "ivan@example.com"
```

---

### Глобальный config

Глобальные настройки действуют для всех репозиториев пользователя.

```bash
git config --global user.name "Ivan"
```

Файл обычно находится здесь:

```bash
~/.gitconfig
```

---

### Локальный config

Локальные настройки действуют только внутри конкретного репозитория.

```bash
git config user.name "Ivan Project"
```

Хранятся здесь:

```bash
.git/config
```

---

### `user.name`

Имя автора коммита.

```bash
git config --global user.name "Ivan Ivanov"
```

Будет отображаться в истории Git.

---

### `user.email`

Email автора коммита.

```bash
git config --global user.email "ivan@example.com"
```

На GitHub/GitLab лучше использовать email, привязанный к аккаунту.

---

### Редактор по умолчанию

Редактор, который Git открывает для сообщений коммитов, rebase и merge.

Пример для Vim:

```bash
git config --global core.editor "vim"
```

Пример для VS Code:

```bash
git config --global core.editor "code --wait"
```

---

### Имя ветки по умолчанию

Можно задать имя основной ветки при `git init`.

```bash
git config --global init.defaultBranch main
```

После этого новые репозитории будут создаваться с веткой `main`.

---

### Credential helper

Credential helper хранит данные для авторизации по HTTPS.

Например:

```bash
git config --global credential.helper store
git config --global credential.helper cache
```

Нужен, чтобы не вводить токен каждый раз.

---

### SSH-ключ для Git

SSH-ключ позволяет подключаться к GitHub/GitLab без ввода пароля.

Создать ключ:

```bash
ssh-keygen -t ed25519 -C "ivan@example.com"
```

Публичный ключ добавляется в GitHub/GitLab.

---

### HTTPS vs SSH авторизация

**HTTPS** использует URL, логин и token.

```bash
https://github.com/user/repo.git
```

**SSH** использует SSH-ключ.

```bash
git@github.com:user/repo.git
```

Для постоянной работы обычно удобнее SSH.

---

## 3. Создание репозитория

### `git init`

Создает новый Git-репозиторий в текущей папке.

```bash
git init
```

После этого появляется директория `.git`.

---

### `git clone`

Клонирует существующий удаленный репозиторий.

```bash
git clone git@github.com:user/repo.git
```

Создает локальную копию проекта.

---

### Клонирование через HTTPS

Клонирование через HTTPS URL.

```bash
git clone https://github.com/user/repo.git
```

При push обычно нужен token.

---

### Клонирование через SSH

Клонирование через SSH URL.

```bash
git clone git@github.com:user/repo.git
```

Требует настроенный SSH-ключ.

---

### `git remote -v`

Показывает удаленные репозитории.

```bash
git remote -v
```

Пример вывода:

```bash
origin  git@github.com:user/repo.git (fetch)
origin  git@github.com:user/repo.git (push)
```

---

### `origin`

`origin` — стандартное имя удаленного репозитория.

Обычно появляется автоматически после `git clone`.

---

### Добавить удаленный репозиторий

Добавить remote можно так:

```bash
git remote add origin git@github.com:user/repo.git
```

После этого можно делать `git push`.

---

### Изменить remote URL

Меняется через `git remote set-url`.

```bash
git remote set-url origin git@github.com:user/new-repo.git
```

Полезно, если репозиторий переехал.

---

### Удалить remote repository

Удалить remote из локального Git:

```bash
git remote remove origin
```

Это не удаляет репозиторий на GitHub, только связь в локальном Git.

---

## 4. Базовый workflow

### `git status`

Показывает текущее состояние репозитория.

```bash
git status
```

Показывает:

- измененные файлы;
    
- staged файлы;
    
- untracked файлы;
    
- текущую ветку.
    

---

### `git add`

Добавляет файл в staging area.

```bash
git add file.txt
```

После этого файл готов к коммиту.

---

### `git add .`

Добавляет все изменения из текущей директории в staging.

```bash
git add .
```

Часто используется перед коммитом.

---

### `git commit`

Создает коммит из staged changes.

```bash
git commit
```

Откроется редактор для ввода commit message.

---

### `git commit -m`

Создает коммит с сообщением сразу из команды.

```bash
git commit -m "add nginx config"
```

Удобно для коротких сообщений.

---

### `git push`

Отправляет локальные коммиты в удаленный репозиторий.

```bash
git push origin main
```

После этого изменения появляются на GitHub/GitLab.

---

### `git pull`

Забирает изменения с remote и сразу применяет их локально.

```bash
git pull
```

По сути:

```bash
git fetch + git merge
```

---

### `git fetch`

Забирает изменения с remote, но не применяет их к рабочей ветке.

```bash
git fetch
```

Безопаснее, чем `pull`, если сначала хочешь посмотреть изменения.

---

### `git log`

Показывает историю коммитов.

```bash
git log
```

Краткий вариант:

```bash
git log --oneline
```

---

### `git diff`

Показывает изменения в файлах.

```bash
git diff
```

По умолчанию показывает unstaged changes.

---

## 5. Staging Area

### Что такое staging area

Staging area — место, куда попадают изменения перед коммитом.

```bash
working directory -> staging area -> commit
```

Нужна, чтобы выбрать, что именно попадет в коммит.

---

### Добавить файл в staging

Добавить конкретный файл:

```bash
git add file.txt
```

После этого файл станет staged.

---

### Добавить все изменения в staging

Добавить все изменения:

```bash
git add .
```

Или:

```bash
git add -A
```

---

### Убрать файл из staging

Убрать файл из staging, но оставить изменения в рабочей директории:

```bash
git restore --staged file.txt
```

Старый вариант:

```bash
git reset HEAD file.txt
```

---

### Staged changes

Staged changes — изменения, которые уже добавлены через `git add`.

Именно они попадут в следующий commit.

---

### Unstaged changes

Unstaged changes — изменения в рабочей директории, которые еще не добавлены в staging.

Их видно через:

```bash
git status
git diff
```

---

### Tracked files

Tracked files — файлы, которые Git уже отслеживает.

То есть они уже были добавлены в репозиторий раньше.

---

### Untracked files

Untracked files — новые файлы, которые Git еще не отслеживает.

Например, только что созданный файл:

```bash
new-script.sh
```

Чтобы начать отслеживать:

```bash
git add new-script.sh
```

---

### Частично добавить изменения в staging

Можно добавить не весь файл, а только часть изменений.

```bash
git add -p
```

Полезно для аккуратных atomic commits.

---

### Почему commit включает только staged changes

Git коммитит только то, что лежит в staging area.

Это позволяет собрать коммит только из нужных изменений, а лишнее оставить на потом.

---

## 6. Коммиты

### Что такое commit

Commit — сохраненный снимок изменений в репозитории.

Каждый commit фиксирует конкретное состояние проекта.

---

### Хэш коммита

Каждый commit имеет уникальный хэш.

Пример:

```bash
f3a9c21
```

По хэшу можно посмотреть, откатить или сравнить commit.

---

### Commit message

Commit message — описание изменений в коммите.

Пример:

```bash
add docker-compose for postgres
```

Нужно, чтобы по истории было понятно, что происходило.

---

### Atomic commit

Atomic commit — коммит, который содержит одно логически завершенное изменение.

Хорошо:

```bash
add nginx config
```

Плохо: в одном коммите nginx, README, багфикс и рефакторинг.

---

### Хороший commit message

Хорошее сообщение коротко объясняет, что изменилось.

Примеры:

```bash
add healthcheck endpoint
fix nginx upstream timeout
update postgres backup script
```

---

### Плохой commit message

Плохое сообщение ничего не объясняет.

Примеры:

```bash
fix
update
changes
123
wip
```

---

### `git commit --amend`

Изменяет последний commit.

```bash
git commit --amend
```

Можно поменять message или добавить забытые файлы.

Важно: amend переписывает последний commit.

---

### Изменить последний commit message

```bash
git commit --amend -m "new commit message"
```

Используется, если ошибся в названии последнего коммита.

---

### Добавить забытый файл в последний commit

Сначала добавить файл:

```bash
git add forgotten-file.txt
```

Потом обновить последний commit:

```bash
git commit --amend --no-edit
```

`--no-edit` оставляет старое сообщение.

---

### Пустой commit

Пустой commit создается без изменений в файлах.

```bash
git commit --allow-empty -m "trigger CI"
```

Иногда используют, чтобы запустить pipeline.

## 7. Просмотр истории

### `git log`
   
Показывает историю коммитов.

```bash
git log
```

Видно автора, дату, хэш и commit message.

---

### `git log --oneline`

Короткий вывод истории.

```bash
git log --oneline
```

Удобно быстро смотреть цепочку коммитов.

---

### `git log --graph`

Показывает историю в виде графа веток.

```bash
git log --graph --oneline --all
```

Полезно, когда были merge/rebase/несколько веток.

---

### `git show`

Показывает информацию о конкретном коммите.

```bash
git show a1b2c3d
```

Показывает message, автора и diff.

---

### `git diff`

Показывает изменения, которые еще не добавлены в staging.

```bash
git diff
```

То есть unstaged changes.

---

### `git diff --staged`

Показывает изменения, которые уже добавлены в staging.

```bash
git diff --staged
```

Это то, что попадет в следующий commit.

---

### `git blame`

Показывает, кто и в каком коммите изменил каждую строку файла.

```bash
git blame app.py
```

Полезно для расследования багов.

---

### Показать изменения в конкретном commit

```bash
git show <commit_hash>
```

Например:

```bash
git show a1b2c3d
```

Покажет, какие изменения внес этот commit.

---

### Показать изменения между ветками

```bash
git diff main..feature
```

Показывает разницу между ветками `main` и `feature`.

---

### Показать изменения между commit

```bash
git diff hash1 hash2
```

Показывает разницу между двумя конкретными коммитами.

---

## 8. Ветки

### Что такое branch

Branch — отдельная линия разработки.

Позволяет работать над задачей, не ломая основную ветку.

---

### `main`

`main` — современное стандартное имя основной ветки.

Обычно в ней лежит стабильная версия проекта.

---

### `master`

`master` — старое имя основной ветки.

Сейчас во многих проектах заменено на `main`.

---

### Feature branch

Ветка для разработки новой функциональности.

```bash
feature/auth
feature/payment
```

После завершения обычно вливается в `main`.

---

### Hotfix branch

Ветка для срочного исправления бага.

```bash
hotfix/nginx-timeout
```

Обычно создается от стабильной ветки.

---

### Release branch

Ветка для подготовки релиза.

```bash
release/1.2.0
```

В ней могут фиксить баги перед выпуском версии.

---

### `git branch`

Показывает локальные ветки.

```bash
git branch
```

Текущая ветка будет отмечена `*`.

---

### `git branch -a`

Показывает все ветки: локальные и удаленные.

```bash
git branch -a
```

Полезно после `git fetch`.

---

### `git switch`

Современная команда для переключения веток.

```bash
git switch main
```

Более понятная альтернатива `checkout`.

---

### `git checkout`

Старая универсальная команда.

Может переключать ветки и восстанавливать файлы.

```bash
git checkout main
```

Сейчас для веток лучше использовать `git switch`.

---

### Создать новую ветку

```bash
git branch feature/login
```

Создает ветку, но не переключается на нее.

Создать и сразу перейти:

```bash
git switch -c feature/login
```

---

### Переключиться на ветку

```bash
git switch feature/login
```

Или старый вариант:

```bash
git checkout feature/login
```

---

### Удалить локальную ветку

```bash
git branch -d feature/login
```

Если ветка не влита, Git может не дать удалить.

Принудительно:

```bash
git branch -D feature/login
```

---

### Удалить удаленную ветку

```bash
git push origin --delete feature/login
```

Удаляет ветку на remote-сервере.

---

### Переименовать ветку

Переименовать текущую ветку:

```bash
git branch -m new-name
```

Переименовать конкретную:

```bash
git branch -m old-name new-name
```

---

## 9. Merge

### Что такое merge

Merge — объединение изменений из одной ветки в другую.

Например, влить `feature` в `main`.

---

### `git merge`

Команда для объединения веток.

```bash
git merge feature/login
```

Выполняется из той ветки, куда нужно влить изменения.

---

### Fast-forward merge

Fast-forward merge — когда Git просто двигает указатель ветки вперед.

Так бывает, если в целевой ветке не было новых коммитов.

```bash
main:    A---B
feature:     C---D

after merge:

main:    A---B---C---D
```

---

### 3-way merge

3-way merge — merge с участием трех точек:

- общий предок;
    
- текущая ветка;
    
- вливаемая ветка.
    

Обычно создает merge commit.

---

### Merge commit

Merge commit — специальный коммит, который объединяет две ветки.

У него обычно два родительских коммита.

---

### Влить feature branch в main

```bash
git switch main
git merge feature/login
```

Так изменения из `feature/login` попадут в `main`.

---

### Влить main в feature branch

```bash
git switch feature/login
git merge main
```

Так feature-ветка обновится изменениями из `main`.

---

### Отменить merge

Если merge еще не завершен:

```bash
git merge --abort
```

Вернет состояние до начала merge.

---

### Merge conflict при merge

Merge conflict возникает, когда Git не может автоматически объединить изменения.

Например, две ветки изменили одну и ту же строку.

---

### Когда использовать merge

Merge используют, когда нужно сохранить реальную историю веток.

Часто применяется при командной работе и pull request.

---

## 10. Rebase

### Что такое rebase

Rebase — перенос коммитов на новую базу.

Как будто твоя ветка была создана от более свежего `main`.

---

### `git rebase`

Команда для перебазирования ветки.

```bash
git rebase main
```

Обычно выполняется из feature-ветки.

---

### Rebase feature branch на main

```bash
git switch feature/login
git fetch
git rebase origin/main
```

После этого коммиты feature-ветки окажутся поверх свежего `main`.

---

### Rebase vs merge

**Merge** сохраняет историю как есть и может создать merge commit.

**Rebase** переписывает историю и делает ее более линейной.

---

### Interactive rebase

Interactive rebase позволяет редактировать историю коммитов.

Можно:

- объединять коммиты;
    
- менять сообщения;
    
- удалять коммиты;
    
- менять порядок.
    

---

### `git rebase -i`

Запускает интерактивный rebase.

```bash
git rebase -i HEAD~3
```

Откроется список последних 3 коммитов.

---

### Squash commits

Squash — объединение нескольких коммитов в один.

В interactive rebase используется:

```bash
squash
```

Полезно перед merge в `main`.

---

### Reword commit

Reword — изменить commit message.

В interactive rebase:

```bash
reword
```

Сам код не меняется, меняется только сообщение.

---

### Drop commit

Drop — удалить коммит из истории.

В interactive rebase:

```bash
drop
```

Или просто удалить строку с коммитом.

---

### Изменить порядок commits

В `git rebase -i` можно поменять строки местами.

Так меняется порядок коммитов в истории.

---

### Отменить rebase

```bash
git rebase --abort
```

Отменяет rebase и возвращает состояние до него.

---

### Продолжить rebase

После решения конфликта:

```bash
git add file.txt
git rebase --continue
```

Git продолжит перебазирование.

---

### Почему нельзя rebase-ить shared branches

Rebase переписывает историю и меняет хэши коммитов.

Если ветка уже используется другими людьми, у них может сломаться история.

---

## 11. Merge Conflicts

### Что такое merge conflict

Merge conflict — ситуация, когда Git не может сам объединить изменения.

Требуется ручное решение.

---

### Почему появляются merge conflicts

Обычно потому что две ветки изменили одно и то же место в файле.

Git не понимает, какую версию оставить.

---

### Как conflict выглядит в файле

Git вставляет специальные маркеры:

```text
<<<<<<< HEAD
твоя версия
=======
версия другой ветки
>>>>>>> feature
```

Их нужно удалить после решения.

---

### `<<<<<<< HEAD`

Начало конфликтующего блока.

`HEAD` — это текущая ветка, в которой ты находишься.

---

### `=======`

Разделитель между двумя версиями кода.

Сверху текущая версия, снизу версия из другой ветки.

---

### `>>>>>>> branch`

Конец конфликтующего блока.

Показывает, из какой ветки пришла другая версия.

---

### Решить conflict вручную

Нужно открыть файл и выбрать правильный вариант.

После этого удалить маркеры:

```text
<<<<<<<
=======
>>>>>>>
```

---

### `git status` во время conflict

```bash
git status
```

Покажет файлы с конфликтами.

Например:

```text
both modified: app.py
```

---

### `git add` после решения conflict

После ручного исправления нужно сделать:

```bash
git add app.py
```

Так Git поймет, что конфликт решен.

---

### `git merge --abort`

Отменяет merge.

```bash
git merge --abort
```

Возвращает репозиторий к состоянию до merge.

---

### `git rebase --abort`

Отменяет rebase.

```bash
git rebase --abort
```

Полезно, если rebase пошел не так.

---

### `git rebase --continue`

Продолжает rebase после решения конфликта.

```bash
git add file.txt
git rebase --continue
```

---

## 12. Remote Repositories

### Что такое remote repository

Remote repository — удаленный репозиторий на сервере.

Например, на GitHub/GitLab.

Используется для обмена кодом между разработчиками.

---

### `origin`

`origin` — стандартное имя основного remote-репозитория.

Обычно создается автоматически после `git clone`.

---

### `git remote`

Команда для управления удаленными репозиториями.

```bash
git remote
```

Покажет список remote-имен.

---

### `git remote -v`

Показывает remote-репозитории и их URL.

```bash
git remote -v
```

Пример:

```text
origin git@github.com:user/repo.git (fetch)
origin git@github.com:user/repo.git (push)
```

---

### `git remote add`

Добавляет remote-репозиторий.

```bash
git remote add origin git@github.com:user/repo.git
```

---

### `git remote remove`

Удаляет remote из локальных настроек Git.

```bash
git remote remove origin
```

Сам репозиторий на GitHub не удаляется.

---

### `git remote set-url`

Меняет URL remote-репозитория.

```bash
git remote set-url origin git@github.com:user/new-repo.git
```

---

### Remote branch

Remote branch — ветка в удаленном репозитории.

Например:

```text
origin/main
origin/feature/login
```

Это локальное представление удаленной ветки.

---

### Tracking branch

Tracking branch — локальная ветка, связанная с remote-веткой.

Например:

```text
main -> origin/main
```

Git понимает, куда делать pull/push.

---

### Upstream branch

Upstream branch — remote-ветка, с которой связана локальная ветка.

Проверить:

```bash
git branch -vv
```

---

### `git push -u origin branch`

Пушит ветку и настраивает upstream.

```bash
git push -u origin feature/login
```

После этого можно писать просто:

```bash
git push
git pull
```

---

## 13. Fetch / Pull / Push

### `git fetch`

Забирает изменения с remote, но не применяет их к текущей ветке.

```bash
git fetch
```

Безопасно для просмотра изменений.

---

### `git pull`

Забирает изменения и сразу применяет их.

```bash
git pull
```

Обычно это `fetch + merge`.

---

### `git pull --rebase`

Забирает изменения и применяет твои коммиты поверх них.

```bash
git pull --rebase
```

Делает историю более линейной.

---

### `git push`

Отправляет локальные коммиты в remote.

```bash
git push
```

Или явно:

```bash
git push origin main
```

---

### `git push -u`

Пушит ветку и задает upstream.

```bash
git push -u origin feature/login
```

После этого Git запоминает связь веток.

---

### Fetch vs pull

`fetch` только скачивает изменения.

`pull` скачивает и сразу применяет.

```text
pull = fetch + merge/rebase
```

---

### Pull = fetch + merge/rebase

Обычный pull:

```text
git fetch + git merge
```

Pull с rebase:

```text
git fetch + git rebase
```

---

### Push локальной ветки в remote

```bash
git push -u origin feature/login
```

Создает такую же ветку на remote.

---

### Push tags

Отправить конкретный tag:

```bash
git push origin v1.0.0
```

Tags не всегда пушатся автоматически.

---

### Push всех tags

```bash
git push origin --tags
```

Отправляет все локальные tags в remote.

---

### Push rejected

Push rejected — Git отказался принимать push.

Часто причина: в remote есть новые коммиты, которых нет локально.

---

### Non-fast-forward error

Ошибка возникает, когда локальная ветка отстала от remote.

Обычно решение:

```bash
git pull --rebase
git push
```

Не надо сразу делать force push без понимания.

---

## 14. Force Push

### Что такое force push

Force push — принудительная отправка локальной истории в remote.

Он может перезаписать историю удаленной ветки.

---

### `git push --force`

```bash
git push --force
```

Принудительно перезаписывает remote-ветку.

Опасная команда.

---

### `git push --force-with-lease`

```bash
git push --force-with-lease
```

Более безопасный force push.

Проверяет, что remote не изменился неожиданно.

---

### Force push после rebase

После rebase хэши коммитов меняются.

Поэтому обычный push может быть rejected.

```bash
git push --force-with-lease
```

---

### Почему force push опасен

Он может удалить чужие коммиты из remote-ветки.

Особенно опасен в общих ветках типа `main`, `develop`.

---

### Почему `--force-with-lease` безопаснее

Он не перезапишет remote, если кто-то уже успел туда запушить новые изменения.

Это защита от случайного удаления чужой работы.

---

### Когда force push допустим

Допустим в своей feature-ветке.

Например, после rebase или squash перед pull request.

---

### Когда force push запрещен

Нельзя force push в общие ветки:

```text
main
master
develop
release
```

Если только это не согласовано с командой.

---

## 15. Restore / Reset / Revert

### `git restore`

Команда для восстановления файлов.

Используется, чтобы отменить изменения в рабочей директории.

---

### `git restore file`

Отменяет локальные изменения в файле.

```bash
git restore file.txt
```

Файл вернется к состоянию последнего коммита.

---

### `git restore --staged`

Убирает файл из staging.

```bash
git restore --staged file.txt
```

Изменения в файле останутся, но не попадут в commit.

---

### `git reset`

Команда для перемещения HEAD и изменения staging/working tree.

Может быть безопасной или опасной в зависимости от режима.

---

### `git reset --soft`

```bash
git reset --soft HEAD~1
```

Отменяет commit, но оставляет изменения в staging.

Полезно, если нужно переделать последний commit.

---

### `git reset --mixed`

```bash
git reset --mixed HEAD~1
```

Отменяет commit и убирает изменения из staging.

Файлы остаются измененными.

Это режим по умолчанию.

---

### `git reset --hard`

```bash
git reset --hard HEAD~1
```

Отменяет commit и удаляет изменения из рабочей директории.

Опасно: можно потерять работу.

---

### `git revert`

Создает новый commit, который отменяет изменения старого commit.

```bash
git revert a1b2c3d
```

Безопасно для общих веток.

---

### Reset vs revert

`reset` переписывает историю.

`revert` не переписывает историю, а создает новый отменяющий commit.

Для shared branches обычно используют `revert`.

---

### Отменить локальные изменения

```bash
git restore file.txt
```

Или все файлы:

```bash
git restore .
```

Удаляет незакоммиченные изменения.

---

### Отменить staged changes

```bash
git restore --staged file.txt
```

Файл останется измененным, но выйдет из staging.

---

### Отменить последний commit

Если commit только локальный:

```bash
git reset --soft HEAD~1
```

Или полностью удалить изменения:

```bash
git reset --hard HEAD~1
```

---

### Отменить pushed commit

Если commit уже запушен, лучше использовать:

```bash
git revert <commit_hash>
git push
```

Так безопаснее для команды.

---

### Безопасный rollback в общей ветке

В общей ветке лучше делать rollback через `git revert`.

```bash
git revert <bad_commit>
```

История остается честной и не ломает работу другим.

---

## 16. Stash

### Что такое stash

Stash — временное сохранение незакоммиченных изменений.

Полезно, если нужно быстро переключиться на другую ветку.

---

### `git stash`

Сохраняет текущие изменения во временное хранилище.

```bash
git stash
```

Рабочая директория станет чистой.

---

### `git stash list`

Показывает список stash.

```bash
git stash list
```

Пример:

```text
stash@{0}: WIP on main
```

---

### `git stash pop`

Применяет последний stash и удаляет его из списка.

```bash
git stash pop
```

Если будут конфликты, их нужно решить.

---

### `git stash apply`

Применяет stash, но не удаляет его из списка.

```bash
git stash apply
```

Полезно, если stash нужен еще раз.

---

### `git stash drop`

Удаляет stash.

```bash
git stash drop stash@{0}
```

---

### `git stash clear`

Удаляет все stash.

```bash
git stash clear
```

Опасно: можно потерять сохраненные изменения.

---

### Stash с сообщением

```bash
git stash push -m "wip nginx config"
```

Так проще понять, что лежит в stash.

---

### Stash untracked files

По умолчанию untracked files могут не попасть в stash.

Добавить их:

```bash
git stash -u
```

---

### Conflict после stash pop

Конфликт может возникнуть, если текущие файлы изменились.

Решение обычное:

- исправить файл;
    
- убрать conflict markers;
    
- сделать `git add`.
    

---

### Когда использовать stash

Когда изменения еще рано коммитить, но нужно:

- переключиться на другую ветку;
    
- сделать pull;
    
- срочно исправить баг.
    

---

## 17. Cherry-pick

### Что такое cherry-pick

Cherry-pick — перенос конкретного коммита из одной ветки в другую.

Не нужно вливать всю ветку целиком.

---

### `git cherry-pick`

Команда для применения конкретного commit.

```bash
git cherry-pick <commit_hash>
```

Создаст новый commit с теми же изменениями.

---

### Cherry-pick конкретного commit

```bash
git cherry-pick a1b2c3d
```

Применит изменения из этого commit в текущую ветку.

---

### Cherry-pick из другой ветки

Сначала найти commit:

```bash
git log other-branch --oneline
```

Потом применить:

```bash
git cherry-pick <commit_hash>
```

---

### Cherry-pick conflict

Конфликт может появиться, если изменения плохо применяются к текущей ветке.

Решается как обычный merge conflict.

---

### `git cherry-pick --abort`

Отменяет cherry-pick.

```bash
git cherry-pick --abort
```

Возвращает состояние до начала операции.

---

### `git cherry-pick --continue`

После решения конфликта:

```bash
git add file.txt
git cherry-pick --continue
```

---

### Когда использовать cherry-pick

Когда нужно забрать один конкретный фикс.

Например:

- перенести hotfix в release;
    
- забрать commit из feature-ветки;
    
- применить багфикс без merge всей ветки.
    

---

### Когда не использовать cherry-pick

Не стоит использовать, если нужно перенести много связанных коммитов.

В таком случае лучше merge или rebase.

---

## 18. Tags

### Что такое tag

Tag — метка на конкретный commit.

Обычно используется для версий и релизов.

---

### Lightweight tag

Lightweight tag — простая ссылка на commit.

```bash
git tag v1.0.0
```

Без дополнительного описания.

---

### Annotated tag

Annotated tag содержит автора, дату и сообщение.

```bash
git tag -a v1.0.0 -m "release v1.0.0"
```

Для релизов лучше использовать annotated tags.

---

### Version tag

Version tag — tag с номером версии.

Например:

```text
v1.0.0
v1.2.3
v2.0.0
```

Часто используется SemVer.

---

### `git tag`

Показывает список tags.

```bash
git tag
```

---

### Создать tag

Lightweight:

```bash
git tag v1.0.0
```

Annotated:

```bash
git tag -a v1.0.0 -m "release v1.0.0"
```

---

### Push tag

Отправить tag в remote:

```bash
git push origin v1.0.0
```

---

### Push всех tags

```bash
git push origin --tags
```

Отправляет все локальные tags.

---

### Удалить локальный tag

```bash
git tag -d v1.0.0
```

Удаляет tag только локально.

---

### Удалить remote tag

```bash
git push origin --delete v1.0.0
```

Удаляет tag из remote.

---

### Tags для releases

Tags часто используют для фиксации релизных версий.

Например, CI/CD может запускать release pipeline при появлении tag.

---

## 19. `.gitignore`

### Что такое `.gitignore`

`.gitignore` — файл со списком файлов и директорий, которые Git должен игнорировать.

Например:

```text
.env
node_modules/
*.log
```

---

### Игнорировать файлы

```text
secret.txt
config.local
```

Такие файлы не будут попадать в `git status` как untracked.

---

### Игнорировать директории

```text
node_modules/
dist/
build/
```

Слэш в конце обычно показывает, что это директория.

---

### Игнорировать логи

```text
*.log
logs/
```

Логи обычно не коммитят, потому что они локальные и быстро меняются.

---

### Игнорировать build artifacts

```text
dist/
build/
target/
```

Build artifacts обычно можно пересобрать заново.

---

### Игнорировать `.env`

```text
.env
.env.local
```

В `.env` часто лежат пароли, токены и локальные настройки.

---

### Игнорировать файлы IDE

```text
.idea/
.vscode/
*.iml
```

Часто зависит от команды: иногда часть `.vscode` специально коммитят.

---

### Файл уже отслеживается Git

Если файл уже был добавлен в Git, `.gitignore` не перестанет его отслеживать.

Нужно убрать его из индекса.

---

### `git rm --cached`

Убирает файл из Git, но оставляет его локально.

```bash
git rm --cached .env
```

После этого можно добавить файл в `.gitignore`.

---

### Глобальный `.gitignore`

Глобальный ignore действует для всех репозиториев пользователя.

```bash
git config --global core.excludesfile ~/.gitignore_global
```

Туда можно добавить личные IDE/OS-файлы.

---

### Почему `.gitignore` не удаляет файл из истории

`.gitignore` влияет только на новые untracked files.

Он не удаляет файлы из старых коммитов и не чистит историю.

---

## 20. Secrets

### Почему нельзя коммитить secrets

Secrets в Git могут попасть к другим людям, в CI/CD, логи и историю.

Даже если удалить файл потом, secret останется в истории.

---

### Пароль в Git

Пароли нельзя хранить в репозитории.

Лучше использовать:

- secret manager;
    
- переменные окружения;
    
- CI/CD secrets.
    

---

### Token в Git

Token в репозитории считается скомпрометированным.

Его нужно сразу отозвать и создать новый.

---

### Private key в Git

Private key нельзя коммитить никогда.

Особенно опасны:

```text
id_rsa
*.pem
*.key
```

---

### `.env` в Git

`.env` часто содержит secrets.

Обычно его добавляют в `.gitignore`.

Для примера можно хранить:

```text
.env.example
```

Без настоящих секретов.

---

### Secret случайно закоммичен

Нужно:

- удалить secret из кода;
    
- удалить из истории, если нужно;
    
- сделать rotate secret;
    
- проверить доступы.
    

Просто удалить файл новым коммитом недостаточно.

---

### Удалить secret из текущей ветки

Если commit еще не pushed:

```bash
git reset --soft HEAD~1
git restore --staged .env
echo ".env" >> .gitignore
git add .gitignore
git commit -m "remove env from git"
```

---

### Удалить secret из Git history

Для чистки истории используют специальные инструменты.

Например:

```bash
git filter-repo
```

Или BFG Repo-Cleaner.

После этого обычно нужен force push.

---

### Rotate leaked secret

Rotate означает заменить скомпрометированный secret.

Например:

- отозвать старый token;
    
- создать новый token;
    
- обновить CI/CD или secret manager.
    

Это обязательно, даже если secret удалили из Git.

---

### Secret scanning

Secret scanning — автоматическая проверка репозитория на токены, ключи и пароли.

Может быть в:

- GitHub;
    
- GitLab;
    
- CI/CD;
    
- pre-commit hooks.
    

---

### `.gitignore` для secrets

Пример:

```text
.env
.env.*
*.pem
*.key
id_rsa
secrets.yaml
```

Но `.gitignore` не защищает уже закоммиченные secrets.

---
## 21. Pull Request Flow

### Создать ветку от main

Перед началом задачи обычно создают отдельную ветку от свежего `main`.

```bash
git switch main
git pull
git switch -c feature/login
```

Так задача изолирована от основной ветки.

---

### Сделать изменения

В feature-ветке меняют код, конфиги, тесты или документацию.

Например:

```bash
vim app.py
vim README.md
```

---

### Закоммитить изменения

Изменения добавляют в staging и создают commit.

```bash
git add .
git commit -m "add login endpoint"
```

Лучше делать небольшие логичные commits.

---

### Запушить ветку

Локальную ветку нужно отправить в remote.

```bash
git push -u origin feature/login
```

После этого ветка появится на GitHub/GitLab.

---

### Открыть Pull Request

Pull Request — запрос на вливание изменений в основную ветку.

Обычно:

```text
feature/login -> main
```

В PR видно diff, commits, проверки и комментарии.

---

### Code review

Code review — проверка изменений другими разработчиками.

Смотрят:

- качество кода;
    
- баги;
    
- безопасность;
    
- читаемость;
    
- влияние на инфраструктуру.
    

---

### Approve PR

Approve — ревьюер одобрил изменения.

После approve PR обычно можно merge-ить, если прошли проверки.

---

### Request changes

Request changes — ревьюер просит что-то исправить.

Например:

- изменить код;
    
- добавить тест;
    
- поправить конфиг;
    
- убрать secret.
    

---

### Обновить PR

Чтобы обновить PR, достаточно запушить новые commits в ту же ветку.

```bash
git add .
git commit -m "fix review comments"
git push
```

PR обновится автоматически.

---

### Merge PR

Merge PR — влить изменения из feature-ветки в `main`.

После merge изменения становятся частью основной ветки.

---

### Squash merge

Squash merge объединяет все commits из PR в один commit.

Полезно, если в ветке было много мелких commits.

```text
fix
wip
review fixes
```

Превращается в один нормальный commit.

---

### Rebase merge

Rebase merge переносит commits из PR поверх `main`.

История получается линейной, без merge commit.

---

### Удалить ветку после merge

После merge feature-ветку обычно удаляют.

Локально:

```bash
git branch -d feature/login
```

На remote:

```bash
git push origin --delete feature/login
```

---

## 22. GitHub / GitLab Flow

### Repository

Repository — проект с кодом и Git-историей.

В GitHub/GitLab внутри repo обычно есть:

- branches;
    
- pull/merge requests;
    
- issues;
    
- CI/CD;
    
- releases;
    
- settings.
    

---

### Pull Request

Pull Request — термин GitHub.

Это запрос на вливание изменений из одной ветки в другую.

Обычно:

```text
feature branch -> main
```

---

### Merge Request

Merge Request — термин GitLab.

По сути то же самое, что Pull Request в GitHub.

```text
feature branch -> main
```

---

### Review

Review — процесс проверки изменений.

Ревьюеры оставляют комментарии, approve или request changes.

---

### Approvals

Approvals — обязательные подтверждения от ревьюеров.

Например, для merge нужно минимум 1–2 approve.

---

### Branch protection

Branch protection — защита важных веток.

Например, нельзя напрямую push-ить в:

```text
main
master
release
```

Изменения идут только через PR/MR.

---

### Required status checks

Required status checks — обязательные проверки перед merge.

Например:

- tests;
    
- lint;
    
- security scan;
    
- build;
    
- terraform plan.
    

Если checks не прошли, merge запрещен.

---

### CI/CD pipeline на PR

Pipeline на PR автоматически проверяет изменения.

Для DevOps/SRE это может быть:

```text
terraform fmt
terraform validate
helm lint
kubeconform
docker build
```

---

### Merge в main

После review и успешных checks изменения вливаются в `main`.

Обычно `main` должен всегда оставаться рабочим и стабильным.

---

### Release из tag

Релиз часто создают из Git tag.

```bash
git tag -a v1.2.0 -m "release v1.2.0"
git push origin v1.2.0
```

CI/CD может автоматически собрать и задеплоить релиз.

---

### Hotfix flow

Hotfix flow — срочное исправление production-проблемы.

Обычно:

- создать hotfix-ветку;
    
- исправить баг;
    
- открыть PR/MR;
    
- быстро review;
    
- merge;
    
- release/deploy.
    

---

## 23. Detached HEAD

### Что такое detached HEAD

Detached HEAD — состояние, когда `HEAD` указывает не на ветку, а напрямую на commit.

Обычно:

```text
HEAD -> commit_hash
```

А не:

```text
HEAD -> main
```

---

### Как появляется detached HEAD

Часто появляется при checkout конкретного commit.

```bash
git checkout a1b2c3d
```

Git переходит в прошлое состояние проекта.

---

### Checkout конкретного commit

```bash
git checkout <commit_hash>
```

Или:

```bash
git switch --detach <commit_hash>
```

Это удобно, чтобы посмотреть старую версию кода.

---

### Работа в detached HEAD

В detached HEAD можно менять файлы и делать commits.

Но эти commits не будут привязаны к ветке.

Из-за этого их легко потерять.

---

### Сохранить изменения из detached HEAD

Чтобы не потерять изменения, нужно создать ветку.

```bash
git switch -c debug-old-version
```

Теперь commits будут сохранены в ветке.

---

### Создать ветку из detached HEAD

Если уже находишься в detached HEAD:

```bash
git switch -c new-branch
```

Это лучший способ сохранить работу.

---

### Выйти из detached HEAD

Можно просто переключиться на ветку.

```bash
git switch main
```

Если были незасейвленные commits без ветки, они могут стать потерянными.

---

### Почему detached HEAD может быть опасен

Опасность в том, что commits не привязаны к branch.

Если уйти на другую ветку, потом эти commits сложно найти без `reflog`.

---

## 24. Reflog

### Что такое reflog

Reflog — локальная история перемещений `HEAD`.

Он помнит, где ты был раньше.

Полезен для восстановления после ошибок.

---

### `git reflog`

Показывает историю перемещения `HEAD`.

```bash
git reflog
```

Там можно найти потерянные commits.

---

### История HEAD

Reflog хранит действия вроде:

- checkout;
    
- commit;
    
- reset;
    
- rebase;
    
- merge.
    

Пример:

```text
HEAD@{0}: reset: moving to HEAD~1
HEAD@{1}: commit: add nginx config
```

---

### Восстановить потерянный commit

Сначала найти commit:

```bash
git reflog
```

Потом создать ветку:

```bash
git switch -c recovered <commit_hash>
```

---

### Восстановиться после reset hard

Если сделал:

```bash
git reset --hard HEAD~1
```

Можно найти старый commit через:

```bash
git reflog
```

И вернуть:

```bash
git reset --hard <old_commit_hash>
```

---

### Восстановить удаленную ветку

Если ветка была удалена локально, можно найти ее последний commit в reflog.

```bash
git reflog
git switch -c restored-branch <commit_hash>
```

---

### Reflog vs log

`git log` показывает историю commits в текущей ветке.

`git reflog` показывает историю перемещений `HEAD`.

Reflog может видеть commits, которых уже нет в обычном log.

---

### Когда reflog полезен

Reflog полезен, если:

- потерял commit;
    
- сделал неправильный reset;
    
- удалил ветку;
    
- вышел из detached HEAD;
    
- сломал историю rebase.
    

---

## 25. Clean

### `git clean`

`git clean` удаляет untracked files.

То есть файлы, которые Git еще не отслеживает.

---

### Удалить untracked files

```bash
git clean -f
```

Удалит untracked files в текущей директории.

---

### Удалить untracked directories

```bash
git clean -fd
```

`-d` добавляет удаление директорий.

---

### Dry run clean

Dry run показывает, что будет удалено, но ничего не удаляет.

Это безопасная проверка перед clean.

---

### `git clean -n`

```bash
git clean -n
```

Показывает список файлов, которые будут удалены.

Лучше всегда сначала запускать так.

---

### `git clean -fd`

```bash
git clean -fd
```

Удаляет untracked files и directories.

Опасно, потому что файлы могут исчезнуть без восстановления через Git.

---

### Почему `git clean` опасен

Git не отслеживает untracked files.

Если удалить их через `git clean`, восстановить через Git обычно нельзя.

---

### Разница clean и restore

`git restore` отменяет изменения в tracked files.

`git clean` удаляет untracked files.

```text
restore -> tracked files
clean   -> untracked files
```

---

## 26. Submodules

### Что такое submodule

Submodule — Git-репозиторий внутри другого Git-репозитория.

Например, основной проект подключает отдельную библиотеку.

---

### `git submodule`

Команда для управления submodules.

```bash
git submodule
```

Показывает подключенные submodules.

---

### Добавить submodule

```bash
git submodule add git@github.com:user/lib.git libs/lib
```

Git добавит ссылку на внешний репозиторий.

---

### Clone repo с submodules

Клонировать сразу с submodules:

```bash
git clone --recurse-submodules git@github.com:user/repo.git
```

Если уже склонировал:

```bash
git submodule update --init --recursive
```

---

### Обновить submodules

```bash
git submodule update --remote
```

Обновляет submodule до более свежего commit из его remote.

---

### Удалить submodule

Удаление submodule обычно сложнее обычной папки.

Примерно:

- удалить запись из `.gitmodules`;
    
- удалить из `.git/config`;
    
- удалить папку;
    
- сделать `git rm`.
    

---

### Почему submodules сложные

Submodule хранит ссылку на конкретный commit другого repo.

Проблемы:

- легко забыть обновить;
    
- сложно новичкам;
    
- ломается clone без `--recursive`;
    
- отдельная история и отдельные ветки.
    

---

### Альтернативы submodules

Вместо submodules часто используют:

- package manager;
    
- vendoring;
    
- mono-repo;
    
- Git subtree;
    
- artifact registry.
    

---

## 27. Git Hooks

### Что такое Git hooks

Git hooks — скрипты, которые автоматически запускаются на Git-события.

Например, перед commit или push.

---

### `.git/hooks`

Hooks лежат в директории:

```bash
.git/hooks
```

Там есть примеры файлов:

```text
pre-commit.sample
commit-msg.sample
pre-push.sample
```

---

### pre-commit hook

`pre-commit` запускается перед созданием commit.

Можно проверять:

- форматирование;
    
- lint;
    
- secrets;
    
- тесты.
    

---

### commit-msg hook

`commit-msg` проверяет commit message.

Например, можно требовать формат:

```text
feat: add login
fix: correct timeout
```

---

### pre-push hook

`pre-push` запускается перед `git push`.

Можно не дать запушить код, если тесты или проверки не прошли.

---

### Client-side hooks

Client-side hooks работают локально у разработчика.

Например:

- pre-commit;
    
- commit-msg;
    
- pre-push.
    

Минус: их можно обойти.

---

### Server-side hooks

Server-side hooks работают на сервере.

Обычно применяются в GitLab/GitHub Enterprise/self-hosted Git.

Их сложнее обойти.

---

### Hooks для linting

Hook может запускать lint перед commit.

Например:

```bash
pre-commit run
```

Идея: не коммитить плохо отформатированный код.

---

### Hooks для secret checking

Hooks могут искать случайные secrets.

Например:

- tokens;
    
- private keys;
    
- passwords;
    
- `.env`.
    

Это снижает шанс утечки секретов в Git.

---

## 28. Git для DevOps/SRE

### Git для Infrastructure as Code

В DevOps/SRE Git часто хранит инфраструктуру как код.

Например:

- Terraform;
    
- Ansible;
    
- Kubernetes manifests;
    
- Helm charts;
    
- CI/CD configs.
    

---

### Terraform в Git

Terraform-код обычно хранится в Git.

```text
main.tf
variables.tf
outputs.tf
providers.tf
```

Через PR удобно ревьюить изменения инфраструктуры.

---

### Ansible в Git

Ansible playbooks и roles тоже хранят в Git.

```text
playbooks/
roles/
inventory/
group_vars/
```

Так можно отслеживать изменения конфигурации серверов.

---

### Kubernetes manifests в Git

Kubernetes YAML-манифесты часто хранят в Git.

```text
deployment.yaml
service.yaml
ingress.yaml
configmap.yaml
```

Это база для GitOps-подхода.

---

### Helm charts в Git

Helm charts можно хранить в отдельном repo или рядом с приложением.

```text
Chart.yaml
values.yaml
templates/
```

Через PR удобно проверять изменения деплоя.

---

### CI/CD configs в Git

CI/CD pipeline обычно описан прямо в репозитории.

Например:

```text
.github/workflows/
.gitlab-ci.yml
Jenkinsfile
```

Изменения pipeline тоже проходят review.

---

### GitOps

GitOps — подход, где Git является источником правды для инфраструктуры и деплоя.

Изменил Git → система применяет изменения в cluster/environment.

Примеры инструментов:

- Argo CD;
    
- Flux.
    

---

### Rollback через Git

Rollback можно сделать откатом commit или возвратом старой версии манифеста.

Например:

```bash
git revert <bad_commit>
```

После merge GitOps-система вернет инфраструктуру к нужному состоянию.

---

### Audit changes через Git

Git дает audit trail.

Можно посмотреть:

- кто изменил;
    
- когда изменил;
    
- что именно изменил;
    
- через какой PR.
    

Для инфраструктуры это очень важно.

---

### Review infrastructure changes

Infrastructure changes нужно ревьюить так же серьезно, как код.

Особенно:

- security groups;
    
- IAM;
    
- Kubernetes RBAC;
    
- Terraform destroy;
    
- database changes;
    
- production configs.
    

---

## 29. Common Problems

### Случайно закоммитил не тот файл

Если commit еще локальный:

```bash
git reset --soft HEAD~1
git restore --staged wrong-file.txt
git commit -m "correct commit"
```

Если файл вообще не нужен в Git — добавь его в `.gitignore`.

---

### Случайно закоммитил secret

Нужно:

- удалить secret из кода;
    
- отозвать/заменить secret;
    
- почистить историю, если нужно;
    
- проверить доступы.
    

Просто удалить файл новым commit недостаточно.

---

### Нужно отменить последний commit

Если оставить изменения:

```bash
git reset --soft HEAD~1
```

Если удалить изменения полностью:

```bash
git reset --hard HEAD~1
```

---

### Нужно отменить pushed commit

В общей ветке лучше:

```bash
git revert <commit_hash>
git push
```

Так история не ломается у других людей.

---

### Нужно удалить локальные изменения

Для tracked files:

```bash
git restore .
```

Для untracked files сначала проверить:

```bash
git clean -n
```

Потом удалить:

```bash
git clean -fd
```

---

### Нужно убрать файл из staging

```bash
git restore --staged file.txt
```

Изменения останутся в файле, но не попадут в commit.

---

### Нужно решить merge conflict

Открыть файл, выбрать правильную версию, удалить маркеры:

```text
<<<<<<<
=======
>>>>>>>
```

Потом:

```bash
git add file.txt
git merge --continue
```

Или при rebase:

```bash
git rebase --continue
```

---

### Push rejected

Часто значит, что remote-ветка ушла вперед.

Обычно:

```bash
git pull --rebase
git push
```

Force push делать только если точно понимаешь последствия.

---

### Non-fast-forward error

Git не может просто передвинуть remote-ветку вперед.

Причина: локальная и удаленная истории разошлись.

Обычно решение:

```bash
git fetch
git rebase origin/main
git push
```

---

### Detached HEAD

Ты находишься не на ветке, а на конкретном commit.

Чтобы сохранить работу:

```bash
git switch -c new-branch
```

---

### Потерянный commit

Ищи через reflog:

```bash
git reflog
```

Потом восстанови ветку:

```bash
git switch -c recovered <commit_hash>
```

---

### Не та ветка

Сначала проверь:

```bash
git branch
git status
```

Если изменения еще не закоммичены, можно использовать stash:

```bash
git stash
git switch correct-branch
git stash pop
```

---

### Локальная ветка отстает от remote

Обновить ветку:

```bash
git pull
```

Или аккуратнее:

```bash
git pull --rebase
```

---

### Локальная ветка разошлась с remote

Значит есть разные commits локально и на remote.

Обычно:

```bash
git fetch
git rebase origin/branch-name
```

Потом:

```bash
git push
```

---

### Нужно синхронизировать feature branch с main

Через merge:

```bash
git switch feature/login
git merge main
```

Через rebase:

```bash
git switch feature/login
git rebase main
```

Rebase дает более линейную историю.

---

## 30. Interview Questions

### Что такое Git

Git — распределенная система контроля версий.

Нужен для хранения истории изменений и командной работы с кодом.

---

### Git vs GitHub

Git — инструмент.

GitHub — сервис для хранения Git-репозиториев и работы через PR, issues, CI/CD.

---

### Что такое commit

Commit — сохраненное состояние проекта.

Имеет hash, автора, дату и message.

---

### Что такое branch

Branch — отдельная линия разработки.

Позволяет работать над задачей отдельно от основной ветки.

---

### Что такое HEAD

HEAD — указатель на текущий commit.

Обычно указывает на вершину текущей ветки.

---

### Что такое staging area

Staging area — промежуточная область перед commit.

В commit попадет только то, что добавлено через `git add`.

---

### Что делает `git add`

`git add` добавляет изменения в staging area.

```bash
git add file.txt
```

---

### Что делает `git commit`

`git commit` создает новый commit из staged changes.

```bash
git commit -m "message"
```

---

### Что делает `git push`

`git push` отправляет локальные commits в remote repository.

```bash
git push origin main
```

---

### Что делает `git pull`

`git pull` забирает изменения с remote и применяет их локально.

Обычно это:

```text
fetch + merge
```

---

### Fetch vs pull

`fetch` только скачивает изменения.

`pull` скачивает и сразу применяет.

---

### Merge vs rebase

`merge` объединяет ветки и сохраняет историю.

`rebase` переносит commits на новую базу и переписывает историю.

---

### Reset vs revert

`reset` перемещает HEAD и может переписать историю.

`revert` создает новый commit, который отменяет старый.

Для shared branches безопаснее `revert`.

---

### Что такое merge conflict

Merge conflict — ситуация, когда Git не может автоматически объединить изменения.

Например, две ветки изменили одну строку.

---

### Как решить conflict

Нужно открыть файл, выбрать нужную версию, удалить conflict markers и сделать:

```bash
git add file.txt
```

Потом продолжить merge/rebase.

---

### Что такое stash

Stash — временное хранилище незакоммиченных изменений.

```bash
git stash
git stash pop
```

Полезно при переключении веток.

 

### Что такое cherry-pick

Cherry-pick — применение одного конкретного commit из другой ветки.

```bash
git cherry-pick <commit_hash>
```

---

### Что такое detached HEAD

Detached HEAD — состояние, когда HEAD указывает напрямую на commit, а не на ветку.

Чтобы сохранить работу:

```bash
git switch -c new-branch
```

---

### Что такое `.gitignore`

`.gitignore` — файл со списком файлов и директорий, которые Git должен игнорировать.

Например:

```text
.env
*.log
node_modules/
```

---

### Что делать, если secret был закоммичен

Нужно:

- удалить secret из кода;
    
- отозвать/заменить secret;
    
- при необходимости почистить Git history;
    
- добавить secret-файлы в `.gitignore`;
    
- проверить доступы.
    

---

### Что такое tag

Tag — метка на конкретный commit.

Часто используется для релизов.

```bash
git tag -a v1.0.0 -m "release v1.0.0"
```

---

### Что такое reflog

Reflog — локальная история перемещений HEAD.

Помогает восстановить потерянные commits после reset, rebase или detached HEAD.

```bash
git reflog
```