[[Git. Конспект - черновик]]

## 1. Основы

- [ ] Что такое Git
- [ ] Git vs GitHub
- [ ] Система контроля версий
- [ ] Распределенная система контроля версий
- [ ] Репозиторий
- [ ] Локальный репозиторий
- [ ] Удаленный репозиторий
- [ ] Рабочая директория
- [ ] Staging area / Index
- [ ] Директория `.git`
- [ ] Коммит
- [ ] Хэш коммита
- [ ] Ветка
- [ ] HEAD
- [ ] Ветка main / master

---

## 2. Настройка Git

- [ ] `git config`
- [ ] Глобальный config
- [ ] Локальный config
- [ ] `user.name`
- [ ] `user.email`
- [ ] Редактор по умолчанию
- [ ] Имя ветки по умолчанию
- [ ] Credential helper
- [ ] SSH-ключ для Git
- [ ] HTTPS vs SSH авторизация

---

## 3. Создание репозитория

- [ ] `git init`
- [ ] `git clone`
- [ ] Клонирование через HTTPS
- [ ] Клонирование через SSH
- [ ] `git remote -v`
- [ ] `origin`
- [ ] Добавить удаленный репозиторий
- [ ] Изменить remote URL
- [ ] Удалить remote repository

---

## 4. Базовый workflow

- [ ] `git status`
- [ ] `git add`
- [ ] `git add .`
- [ ] `git commit`
- [ ] `git commit -m`
- [ ] `git push`
- [ ] `git pull`
- [ ] `git fetch`
- [ ] `git log`
- [ ] `git diff`

---

## 5. Staging Area

- [ ] Что такое staging area
- [ ] Добавить файл в staging
- [ ] Добавить все изменения в staging
- [ ] Убрать файл из staging
- [ ] Staged changes
- [ ] Unstaged changes
- [ ] Tracked files
- [ ] Untracked files
- [ ] Частично добавить изменения в staging
- [ ] Почему commit включает только staged changes

---

## 6. Коммиты

- [ ] Что такое commit
- [ ] Хэш коммита
- [ ] Commit message
- [ ] Atomic commit
- [ ] Хороший commit message
- [ ] Плохой commit message
- [ ] `git commit --amend`
- [ ] Изменить последний commit message
- [ ] Добавить забытый файл в последний commit
- [ ] Пустой commit

---

## 7. Просмотр истории

- [ ] `git log`
- [ ] `git log --oneline`
- [ ] `git log --graph`
- [ ] `git show`
- [ ] `git diff`
- [ ] `git diff --staged`
- [ ] `git blame`
- [ ] Показать изменения в конкретном commit
- [ ] Показать изменения между ветками
- [ ] Показать изменения между commit

---

## 8. Ветки

- [ ] Что такое branch
- [ ] `main`
- [ ] `master`
- [ ] Feature branch
- [ ] Hotfix branch
- [ ] Release branch
- [ ] `git branch`
- [ ] `git branch -a`
- [ ] `git switch`
- [ ] `git checkout`
- [ ] Создать новую ветку
- [ ] Переключиться на ветку
- [ ] Удалить локальную ветку
- [ ] Удалить удаленную ветку
- [ ] Переименовать ветку

---

## 9. Merge

- [ ] Что такое merge
- [ ] `git merge`
- [ ] Fast-forward merge
- [ ] 3-way merge
- [ ] Merge commit
- [ ] Влить feature branch в main
- [ ] Влить main в feature branch
- [ ] Отменить merge
- [ ] Merge conflict при merge
- [ ] Когда использовать merge

---

## 10. Rebase

- [ ] Что такое rebase
- [ ] `git rebase`
- [ ] Rebase feature branch на main
- [ ] Rebase vs merge
- [ ] Interactive rebase
- [ ] `git rebase -i`
- [ ] Squash commits
- [ ] Reword commit
- [ ] Drop commit
- [ ] Изменить порядок commits
- [ ] Отменить rebase
- [ ] Продолжить rebase
- [ ] Почему нельзя rebase-ить shared branches

---

## 11. Merge Conflicts

- [ ] Что такое merge conflict
- [ ] Почему появляются merge conflicts
- [ ] Как conflict выглядит в файле
- [ ] `<<<<<<< HEAD`
- [ ] `=======`
- [ ] `>>>>>>> branch`
- [ ] Решить conflict вручную
- [ ] `git status` во время conflict
- [ ] `git add` после решения conflict
- [ ] `git merge --abort`
- [ ] `git rebase --abort`
- [ ] `git rebase --continue`

---

## 12. Remote Repositories

- [ ] Что такое remote repository
- [ ] `origin`
- [ ] `git remote`
- [ ] `git remote -v`
- [ ] `git remote add`
- [ ] `git remote remove`
- [ ] `git remote set-url`
- [ ] Remote branch
- [ ] Tracking branch
- [ ] Upstream branch
- [ ] `git push -u origin branch`

---

## 13. Fetch / Pull / Push

- [ ] `git fetch`
- [ ] `git pull`
- [ ] `git pull --rebase`
- [ ] `git push`
- [ ] `git push -u`
- [ ] Fetch vs pull
- [ ] Pull = fetch + merge/rebase
- [ ] Push локальной ветки в remote
- [ ] Push tags
- [ ] Push rejected
- [ ] Non-fast-forward error

---

## 14. Force Push

- [ ] Что такое force push
- [ ] `git push --force`
- [ ] `git push --force-with-lease`
- [ ] Force push после rebase
- [ ] Почему force push опасен
- [ ] Почему `--force-with-lease` безопаснее
- [ ] Когда force push допустим
- [ ] Когда force push запрещен

---

## 15. Restore / Reset / Revert

- [ ] `git restore`
- [ ] `git restore file`
- [ ] `git restore --staged`
- [ ] `git reset`
- [ ] `git reset --soft`
- [ ] `git reset --mixed`
- [ ] `git reset --hard`
- [ ] `git revert`
- [ ] Reset vs revert
- [ ] Отменить локальные изменения
- [ ] Отменить staged changes
- [ ] Отменить последний commit
- [ ] Отменить pushed commit
- [ ] Безопасный rollback в общей ветке

---

## 16. Stash

- [ ] Что такое stash
- [ ] `git stash`
- [ ] `git stash list`
- [ ] `git stash pop`
- [ ] `git stash apply`
- [ ] `git stash drop`
- [ ] `git stash clear`
- [ ] Stash с сообщением
- [ ] Stash untracked files
- [ ] Conflict после stash pop
- [ ] Когда использовать stash

---

## 17. Cherry-pick

- [ ] Что такое cherry-pick
- [ ] `git cherry-pick`
- [ ] Cherry-pick конкретного commit
- [ ] Cherry-pick из другой ветки
- [ ] Cherry-pick conflict
- [ ] `git cherry-pick --abort`
- [ ] `git cherry-pick --continue`
- [ ] Когда использовать cherry-pick
- [ ] Когда не использовать cherry-pick

---

## 18. Tags

- [ ] Что такое tag
- [ ] Lightweight tag
- [ ] Annotated tag
- [ ] Version tag
- [ ] `git tag`
- [ ] Создать tag
- [ ] Push tag
- [ ] Push всех tags
- [ ] Удалить локальный tag
- [ ] Удалить remote tag
- [ ] Tags для releases

---

## 19. `.gitignore`

- [ ] Что такое `.gitignore`
- [ ] Игнорировать файлы
- [ ] Игнорировать директории
- [ ] Игнорировать логи
- [ ] Игнорировать build artifacts
- [ ] Игнорировать `.env`
- [ ] Игнорировать файлы IDE
- [ ] Файл уже отслеживается Git
- [ ] `git rm --cached`
- [ ] Глобальный `.gitignore`
- [ ] Почему `.gitignore` не удаляет файл из истории

---

## 20. Secrets

- [ ] Почему нельзя коммитить secrets
- [ ] Пароль в Git
- [ ] Token в Git
- [ ] Private key в Git
- [ ] `.env` в Git
- [ ] Secret случайно закоммичен
- [ ] Удалить secret из текущей ветки
- [ ] Удалить secret из Git history
- [ ] Rotate leaked secret
- [ ] Secret scanning
- [ ] `.gitignore` для secrets

---

## 21. Pull Request Flow

- [ ] Создать ветку от main
- [ ] Сделать изменения
- [ ] Закоммитить изменения
- [ ] Запушить ветку
- [ ] Открыть Pull Request
- [ ] Code review
- [ ] Approve PR
- [ ] Request changes
- [ ] Обновить PR
- [ ] Merge PR
- [ ] Squash merge
- [ ] Rebase merge
- [ ] Удалить ветку после merge

---

## 22. GitHub / GitLab Flow

- [ ] Repository
- [ ] Pull Request
- [ ] Merge Request
- [ ] Review
- [ ] Approvals
- [ ] Branch protection
- [ ] Required status checks
- [ ] CI/CD pipeline на PR
- [ ] Merge в main
- [ ] Release из tag
- [ ] Hotfix flow

---

## 23. Detached HEAD

- [ ] Что такое detached HEAD
- [ ] Как появляется detached HEAD
- [ ] Checkout конкретного commit
- [ ] Работа в detached HEAD
- [ ] Сохранить изменения из detached HEAD
- [ ] Создать ветку из detached HEAD
- [ ] Выйти из detached HEAD
- [ ] Почему detached HEAD может быть опасен

---

## 24. Reflog

- [ ] Что такое reflog
- [ ] `git reflog`
- [ ] История HEAD
- [ ] Восстановить потерянный commit
- [ ] Восстановиться после reset hard
- [ ] Восстановить удаленную ветку
- [ ] Reflog vs log
- [ ] Когда reflog полезен

---

## 25. Clean

- [ ] `git clean`
- [ ] Удалить untracked files
- [ ] Удалить untracked directories
- [ ] Dry run clean
- [ ] `git clean -n`
- [ ] `git clean -fd`
- [ ] Почему `git clean` опасен
- [ ] Разница clean и restore

---

## 26. Submodules

- [ ] Что такое submodule
- [ ] `git submodule`
- [ ] Добавить submodule
- [ ] Clone repo с submodules
- [ ] Обновить submodules
- [ ] Удалить submodule
- [ ] Почему submodules сложные
- [ ] Альтернативы submodules

---

## 27. Git Hooks

- [ ] Что такое Git hooks
- [ ] `.git/hooks`
- [ ] pre-commit hook
- [ ] commit-msg hook
- [ ] pre-push hook
- [ ] Client-side hooks
- [ ] Server-side hooks
- [ ] Hooks для linting
- [ ] Hooks для secret checking

---

## 28. Git для DevOps/SRE

- [ ] Git для Infrastructure as Code
- [ ] Terraform в Git
- [ ] Ansible в Git
- [ ] Kubernetes manifests в Git
- [ ] Helm charts в Git
- [ ] CI/CD configs в Git
- [ ] GitOps
- [ ] Rollback через Git
- [ ] Audit changes через Git
- [ ] Review infrastructure changes

---

## 29. Common Problems

- [ ] Случайно закоммитил не тот файл
- [ ] Случайно закоммитил secret
- [ ] Нужно отменить последний commit
- [ ] Нужно отменить pushed commit
- [ ] Нужно удалить локальные изменения
- [ ] Нужно убрать файл из staging
- [ ] Нужно решить merge conflict
- [ ] Push rejected
- [ ] Non-fast-forward error
- [ ] Detached HEAD
- [ ] Потерянный commit
- [ ] Не та ветка
- [ ] Локальная ветка отстает от remote
- [ ] Локальная ветка разошлась с remote
- [ ] Нужно синхронизировать feature branch с main

---

## 30. Interview Questions

- [ ] Что такое Git
- [ ] Git vs GitHub
- [ ] Что такое commit
- [ ] Что такое branch
- [ ] Что такое HEAD
- [ ] Что такое staging area
- [ ] Что делает `git add`
- [ ] Что делает `git commit`
- [ ] Что делает `git push`
- [ ] Что делает `git pull`
- [ ] Fetch vs pull
- [ ] Merge vs rebase
- [ ] Reset vs revert
- [ ] Что такое merge conflict
- [ ] Как решить conflict
- [ ] Что такое stash
- [ ] Что такое cherry-pick
- [ ] Что такое detached HEAD
- [ ] Что такое `.gitignore`
- [ ] Что делать, если secret был закоммичен
- [ ] Что такое tag
- [ ] Что такое reflog