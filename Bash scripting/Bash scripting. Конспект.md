## 1. Основы

### Что такое Bash

Bash — командная оболочка и язык скриптов в Linux.

Через Bash запускают команды, пишут скрипты и автоматизируют задачи.

---

### Что такое shell

Shell — программа, которая принимает команды пользователя и передает их системе.

Примеры shell:

```bash
bash
sh
zsh
fish
```

---

### Bash vs sh

`sh` — более старый и минимальный shell.

`bash` — более мощный shell с дополнительными возможностями.

```bash
#!/bin/sh
#!/bin/bash
```

Для простых скриптов можно `sh`, для удобных Bash-фич — `bash`.

---

### Скрипт

Скрипт — файл с набором команд.

Например:

```bash
echo "Hello"
date
whoami
```

Скрипты используют для автоматизации.

---

### Shebang

Shebang указывает, каким интерпретатором запускать скрипт.

```bash
#!/bin/bash
```

Обычно пишется первой строкой файла.

---

### Как запустить скрипт

Можно запустить через Bash:

```bash
bash script.sh
```

Или напрямую:

```bash
./script.sh
```

---

### Права на выполнение

Чтобы запускать скрипт как программу, нужны execute-права.

Проверить:

```bash
ls -l script.sh
```

---

### `chmod +x`

Добавляет право на выполнение файла.

```bash
chmod +x script.sh
```

После этого скрипт можно запускать через `./script.sh`.

---

### `./script.sh`

Запуск скрипта из текущей директории.

```bash
./script.sh
```

`./` нужно, потому что текущая директория обычно не входит в `$PATH`.

---

## 2. Переменные

### Переменные

Переменная хранит значение.

```bash
name="Ivan"
```

Важно: вокруг `=` не должно быть пробелов.

---

### Присваивание переменной

Правильно:

```bash
user="admin"
```

Неправильно:

```bash
user = "admin"
```

Bash воспримет это как команду.

---

### Использование переменной

Чтобы получить значение переменной, используется `$`.

```bash
name="Ivan"
echo "$name"
```

---

### Кавычки

Кавычки управляют тем, как Bash обрабатывает текст и переменные.

Лучше почти всегда использовать двойные кавычки:

```bash
echo "$file"
```

---

### Одинарные кавычки

Одинарные кавычки не раскрывают переменные.

```bash
name="Ivan"
echo '$name'
```

Выведет:

```text
$name
```

---

### Двойные кавычки

Двойные кавычки раскрывают переменные.

```bash
name="Ivan"
echo "$name"
```

Выведет:

```text
Ivan
```

---

### Environment variables

Environment variables — переменные окружения, доступные процессам.

Примеры:

```bash
PATH
HOME
USER
SHELL
```

Посмотреть:

```bash
env
```

---

### `export`

`export` делает переменную доступной дочерним процессам.

```bash
export APP_ENV=prod
```

После этого переменную увидит запущенный из shell процесс.

---

### `unset`

`unset` удаляет переменную.

```bash
unset APP_ENV
```

После этого переменная больше не задана.

---

## 3. Аргументы скрипта

### `$0`

`$0` — имя скрипта.

```bash
echo "$0"
```

Если запустить `./backup.sh`, `$0` будет примерно `./backup.sh`.

---

### `$1`, `$2`, `$3`

Позиционные аргументы скрипта.

```bash
./script.sh one two three
```

```bash
$1 = one
$2 = two
$3 = three
```

---

### `$#`

`$#` — количество переданных аргументов.

```bash
echo "$#"
```

Полезно для проверки, передал ли пользователь нужные параметры.

---

### `$@`

`$@` — все аргументы скрипта.

Обычно правильно использовать так:

```bash
for arg in "$@"; do
  echo "$arg"
done
```

Каждый аргумент сохраняется отдельно.

---

### `$*`

`$*` — тоже все аргументы, но обрабатываются менее безопасно.

Чаще в скриптах используют `"$@"`.

---

### `shift`

`shift` сдвигает аргументы влево.

```bash
echo "$1"
shift
echo "$1"
```

После `shift` бывший `$2` станет `$1`.

---

### Проверка количества аргументов

Пример проверки:

```bash
if [ "$#" -ne 2 ]; then
  echo "Usage: $0 source destination"
  exit 1
fi
```

Полезно для CLI-скриптов.

---

## 4. Exit Codes

### Что такое exit code

Exit code — код завершения команды.

По нему понятно, успешно ли выполнилась команда.

---

### `0`

Exit code `0` означает успех.

```bash
echo "ok"
echo "$?"
```

---

### Не `0`

Любой код не `0` обычно означает ошибку.

Например:

```bash
ls /not-exist
echo "$?"
```

---

### `$?`

`$?` хранит exit code последней команды.

```bash
systemctl status nginx
echo "$?"
```

Важно проверять сразу после команды.

---

### `exit`

`exit` завершает скрипт.

```bash
exit
```

Можно указать код завершения.

---

### `exit 1`

`exit 1` завершает скрипт с ошибкой.

```bash
echo "Error"
exit 1
```

Часто используется при неправильных аргументах или ошибках.

---

### Проверка успешности команды

Пример:

```bash
if systemctl is-active --quiet nginx; then
  echo "nginx is running"
else
  echo "nginx is down"
fi
```

Команды в `if` проверяются по exit code.

---

## 5. Conditions

### `if`

`if` выполняет код, если условие истинно.

```bash
if [ "$USER" = "root" ]; then
  echo "root user"
fi
```

---

### `else`

`else` выполняется, если условие `if` ложно.

```bash
if [ -f file.txt ]; then
  echo "exists"
else
  echo "not found"
fi
```

---

### `elif`

`elif` — дополнительное условие.

```bash
if [ "$env" = "prod" ]; then
  echo "production"
elif [ "$env" = "dev" ]; then
  echo "development"
else
  echo "unknown"
fi
```

---

### `test`

`test` проверяет условие.

```bash
test -f file.txt
```

То же самое, что:

```bash
[ -f file.txt ]
```

---

### `[ ]`

Классический синтаксис условий.

```bash
if [ "$name" = "Ivan" ]; then
  echo "yes"
fi
```

Важно: пробелы внутри `[ ]` обязательны.

---

### `[[ ]]`

Расширенный Bash-синтаксис условий.

```bash
if [[ "$file" == *.log ]]; then
  echo "log file"
fi
```

В Bash часто удобнее и безопаснее, чем `[ ]`.

---

### `-eq`

Сравнение чисел: равно.

```bash
if [ "$count" -eq 5 ]; then
  echo "five"
fi
```

---

### `-ne`

Сравнение чисел: не равно.

```bash
if [ "$code" -ne 0 ]; then
  echo "error"
fi
```

---

### `-gt`

Сравнение чисел: больше.

```bash
if [ "$load" -gt 80 ]; then
  echo "high load"
fi
```

---

### `-lt`

Сравнение чисел: меньше.

```bash
if [ "$free" -lt 10 ]; then
  echo "low space"
fi
```

---

### `=`

Сравнение строк: равно.

```bash
if [ "$env" = "prod" ]; then
  echo "production"
fi
```

---

### `!=`

Сравнение строк: не равно.

```bash
if [ "$env" != "prod" ]; then
  echo "not production"
fi
```

---

### `-z`

Проверяет, что строка пустая.

```bash
if [ -z "$name" ]; then
  echo "empty"
fi
```

---

### `-n`

Проверяет, что строка не пустая.

```bash
if [ -n "$name" ]; then
  echo "not empty"
fi
```

---

## 6. Файловые проверки

### `-f`

Проверяет, что файл существует и это обычный файл.

```bash
if [ -f /etc/passwd ]; then
  echo "file exists"
fi
```

---

### `-d`

Проверяет, что директория существует.

```bash
if [ -d /var/log ]; then
  echo "directory exists"
fi
```

---

### `-e`

Проверяет, что путь существует.

```bash
if [ -e /tmp/test ]; then
  echo "exists"
fi
```

Подходит и для файлов, и для директорий.

---

### `-r`

Проверяет, что файл доступен для чтения.

```bash
if [ -r config.yaml ]; then
  echo "readable"
fi
```

---

### `-w`

Проверяет, что файл доступен для записи.

```bash
if [ -w output.log ]; then
  echo "writable"
fi
```

---

### `-x`

Проверяет, что файл доступен для выполнения.

```bash
if [ -x script.sh ]; then
  echo "executable"
fi
```

---

### Проверить, существует ли файл

```bash
if [ -f file.txt ]; then
  echo "file exists"
fi
```

Используется перед чтением файла.

---

### Проверить, существует ли директория

```bash
if [ -d /backup ]; then
  echo "backup dir exists"
fi
```

Полезно перед записью файлов.

---

### Проверить права на файл

```bash
if [ -r file.txt ] && [ -w file.txt ]; then
  echo "can read and write"
fi
```

Можно проверять чтение, запись и выполнение.

---

## 7. Циклы

### `for`

`for` выполняет действия для каждого элемента списка.

```bash
for name in app db cache; do
  echo "$name"
done
```

---

### `while`

`while` выполняется, пока условие истинно.

```bash
while [ "$count" -lt 5 ]; do
  echo "$count"
  count=$((count + 1))
done
```

---

### `until`

`until` выполняется, пока условие ложно.

```bash
until ping -c1 google.com; do
  echo "waiting..."
  sleep 1
done
```

---

### `break`

`break` завершает цикл.

```bash
for i in 1 2 3; do
  break
done
```

Полезно, когда нужный результат уже найден.

---

### `continue`

`continue` пропускает текущую итерацию и идет к следующей.

```bash
for file in *; do
  [ -d "$file" ] && continue
  echo "$file"
done
```

---

### Цикл по файлам

```bash
for file in *.log; do
  echo "$file"
done
```

Важно использовать кавычки, если в именах могут быть пробелы.

---

### Цикл по строкам файла

```bash
while IFS= read -r line; do
  echo "$line"
done < file.txt
```

Это нормальный способ читать файл построчно.

---

### Бесконечный цикл

```bash
while true; do
  date
  sleep 5
done
```

Используется для простых watcher-скриптов.

---

## 8. Ввод и вывод

### `echo`

`echo` выводит текст.

```bash
echo "hello"
```

Простой вариант для сообщений в скриптах.

---

### `printf`

`printf` выводит форматированный текст.

```bash
printf "User: %s\n" "$USER"
```

Более предсказуемый, чем `echo`.

---

### `read`

`read` читает ввод пользователя.

```bash
read -r name
echo "$name"
```

`-r` отключает специальную обработку backslash.

---

### stdin

`stdin` — стандартный ввод.

Обычно это клавиатура или данные из pipe.

```bash
cat file.txt | grep error
```

---

### stdout

`stdout` — стандартный вывод.

Обычно это то, что команда печатает в терминал.

```bash
echo "ok"
```

---

### stderr

`stderr` — поток ошибок.

Ошибки отделены от обычного вывода.

```bash
ls /not-exist
```

---

### Redirect

Redirect — перенаправление ввода/вывода.

Например, записать вывод команды в файл.

```bash
echo "hello" > file.txt
```

---

### `>`

`>` перезаписывает файл.

```bash
echo "new" > file.txt
```

Если файл существовал, старое содержимое удалится.

---

### `>>`

`>>` добавляет вывод в конец файла.

```bash
echo "new line" >> file.txt
```

Старое содержимое остается.

---

### `2>`

`2>` перенаправляет stderr.

```bash
ls /not-exist 2> error.log
```

Ошибки попадут в файл.

---

### `2>&1`

`2>&1` объединяет stderr со stdout.

```bash
command > output.log 2>&1
```

И обычный вывод, и ошибки попадут в один файл.

---

### `/dev/null`

`/dev/null` — специальное место, куда можно выбросить вывод.

```bash
command > /dev/null 2>&1
```

Полезно, если вывод не нужен.

---

## 9. Pipes

### Pipe

Pipe передает вывод одной команды на вход другой.

```bash
command1 | command2
```

Очень часто используется для обработки текста.

---

### `|`

Символ pipe.

```bash
cat access.log | grep "500"
```

`stdout` первой команды становится `stdin` второй.

---

### `grep`

`grep` ищет строки по шаблону.

```bash
grep "error" app.log
```

Полезно для логов и фильтрации вывода.

---

### `awk`

`awk` обрабатывает текст по колонкам.

```bash
awk '{print $1}' access.log
```

Часто используют для парсинга вывода команд.

---

### `sed`

`sed` редактирует поток текста.

Например, заменить слово:

```bash
sed 's/old/new/g' file.txt
```

Полезен для простых замен.

---

### `cut`

`cut` вырезает колонки или части строк.

```bash
cut -d':' -f1 /etc/passwd
```

Здесь `-d` — разделитель, `-f` — поле.

---

### `sort`

`sort` сортирует строки.

```bash
sort names.txt
```

Часто используется перед `uniq`.

---

### `uniq`

`uniq` убирает соседние одинаковые строки.

```bash
sort file.txt | uniq
```

Подсчитать повторы:

```bash
sort file.txt | uniq -c
```

---

### `wc`

`wc` считает строки, слова и байты.

```bash
wc -l file.txt
```

`-l` — количество строк.

---

### `xargs`

`xargs` превращает входные данные в аргументы команды.

```bash
cat files.txt | xargs rm
```

Часто используют вместе с `find`.

```bash
find . -name "*.log" | xargs rm
```

## 10. Functions

### Что такое function

Function — именованный блок кода, который можно вызывать несколько раз.

Нужна, чтобы не копировать одинаковую логику.

---

### Объявление function

Вариант 1:

```bash
my_func() {
  echo "Hello"
}
```

Вызов:

```bash
my_func
```

---

### Аргументы function

Функция получает аргументы так же, как скрипт:

```bash
backup() {
  echo "source: $1"
  echo "dest: $2"
}

backup /etc /backup
```

---

### Return code function

Функция возвращает exit code последней команды.

Можно явно:

```bash
check_file() {
  [ -f "$1" ] || return 1
}
```

---

### Локальные переменные

Без `local` переменная внутри функции становится глобальной для скрипта.

```bash
name="global"

func() {
  name="changed"
}
```

Это может ломать скрипты.

---

### `local`

`local` создает переменную только внутри функции.

```bash
func() {
  local file="$1"
  echo "$file"
}
```

Хорошая практика в Bash-функциях.

---

### Переиспользование кода

Функции используют для повторяемых действий.

Например:

- логирование;

- проверки;

- backup;

- cleanup;

- restart service.


---

## 11. Arrays

### Что такое array

Array — массив, список значений в одной переменной.

```bash
servers=("web1" "web2" "db1")
```

---

### Создать array

```bash
files=("app.log" "error.log" "access.log")
```

Элементы разделяются пробелами.

---

### Получить элемент array

Индексация начинается с `0`.

```bash
echo "${files[0]}"
```

Выведет первый элемент.

---

### Все элементы array

```bash
echo "${files[@]}"
```

Для циклов почти всегда используют `"${array[@]}"`.

---

### Длина array

```bash
echo "${#files[@]}"
```

Покажет количество элементов массива.

---

### Цикл по array

```bash
for file in "${files[@]}"; do
  echo "$file"
done
```

Кавычки важны, если в элементах есть пробелы.

---

### Associative array basics

Associative array — массив с ключами вместо числовых индексов.

```bash
declare -A ports

ports[nginx]=80
ports[postgres]=5432

echo "${ports[nginx]}"
```

Работает в Bash, но не в обычном `sh`.

---

## 12. Command Substitution

### `$(command)`

Command substitution подставляет вывод команды.

```bash
today="$(date +%F)"
echo "$today"
```

---

### Backticks

Старый синтаксис:

```bash
today=`date +%F`
```

Работает, но хуже читается.

---

### Сохранить вывод команды в переменную

```bash
hostname="$(hostname)"
echo "$hostname"
```

Часто используют для `date`, `hostname`, `kubectl`, `aws`, `grep`.

---

### Вложенные команды

С `$(...)` вложенность читается нормально.

```bash
echo "Today: $(date +%F)"
```

Можно вкладывать команды друг в друга.

---

### Почему `$(...)` лучше backticks

`$(...)`:

- лучше читается;

- проще вкладывается;

- современный стиль;

- меньше проблем с экранированием.


---

## 13. Arithmetic

### `$(( ))`

Основной способ арифметики в Bash.

```bash
count=$((count + 1))
```

---

### `let`

Старый способ арифметики.

```bash
let count=count+1
```

Сейчас чаще используют `$(( ))`.

---

### `expr`

Еще один старый способ.

```bash
expr 1 + 2
```

В современных Bash-скриптах почти не нужен.

---

### Инкремент

Увеличить число на 1:

```bash
count=$((count + 1))
```

Или:

```bash
((count++))
```

---

### Декремент

Уменьшить число на 1:

```bash
count=$((count - 1))
```

Или:

```bash
((count--))
```

---

### Сравнение чисел

Для чисел используют:

```bash
-eq
-ne
-gt
-lt
-ge
-le
```

Пример:

```bash
if [ "$count" -gt 10 ]; then
  echo "too much"
fi
```

---

### Bash не для сложной математики

Bash подходит для простой целочисленной арифметики.

Для сложной математики лучше использовать:

- Python;

- awk;

- bc.


---

## 14. Globs

### `*`

`*` означает любое количество символов.

```bash
ls *.log
```

Найдет все файлы, заканчивающиеся на `.log`.

---

### `?`

`?` означает один любой символ.

```bash
ls file?.txt
```

Подойдет `file1.txt`, `fileA.txt`.

---

### `[]`

`[]` задает набор символов.

```bash
ls file[123].txt
```

Подойдет:

```text
file1.txt
file2.txt
file3.txt
```

---

### Brace expansion

Brace expansion генерирует несколько вариантов строки.

```bash
echo file{1,2,3}.txt
```

Результат:

```text
file1.txt file2.txt file3.txt
```

---

### `{}`

`{}` часто используют для генерации списков.

```bash
mkdir -p app/{logs,tmp,config}
```

Создаст несколько директорий.

---

### Path expansion

Path expansion — раскрытие путей через wildcard.

```bash
echo /var/log/*.log
```

Bash сам подставит подходящие файлы.

---

### Wildcards

Wildcards — символы-шаблоны для файлов.

Основные:

```text
*
?
[]
```

Полезны для массовой работы с файлами.

---

### Опасность `rm -rf *`

```bash
rm -rf *
```

Удалит все подходящие файлы в текущей директории.

Особенно опасно, если ты не там, где думаешь.

Перед удалением лучше:

```bash
pwd
ls
```

---

## 15. Quoting

### Почему кавычки важны

Кавычки защищают переменные от word splitting и globbing.

Почти всегда лучше писать:

```bash
"$var"
```

---

### Word splitting

Word splitting — Bash разбивает строку по пробелам.

```bash
file="my file.txt"
rm $file
```

Bash увидит два аргумента: `my` и `file.txt`.

Правильно:

```bash
rm "$file"
```

---

### Globbing

Globbing — раскрытие `*`, `?`, `[]` в имена файлов.

Если переменная содержит `*`, Bash может случайно раскрыть ее.

Кавычки это предотвращают.

---

### `"${var}"`

Безопасный способ использовать переменную.

```bash
echo "${file}"
```

Особенно полезно при склейке:

```bash
echo "${name}_backup"
```

---

### `'$var'`

Одинарные кавычки не раскрывают переменные.

```bash
echo '$HOME'
```

Выведет буквально:

```text
$HOME
```

---

### Экранирование

Экранирование отключает специальный смысл символа.

```bash
echo "\$HOME"
```

Выведет:

```text
$HOME
```

---

### `\`

Backslash экранирует следующий символ.

```bash
echo "Price: \$10"
```

Полезно для `$`, `"`, пробелов и спецсимволов.

---

### Пробелы в именах файлов

Файлы с пробелами ломают плохие скрипты.

Плохо:

```bash
rm $file
```

Хорошо:

```bash
rm "$file"
```

---

## 16. Error Handling

### `set -e`

`set -e` завершает скрипт при ошибке команды.

```bash
set -e
```

Если команда вернула non-zero exit code, скрипт остановится.

---

### `set -u`

`set -u` завершает скрипт при обращении к незаданной переменной.

```bash
set -u
```

Помогает ловить опечатки в именах переменных.

---

### `set -o pipefail`

Без `pipefail` pipeline может скрыть ошибку первой команды.

```bash
set -o pipefail
```

Хорошая практика для надежных скриптов.

---

### `set -x`

`set -x` печатает команды перед выполнением.

```bash
set -x
```

Полезно для дебага скрипта.

---

### `trap`

`trap` выполняет команду при сигнале или выходе из скрипта.

```bash
trap 'echo "cleanup"' EXIT
```

Часто используется для cleanup.

---

### Обработка ошибок

Ошибки можно обрабатывать через `if`.

```bash
if ! cp file /backup/; then
  echo "backup failed"
  exit 1
fi
```

Так понятнее, чем просто надеяться на `set -e`.

---

### Проверка команд

Команду можно проверить по exit code.

```bash
if systemctl is-active --quiet nginx; then
  echo "running"
else
  echo "not running"
fi
```

---

### Cleanup при ошибке

Cleanup нужен, чтобы удалить временные файлы или остановить процессы.

```bash
tmpfile="$(mktemp)"

trap 'rm -f "$tmpfile"' EXIT
```

Даже при ошибке файл будет удален.

---

## 17. Signals

### Что такое signal

Signal — сигнал процессу от системы или пользователя.

Например, остановить процесс или попросить завершиться.

---

### `SIGINT`

`SIGINT` обычно приходит при `Ctrl+C`.

Скрипт можно поймать через `trap`.

```bash
trap 'echo "interrupted"; exit 1' INT
```

---

### `SIGTERM`

`SIGTERM` — просьба процессу корректно завершиться.

Часто используется в systemd, Docker, Kubernetes.

---

### `SIGHUP`

`SIGHUP` исторически означал разрыв терминала.

Часто используется для reload конфигурации демонов.

---

### `trap`

`trap` ловит сигналы.

```bash
trap 'echo "stopping"; exit 0' TERM INT
```

Полезно для graceful shutdown.

---

### Cleanup on exit

Cleanup on exit выполняется при завершении скрипта.

```bash
trap 'rm -f "$tmpfile"' EXIT
```

Работает и при ошибке, и при нормальном выходе.

---

### Graceful shutdown

Graceful shutdown — аккуратное завершение.

Например:

- остановить дочерний процесс;

- удалить temp files;

- записать лог;

- закрыть соединение.


---

## 18. Работа с файлами

### Читать файл

Простой вариант:

```bash
cat file.txt
```

Но в скриптах часто читают файл построчно.

---

### Читать файл построчно

Правильный шаблон:

```bash
while IFS= read -r line; do
  echo "$line"
done < file.txt
```

`IFS=` и `-r` помогают читать строки безопаснее.

---

### Записать в файл

```bash
echo "hello" > file.txt
```

`>` перезаписывает файл.

---

### Добавить в файл

```bash
echo "new line" >> file.txt
```

`>>` добавляет строку в конец файла.

---

### Проверить пустой файл

```bash
if [ ! -s file.txt ]; then
  echo "file is empty"
fi
```

`-s` проверяет, что файл существует и не пустой.

---

### Найти файлы

```bash
find /var/log -name "*.log"
```

`find` часто используют для поиска, удаления и обработки файлов.

---

### Удалить старые файлы

Удалить `.log` старше 7 дней:

```bash
find /var/log -name "*.log" -mtime +7 -delete
```

Перед `-delete` лучше проверить без удаления.

---

### Работа с temp files

Создать временный файл:

```bash
tmpfile="$(mktemp)"
```

И удалить при выходе:

```bash
trap 'rm -f "$tmpfile"' EXIT
```

---

## 19. Работа с процессами

### `ps`

`ps` показывает процессы.

```bash
ps aux
```

Часто используют с `grep`.

---

### `pgrep`

`pgrep` ищет PID процесса по имени.

```bash
pgrep nginx
```

Удобнее, чем `ps aux | grep`.

---

### `pkill`

`pkill` отправляет сигнал процессам по имени.

```bash
pkill nginx
```

Осторожно: может задеть несколько процессов.

---

### `kill`

`kill` отправляет сигнал процессу по PID.

```bash
kill 1234
```

По умолчанию отправляет `SIGTERM`.

---

### `wait`

`wait` ждет завершения background process.

```bash
command &
pid=$!

wait "$pid"
```

---

### Background process

Background process — процесс, запущенный в фоне.

Он не блокирует текущий shell.

---

### `&`

`&` запускает команду в background.

```bash
sleep 60 &
```

Shell сразу вернет управление.

---

### PID

PID — process ID, уникальный номер процесса.

Посмотреть:

```bash
ps aux
```

Или:

```bash
echo "$$"
```

`$$` — PID текущего shell.

---

### `$!`

`$!` — PID последнего background process.

```bash
sleep 60 &
pid=$!

echo "$pid"
```

Полезно, чтобы потом сделать `wait` или `kill`.

---

## 20. Практика для DevOps/SRE

### Backup script

Скрипт для backup обычно:

- проверяет source;

- создает архив;

- пишет лог;

- удаляет старые backup.


Пример идеи:

```bash
tar -czf backup.tar.gz /etc/nginx
```

---

### Log cleanup script

Скрипт чистит старые логи.

```bash
find /var/log/myapp -name "*.log" -mtime +7 -delete
```

Важно не удалить лишнее.

---

### Healthcheck script

Healthcheck проверяет, жив ли сервис.

```bash
curl -fsS http://localhost:8080/health
```

Если команда вернула ошибку — сервис считается unhealthy.

---

### Service restart script

Скрипт может проверить сервис и перезапустить его.

```bash
if ! systemctl is-active --quiet nginx; then
  systemctl restart nginx
fi
```

---

### Deploy script

Deploy script обычно:

- скачивает новую версию;

- обновляет файлы;

- перезапускает сервис;

- проверяет healthcheck.


---

### Cron script

Скрипт для cron должен использовать абсолютные пути.

```bash
/usr/bin/bash /opt/scripts/backup.sh
```

В cron окружение минимальное.

---

### Disk usage alert

Проверка заполнения диска:

```bash
df -h
```

В скрипте обычно парсят процент и отправляют alert.

---

### Process check

Проверка процесса:

```bash
pgrep nginx > /dev/null
```

Если процесса нет — можно отправить alert или restart.

---

### HTTP check

Проверка HTTP endpoint:

```bash
curl -fsS https://example.com/health
```

`-f` делает ошибку при HTTP 4xx/5xx.

---

### JSON parsing basics

Для JSON лучше использовать `jq`.

```bash
curl -s http://localhost:8080/status | jq '.status'
```

Парсить JSON через `grep/sed` обычно плохая идея.

---

## 21. Частые ошибки

### Нет shebang

Если нет shebang, непонятно, каким shell запускать скрипт.

Правильно:

```bash
#!/bin/bash
```

Или:

```bash
#!/usr/bin/env bash
```

---

### Нет execute permissions

Если нет права на выполнение:

```text
Permission denied
```

Исправить:

```bash
chmod +x script.sh
```

---

### Пробелы вокруг `=`

Неправильно:

```bash
name = "Ivan"
```

Правильно:

```bash
name="Ivan"
```

В Bash пробелы вокруг `=` ломают присваивание.

---

### Нет кавычек вокруг переменных

Плохо:

```bash
rm $file
```

Хорошо:

```bash
rm "$file"
```

Без кавычек можно словить проблемы с пробелами и wildcard.

---

### Неправильный exit code

Скрипт должен возвращать:

- `0` при успехе;

- не `0` при ошибке.


```bash
exit 1
```

Используется для ошибок.

---

### Скрипт работает руками, но не в cron

Частые причины:

- другой `$PATH`;

- нет environment variables;

- относительные пути;

- нет прав;

- другой working directory.


Лучше использовать абсолютные пути.

---

### Относительные пути

Плохо:

```bash
./backup
```

В cron или systemd текущая директория может быть другой.

Лучше:

```bash
/opt/scripts/backup.sh
```

---

### Bash-specific код в sh

Если shebang:

```bash
#!/bin/sh
```

Нельзя использовать Bash-only фичи вроде arrays или `[[ ]]`.

Для Bash-фич нужно:

```bash
#!/bin/bash
```

---

### Потеря ошибок в pipe

Без `pipefail` ошибка в начале pipeline может потеряться.

```bash
set -o pipefail
```

Хорошая практика:

```bash
set -euo pipefail
```

---

### Опасный `rm`

Опасные варианты:

```bash
rm -rf *
rm -rf "$dir"
```

Если переменная пустая или путь неправильный — можно удалить лишнее.

Перед удалением проверяй переменные и `pwd`.

---

## 22. Interview Questions

### Что такое shebang

Shebang — первая строка скрипта, которая указывает интерпретатор.

```bash
#!/bin/bash
```

---

### Как передать аргументы в script

Аргументы передаются после имени скрипта.

```bash
./script.sh arg1 arg2
```

Внутри:

```bash
$1
$2
```

---

### Что такое `$?`

`$?` — exit code последней выполненной команды.

```bash
ls /not-exist
echo "$?"
```

---

### Что такое `$@`

`$@` — все аргументы скрипта.

Правильно использовать так:

```bash
for arg in "$@"; do
  echo "$arg"
done
```

---

### Разница `$@` и `$*`

`"$@"` сохраняет аргументы отдельными элементами.

`"$*"` склеивает аргументы в одну строку.

В большинстве случаев нужен `"$@"`.

---

### Разница `[ ]` и `[[ ]]`

`[ ]` — старый POSIX-синтаксис.

`[[ ]]` — Bash-расширение, удобнее и безопаснее.

```bash
[[ "$file" == *.log ]]
```

---

### Что делает `set -e`

`set -e` завершает скрипт, если команда вернула ошибку.

```bash
set -e
```

---

### Что делает `set -u`

`set -u` завершает скрипт при использовании незаданной переменной.

Помогает ловить опечатки.

---

### Что делает `pipefail`

`pipefail` делает pipeline ошибочным, если упала любая команда внутри pipe.

```bash
set -o pipefail
```

---

### Что такое trap

`trap` задает действие на сигнал или завершение скрипта.

```bash
trap 'cleanup' EXIT
```

Используется для cleanup и graceful shutdown.

---

### Как читать файл построчно

```bash
while IFS= read -r line; do
  echo "$line"
done < file.txt
```

Это стандартный безопасный шаблон.

---

### Как проверить существование файла

```bash
if [ -f file.txt ]; then
  echo "exists"
fi
```

`-f` проверяет обычный файл.

---

### Как запустить процесс в background

```bash
sleep 60 &
```

`&` запускает команду в фоне.

---

### Как получить PID последнего background process

```bash
sleep 60 &
pid=$!
echo "$pid"
```

`$!` хранит PID последнего фонового процесса.

---

### Почему нужны кавычки вокруг переменных

Кавычки защищают от:

- пробелов;

- word splitting;

- globbing;

- неожиданных аргументов.


Правильно:

```bash
rm "$file"
```