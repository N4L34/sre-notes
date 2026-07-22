[[Git. Конспект - черновик]]

## 1. Основы

- [x] Что такое Git
- [x] Git vs GitHub
- [x] Система контроля версий
- [x] Распределенная система контроля версий
- [x] Репозиторий
- [x] Локальный репозиторий
- [x] Удаленный репозиторий
- [x] Рабочая директория
- [x] Staging area / Index
- [x] Директория `.git`
- [x] Коммит
- [x] Хэш коммита
- [x] Ветка
- [x] HEAD
- [x] Ветка main / master

---

## 2. Настройка Git

- [x] `git config`
- [x] Глобальный config
- [x] Локальный config
- [x] `user.name`
- [x] `user.email`
- [x] Редактор по умолчанию
- [x] Имя ветки по умолчанию
- [x] Credential helper
- [x] SSH-ключ для Git
- [x] HTTPS vs SSH авторизация

---

## 3. Создание репозитория

- [x] `git init`
- [x] `git clone`
- [x] Клонирование через HTTPS
- [x] Клонирование через SSH
- [x] `git remote -v`
- [x] `origin`
- [x] Добавить удаленный репозиторий
- [x] Изменить remote URL
- [x] Удалить remote repository

---

## 4. Базовый workflow

- [x] `git status`
- [x] `git add`
- [x] `git add .`
- [x] `git commit`
- [x] `git commit -m`
- [x] `git push`
- [x] `git pull`
- [x] `git fetch`
- [x] `git log`
- [x] `git diff`

---

## 5. Staging Area

- [x] Что такое staging area
- [x] Добавить файл в staging
- [x] Добавить все изменения в staging
- [x] Убрать файл из staging
- [x] Staged changes
- [x] Unstaged changes
- [x] Tracked files
- [x] Untracked files
- [x] Частично добавить изменения в staging
- [x] Почему commit включает только staged changes

---

## 6. Коммиты

- [x] Что такое commit
- [x] Хэш коммита
- [x] Commit message
- [x] Atomic commit
- [x] Хороший commit message
- [x] Плохой commit message
- [x] `git commit --amend`
- [x] Изменить последний commit message
- [x] Добавить забытый файл в последний commit
- [x] Пустой commit

---

## 7. Просмотр истории

- [x] `git log`  
- [x] `git log --oneline`
- [x] `git log --graph`
- [x] `git show`
- [x] `git diff`
- [x] `git diff --staged`
- [x] `git blame`
- [x] Показать изменения в конкретном commit
- [x] Показать изменения между ветками
- [x] Показать изменения между commit

---

## 8. Ветки

- [x] Что такое branch
- [x] `main`
- [x] `master`
- [x] Feature branch
- [x] Hotfix branch
- [x] Release branch
- [x] `git branch`
- [x] `git branch -a`
- [x] `git switch`
- [x] `git checkout`
- [x] Создать новую ветку
- [x] Переключиться на ветку
- [x] Удалить локальную ветку
- [x] Удалить удаленную ветку
- [x] Переименовать ветку

---

## 9. Merge

- [x] Что такое merge
- [x] `git merge`
- [x] Fast-forward merge
- [x] 3-way merge
- [x] Merge commit
- [x] Влить feature branch в main
- [x] Влить main в feature branch
- [x] Отменить merge
- [x] Merge conflict при merge
- [x] Когда использовать merge

---

## 10. Rebase

- [x] Что такое rebase
- [x] `git rebase`
- [x] Rebase feature branch на main
- [x] Rebase vs merge
- [x] Interactive rebase
- [x] `git rebase -i`
- [x] Squash commits
- [x] Reword commit
- [x] Drop commit
- [x] Изменить порядок commits
- [x] Отменить rebase
- [x] Продолжить rebase
- [x] Почему нельзя rebase-ить shared branches

---

## 11. Merge Conflicts

- [x] Что такое merge conflict
- [x] Почему появляются merge conflicts
- [x] Как conflict выглядит в файле
- [x] `<<<<<<< HEAD`
- [x] `=======`
- [x] `>>>>>>> branch`
- [x] Решить conflict вручную
- [x] `git status` во время conflict
- [x] `git add` после решения conflict
- [x] `git merge --abort`
- [x] `git rebase --abort`
- [x] `git rebase --continue`

---

## 12. Remote Repositories

- [x] Что такое remote repository
- [x] `origin`
- [x] `git remote`
- [x] `git remote -v`
- [x] `git remote add`
- [x] `git remote remove`
- [x] `git remote set-url`
- [x] Remote branch
- [x] Tracking branch
- [x] Upstream branch
- [x] `git push -u origin branch`

---

## 13. Fetch / Pull / Push

- [x] `git fetch`
- [x] `git pull`
- [x] `git pull --rebase`
- [x] `git push`
- [x] `git push -u`
- [x] Fetch vs pull
- [x] Pull = fetch + merge/rebase
- [x] Push локальной ветки в remote
- [x] Push tags
- [x] Push rejected
- [x] Non-fast-forward error

---

## 14. Force Push

- [x] Что такое force push
- [x] `git push --force`
- [x] `git push --force-with-lease`
- [x] Force push после rebase
- [x] Почему force push опасен
- [x] Почему `--force-with-lease` безопаснее
- [x] Когда force push допустим
- [x] Когда force push запрещен

---

## 15. Restore / Reset / Revert

- [x] `git restore`
- [x] `git restore file`
- [x] `git restore --staged`
- [x] `git reset`
- [x] `git reset --soft`
- [x] `git reset --mixed`
- [x] `git reset --hard`
- [x] `git revert`
- [x] Reset vs revert
- [x] Отменить локальные изменения
- [x] Отменить staged changes
- [x] Отменить последний commit
- [x] Отменить pushed commit
- [x] Безопасный rollback в общей ветке
 
---

## 16. Stash

- [x] Что такое stash
- [x] `git stash`
- [x] `git stash list`
- [x] `git stash pop`
- [x] `git stash apply`
- [x] `git stash drop`
- [x] `git stash clear`
- [x] Stash с сообщением
- [x] Stash untracked files
- [x] Conflict после stash pop
- [x] Когда использовать stash

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

- [x] Что такое tag
- [x] Lightweight tag
- [x] Annotated tag
- [x] Version tag
- [x] `git tag`
- [x] Создать tag
- [x] Push tag
- [ ] Push всех tags
- [x] Удалить локальный tag
- [x] Удалить remote tag
- [x] Tags для releases

---

## 19. `.gitignore`

- [x] Что такое `.gitignore`
- [x] Игнорировать файлы
- [x] Игнорировать директории
- [x] Игнорировать логи
- [x] Игнорировать build artifacts
- [x] Игнорировать `.env`
- [x] Игнорировать файлы IDE
- [x] Файл уже отслеживается Git
- [x] `git rm --cached`
- [x] Глобальный `.gitignore`
- [x] Почему `.gitignore` не удаляет файл из истории

---

## 20. Secrets

- [x] Почему нельзя коммитить secrets
- [x] Пароль в Git
- [x] Token в Git
- [x] Private key в Git
- [x] `.env` в Git
- [x] Secret случайно закоммичен
- [x] Удалить secret из текущей ветки
- [x] Удалить secret из Git history
- [x] Rotate leaked secret
- [x] Secret scanning
- [x] `.gitignore` для secrets

---

## 21. Pull Request Flow

- [x] Создать ветку от main
- [x] Сделать изменения
- [x] Закоммитить изменения
- [x] Запушить ветку
- [x] Открыть Pull Request
- [x] Code review
- [x] Approve PR
- [x] Request changes
- [x] Обновить PR
- [x] Merge PR
- [x] Squash merge
- [x] Rebase merge
- [x] Удалить ветку после merge

---

## 22. GitHub / GitLab Flow

- [x] Repository
- [x] Pull Request
- [x] Merge Request
- [x] Review
- [x] Approvals
- [x] Branch protection
- [x] Required status checks
- [x] CI/CD pipeline на PR
- [x] Merge в main
- [x] Release из tag
- [x] Hotfix flow

---

## 23. Detached HEAD

- [x] Что такое detached HEAD
- [x] Как появляется detached HEAD
- [x] Checkout конкретного commit
- [x] Работа в detached HEAD
- [x] Сохранить изменения из detached HEAD
- [x] Создать ветку из detached HEAD
- [x] Выйти из detached HEAD
- [x] Почему detached HEAD может быть опасен

---

## 24. Reflog

- [x] Что такое reflog
- [x] `git reflog`
- [x] История HEAD
- [x] Восстановить потерянный commit
- [x] Восстановиться после reset hard
- [x] Восстановить удаленную ветку
- [x] Reflog vs log
- [x] Когда reflog полезен

---

## 25. Clean

- [x] `git clean`
- [x] Удалить untracked files
- [x] Удалить untracked directories
- [x] Dry run clean
- [x] `git clean -n`
- [x] `git clean -fd`
- [x] Почему `git clean` опасен
- [x] Разница clean и restore

---

## 26. Submodules

- [x] Что такое submodule
- [x] `git submodule`
- [x] Добавить submodule
- [x] Clone repo с submodules
- [x] Обновить submodules
- [x] Удалить submodule
- [x] Почему submodules сложные
- [x] Альтернативы submodules

---

## 27. Git Hooks

- [x] Что такое Git hooks
- [x] `.git/hooks`
- [x] pre-commit hook
- [x] commit-msg hook
- [x] pre-push hook
- [x] Client-side hooks
- [x] Server-side hooks
- [x] Hooks для linting
- [x] Hooks для secret checking

---

## 28. Git для DevOps/SRE

- [x] Git для Infrastructure as Code
- [x] Terraform в Git
- [x] Ansible в Git
- [x] Kubernetes manifests в Git
- [x] Helm charts в Git
- [x] CI/CD configs в Git
- [x] GitOps
- [x] Rollback через Git
- [x] Audit changes через Git
- [x] Review infrastructure changes

---

## 29. Common Problems

- [x] Случайно закоммитил не тот файл
- [x] Случайно закоммитил secret
- [x] Нужно отменить последний commit
- [x] Нужно отменить pushed commit
- [x] Нужно удалить локальные изменения
- [x] Нужно убрать файл из staging
- [x] Нужно решить merge conflict
- [x] Push rejected
- [x] Non-fast-forward error
- [x] Detached HEAD
- [x] Потерянный commit
- [x] Не та ветка
- [x] Локальная ветка отстает от remote
- [x] Локальная ветка разошлась с remote
- [x] Нужно синхронизировать feature branch с main

---

## 30. Interview Questions

- [x] Что такое Git
- [x] Git vs GitHub
- [x] Что такое commit
- [x] Что такое branch
- [x] Что такое HEAD
- [x] Что такое staging area
- [x] Что делает `git add`
- [x] Что делает `git commit`
- [x] Что делает `git push`
- [x] Что делает `git pull`
- [x] Fetch vs pull
- [x] Merge vs rebase
- [x] Reset vs revert
- [x] Что такое merge conflict
- [x] Как решить conflict
- [x] Что такое stash
- [x] Что такое cherry-pick
- [x] Что такое detached HEAD
- [x] Что такое `.gitignore`
- [x] Что делать, если secret был закоммичен
- [x] Что такое tag
- [x] Что такое reflog