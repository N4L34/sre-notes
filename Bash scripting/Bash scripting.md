[[Bash scripting. Конспект]]

# Bash Scripting

## 1. Основы

- [x] Что такое Bash
- [x] Что такое shell
- [x] Bash vs sh
- [x] Скрипт
- [x] Shebang
- [x] Как запустить скрипт
- [x] Права на выполнение
- [x] `chmod +x`
- [x] `./script.sh`

---

## 2. Переменные

- [x] Переменные
- [x] Присваивание переменной
- [x] Использование переменной
- [x] Кавычки
- [x] Одинарные кавычки
- [x] Двойные кавычки
- [x] Environment variables
- [x] `export`
- [x] `unset`

---

## 3. Аргументы скрипта

- [x] `$0`
- [x] `$1`, `$2`, `$3`
- [x] `$#`
- [x] `$@`
- [x] `$*`
- [x] `shift`
- [x] Проверка количества аргументов

---

## 4. Exit Codes

- [x] Что такое exit code
- [x] `0`
- [x] Не `0`
- [x] `$?`
- [x] `exit`
- [x] `exit 1`
- [x] Проверка успешности команды

---

## 5. Conditions

- [x] `if`
- [x] `else`
- [x] `elif`
- [x] `test`
- [x] `[ ]`
- [x] `[[ ]]`
- [x] `-eq`
- [x] `-ne`
- [x] `-gt`
- [x] `-lt`
- [x] `=`
- [x] `!=`
- [x] `-z`
- [x] `-n`

---

## 6. Файловые проверки

- [x] `-f`
- [x] `-d`
- [x] `-e`
- [x] `-r`
- [x] `-w`
- [x] `-x`
- [x] Проверить, существует ли файл
- [x] Проверить, существует ли директория
- [x] Проверить права на файл

---

## 7. Циклы

- [x] `for`
- [x] `while`
- [x] `until`
- [x] `break`
- [x] `continue`
- [x] Цикл по файлам
- [x] Цикл по строкам файла
- [x] Бесконечный цикл

---

## 8. Ввод и вывод

- [x] `echo`
- [x] `printf`
- [x] `read`
- [x] stdin
- [x] stdout
- [x] stderr
- [x] Redirect
- [x] `>`
- [x] `>>`
- [x] `2>`
- [x] `2>&1`
- [x] `/dev/null`

---

## 9. Pipes

- [x] Pipe
- [x] `|`
- [x] `grep`
- [x] `awk`
- [x] `sed`
- [x] `cut`
- [x] `sort`
- [x] `uniq`
- [x] `wc`
- [x] `xargs`

---

## 10. Functions

- [x] Что такое function
- [x] Объявление function
- [x] Аргументы function
- [x] Return code function
- [x] Локальные переменные
- [x] `local`
- [x] Переиспользование кода

---

## 11. Arrays

- [x] Что такое array
- [x] Создать array
- [x] Получить элемент array
- [x] Все элементы array
- [x] Длина array
- [x] Цикл по array
- [x] Associative array basics

---

## 12. Command Substitution

- [x] `$(command)`
- [x] Backticks
- [x] Сохранить вывод команды в переменную
- [x] Вложенные команды
- [x] Почему `$(...)` лучше backticks

---

## 13. Arithmetic

- [x] `$(( ))`
- [x] `let`
- [x] `expr`
- [x] Инкремент
- [x] Декремент
- [x] Сравнение чисел
- [x] Bash не для сложной математики

---

## 14. Globs

- [x] `*`
- [x] `?`
- [x] `[]`
- [x] Brace expansion
- [x] `{}`
- [x] Path expansion
- [x] Wildcards
- [x] Опасность `rm -rf *`

---

## 15. Quoting

- [x] Почему кавычки важны
- [x] Word splitting
- [x] Globbing
- [x] `"${var}"`
- [x] `'$var'`
- [x] Экранирование
- [x] `\`
- [x] Пробелы в именах файлов

---

## 16. Error Handling

- [x] `set -e`
- [x] `set -u`
- [x] `set -o pipefail`
- [x] `set -x`
- [x] `trap`
- [x] Обработка ошибок
- [x] Проверка команд
- [x] Cleanup при ошибке

---

## 17. Signals

- [x] Что такое signal
- [x] `SIGINT`
- [x] `SIGTERM`
- [x] `SIGHUP`
- [x] `trap`
- [x] Cleanup on exit
- [x] Graceful shutdown

---

## 18. Работа с файлами

- [x] Читать файл
- [x] Читать файл построчно
- [x] Записать в файл
- [x] Добавить в файл
- [x] Проверить пустой файл
- [x] Найти файлы
- [x] Удалить старые файлы
- [x] Работа с temp files

---

## 19. Работа с процессами

- [x] `ps`
- [x] `pgrep`
- [x] `pkill`
- [x] `kill`
- [x] `wait`
- [x] Background process
- [x] `&`
- [x] PID
- [x] `$!`

---

## 20. Практика для DevOps/SRE

- [x] Backup script
- [x] Log cleanup script
- [x] Healthcheck script
- [x] Service restart script
- [x] Deploy script
- [x] Cron script
- [x] Disk usage alert
- [x] Process check
- [x] HTTP check
- [x] JSON parsing basics

---

## 21. Частые ошибки

- [x] Нет shebang
- [x] Нет execute permissions
- [x] Пробелы вокруг `=`
- [x] Нет кавычек вокруг переменных
- [x] Неправильный exit code
- [x] Скрипт работает руками, но не в cron
- [x] Относительные пути
- [x] Bash-specific код в sh
- [x] Потеря ошибок в pipe
- [x] Опасный `rm`

---

## 22. Interview Questions

- [x] Что такое shebang
- [x] Как передать аргументы в script
- [x] Что такое `$?`
- [x] Что такое `$@`
- [x] Разница `$@` и `$*`
- [x] Разница `[ ]` и `[[ ]]`
- [x] Что делает `set -e`
- [x] Что делает `set -u`
- [x] Что делает `pipefail`
- [x] Что такое trap
- [x] Как читать файл построчно
- [x] Как проверить существование файла
- [x] Как запустить процесс в background
- [x] Как получить PID последнего background process
- [x] Почему нужны кавычки вокруг переменных