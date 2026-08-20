

Производительность Linux обычно разбирают по четырём главным направлениям:

1. CPU

2. память

3. диск

4. сеть


Задача DevOps/SRE — не просто посмотреть `top`, а понять:

- где именно узкое место;

- какой процесс создаёт нагрузку;

- это проблема приложения, системы, диска, сети или лимитов;

- что можно проверить дальше.


---

# CPU usage

CPU usage — это загрузка процессора.

В `top` можно увидеть строку вида:

```bash
%Cpu(s):  20.0 us,  5.0 sy,  0.0 ni, 70.0 id,  3.0 wa,  0.0 hi,  1.0 si,  1.0 st
```

Основные поля:

```text
us  user        CPU занят пользовательскими процессами
sy  system      CPU занят ядром Linux
id  idle        CPU простаивает
wa  iowait      CPU ждёт диск или другое I/O
st  steal       CPU украден гипервизором у виртуальной машины
hi  hardware interrupts
si  software interrupts
```

Самые важные для диагностики:

```text
us + sy высокий     CPU реально занят работой
wa высокий          процессы ждут диск
st высокий          проблема на уровне виртуализации/хоста
id высокий          CPU свободен
```

Пример CPU bottleneck:

```text
CPU usage: 95%
load average: высокий
iowait: низкий
процессы в top жрут CPU
```

Это значит, что сервер реально упёрся в процессор.

---

# Load average

Load average показывает среднее количество задач, которые:

1. выполняются на CPU;

2. ждут CPU;

3. находятся в состоянии uninterruptible sleep, обычно из-за I/O.


Посмотреть:

```bash
uptime
```

Пример:

```bash
 15:20:01 up 10 days,  2:15,  2 users,  load average: 4.20, 3.80, 2.50
```

Три числа — это средняя нагрузка за:

```text
1 минута
5 минут
15 минут
```

То есть:

```text
4.20 — средняя нагрузка за последнюю минуту
3.80 — за 5 минут
2.50 — за 15 минут
```

---

# Как понимать load average

Load average нужно сравнивать с количеством CPU cores.

Посмотреть количество CPU:

```bash
nproc
```

Или:

```bash
lscpu
```

Пример:

```text
1 CPU core:
load 1.00 ≈ сервер полностью занят
load 2.00 ≈ задач в 2 раза больше, чем CPU может нормально обработать

4 CPU cores:
load 4.00 ≈ нормальная полная загрузка
load 8.00 ≈ перегруз примерно в 2 раза
```

Важно:

```text
load average ≠ CPU usage
```

Load может быть высоким даже при низком CPU.

---

# Почему load average может быть высокий при низком CPU

В Linux load average учитывает не только процессы, которые хотят CPU, но и процессы в состоянии `D`.

Состояние `D` — uninterruptible sleep.

Обычно это ожидание I/O:

- медленный диск;

- зависший NFS;

- проблемы с SAN/storage;

- медленные операции чтения/записи;

- проблемы с файловой системой;

- ожидание ответа от блочного устройства.


Пример ситуации:

```text
CPU usage: 20%
load average: 50
iowait: высокий
много процессов в состоянии D
```

Это не CPU bottleneck. Это, скорее всего, I/O bottleneck.

Посмотреть процессы в состоянии `D`:

```bash
ps aux | awk '$8 ~ /D/ { print }'
```

Или:

```bash
top
```

В колонке `S` будет состояние процесса.

---

# Memory usage

Память в Linux нельзя оценивать только по строке `free`.

Команда:

```bash
free -m
```

Пример:

```bash
              total        used        free      shared  buff/cache   available
Mem:           7930        2100         500         100        5330        5400
Swap:          2048           0        2048
```

Основные поля:

```text
total        всего RAM
used         использовано
free         полностью свободно
buff/cache   память под buffers и page cache
available   сколько памяти реально доступно приложениям
```

Главное поле:

```text
available
```

Если `free` мало, но `available` много — это нормально.

Linux активно использует свободную RAM под кэш.

---

# Page cache

Page cache — это кэш файлов в оперативной памяти.

Когда приложение читает файл с диска, Linux может оставить содержимое файла в RAM.

Зачем:

```text
первое чтение — с диска
следующее чтение — из памяти
```

Это ускоряет работу.

Поэтому Linux может показывать, что памяти “занято много”, хотя на самом деле значительная часть занята кэшем и может быть освобождена при необходимости.

Пример:

```bash
free -m
```

```text
buff/cache: 5000 MB
available: 5200 MB
```

Это нормально.

Плохо, когда:

```text
available мало
swap активно используется
OOM killer убивает процессы
приложения получают ошибки memory allocation
```

---

# Buffers/cache

`buffers` и `cache` — это память, которую ядро использует для ускорения I/O.

Упрощённо:

```text
buffers — кэш метаданных блочных устройств
cache   — page cache, кэш файлов
```

В современных выводах `free` часто объединяет это в `buff/cache`.

Важно:

```text
buff/cache — это не обязательно проблема
```

Linux может освободить эту память для приложений.

---

# Swap

Swap — это место на диске, которое используется как продолжение RAM.

Посмотреть:

```bash
free -m
```

```bash
swapon --show
```

Если swap немного используется — это не всегда плохо.

Плохо, когда swap активно читается и пишется.

Проверить через `vmstat`:

```bash
vmstat 1
```

Смотреть поля:

```text
si  swap in
so  swap out
```

Если `si` и `so` постоянно больше нуля — серверу не хватает RAM.

---

# OOM Killer

OOM Killer — механизм ядра Linux, который убивает процессы, когда системе не хватает памяти.

OOM = Out Of Memory.

Когда памяти не хватает, ядро выбирает “жертву” и завершает процесс.

Проверить OOM:

```bash
dmesg -T | grep -i oom
```

Или:

```bash
journalctl -k | grep -i oom
```

Часто можно увидеть:

```text
Out of memory: Killed process 1234 (java)
```

Это значит, что ядро убило процесс `java`, потому что не хватило памяти.

---

# Как найти, кто съел память

Быстро через `ps`:

```bash
ps aux --sort=-%mem | head
```

Через `top`:

```bash
top
```

Потом нажать:

```text
M
```

Это отсортирует процессы по памяти.

Ещё полезно:

```bash
ps -eo pid,ppid,cmd,%mem,%cpu,rss,vsz --sort=-rss | head
```

Где:

```text
RSS — реально используемая физическая память
VSZ — виртуальная память процесса
```

Важно:

```text
VSZ может быть огромным, но это не значит, что процесс реально съел столько RAM
RSS важнее для практической диагностики
```

---

# Disk I/O

Disk I/O — это чтение и запись на диск.

Проблемы с диском часто выглядят так:

```text
load average высокий
CPU usage невысокий
iowait высокий
приложения тормозят
запросы к БД медленные
логи пишутся медленно
```

Основная команда:

```bash
iostat -xz 1
```

Если команды нет, пакет обычно называется:

```bash
sysstat
```

Пример установки на Debian/Ubuntu:

```bash
sudo apt install sysstat
```

---

# Важные поля iostat

Пример:

```bash
iostat -xz 1
```

Смотреть:

```text
%util    насколько занят диск
await    среднее время ожидания I/O
r/s      операций чтения в секунду
w/s      операций записи в секунду
rkB/s    скорость чтения
wkB/s    скорость записи
aqu-sz   средняя длина очереди
```

Пример проблемы:

```text
%util: 100%
await: высокий
aqu-sz: высокий
```

Это похоже на disk bottleneck.

Важно:

```text
100% util для одного диска — часто плохо
100% util для быстрых SSD/NVMe не всегда означает проблему
```

Нужно смотреть ещё latency: `await`.

---

# Network I/O

Network I/O — это сетевой ввод/вывод.

Симптомы сетевой проблемы:

```text
приложение не может подключиться к сервису
много timeout
низкая скорость передачи
много retransmits
пакеты теряются
соединения зависают
```

Посмотреть интерфейсы:

```bash
ip addr
```

Статистика интерфейса:

```bash
ip -s link
```

Посмотреть соединения:

```bash
ss -tulpn
```

Общая TCP-статистика:

```bash
ss -s
```

Через `sar`:

```bash
sar -n DEV 1
```

TCP-статистика:

```bash
sar -n TCP,ETCP 1
```

Особенно полезно смотреть retransmits:

```text
retransmits растут → возможны потери пакетов или проблемы сети
```

---

# top

`top` — базовая команда для просмотра нагрузки системы.

Запуск:

```bash
top
```

Что смотреть:

```text
load average
%Cpu(s)
KiB/MiB Mem
KiB/MiB Swap
PID
USER
%CPU
%MEM
TIME+
COMMAND
```

Полезные клавиши:

```text
P     сортировать по CPU
M     сортировать по памяти
1     показать загрузку по каждому CPU core
k     убить процесс
c     показать полную команду запуска
```

Пример:

```text
%CPU высокий у одного процесса → он грузит CPU
%MEM высокий → процесс ест память
много zombie → проблема с родительским процессом
```

---

# htop

`htop` — более удобная версия `top`.

Запуск:

```bash
htop
```

Плюсы:

```text
удобная навигация
цветной интерфейс
можно сортировать процессы
можно искать процесс
видно дерево процессов
удобно убивать процессы
```

Полезные клавиши:

```text
F5    tree view
F6    sort by
F9    kill
/     search
```

---

# uptime

`uptime` показывает:

```text
текущее время
сколько система работает
сколько пользователей
load average
```

Пример:

```bash
uptime
```

```text
15:25:10 up 12 days,  4:10,  2 users,  load average: 0.50, 0.70, 0.90
```

Используется для быстрой оценки:

```text
сервер давно работает?
нагрузка высокая?
нагрузка растёт или падает?
```

Если:

```text
1-minute load > 15-minute load
```

нагрузка растёт.

Если:

```text
1-minute load < 15-minute load
```

нагрузка снижается.

---

# free -m

Показывает использование памяти в мегабайтах:

```bash
free -m
```

Главное смотреть:

```text
available
swap used
```

Пример нормальной ситуации:

```text
free мало
buff/cache много
available много
swap почти не используется
```

Пример плохой ситуации:

```text
available мало
swap used высокий
si/so в vmstat растут
OOM в dmesg
```

---

# vmstat

`vmstat` показывает CPU, память, swap и I/O.

Запуск:

```bash
vmstat 1
```

Пример:

```text
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 500000 100000 3000000  0    0    10    20 1000 2000 20  5 75  0  0
```

Важные поля:

```text
r   сколько процессов ждут CPU
b   сколько процессов заблокированы в ожидании I/O
si  swap in
so  swap out
bi  block input
bo  block output
us  user CPU
sy  system CPU
id  idle
wa  iowait
st  steal
```

Диагностика:

```text
r высокий, CPU занят     → CPU bottleneck
b высокий, wa высокий    → I/O bottleneck
si/so высокие            → нехватка RAM, swap pressure
st высокий               → проблема с виртуализацией
```

---

# iostat

`iostat` используется для анализа дисков.

Запуск:

```bash
iostat -xz 1
```

Ключи:

```text
-x   extended statistics
-z   не показывать неактивные устройства
1    обновлять каждую секунду
```

Смотреть:

```text
await
%util
r/s
w/s
rkB/s
wkB/s
aqu-sz
```

Пример disk bottleneck:

```text
await высокий
%util около 100
очередь растёт
приложение тормозит на чтении/записи
```

---

# iotop

`iotop` показывает, какие процессы читают или пишут на диск.

Запуск:

```bash
sudo iotop
```

Показать только активные процессы:

```bash
sudo iotop -o
```

Показать накопленную статистику по процессам:

```bash
sudo iotop -a
```

Полезный вариант:

```bash
sudo iotop -oPa
```

Где:

```text
-o  only active
-P  показывать процессы, а не threads
-a  accumulated
```

Используется, чтобы найти:

```text
кто активно пишет на диск
кто читает с диска
какой процесс создаёт I/O wait
```

---

# sar

`sar` собирает и показывает историческую статистику системы.

Обычно входит в пакет:

```bash
sysstat
```

Примеры:

CPU:

```bash
sar 1
```

Память:

```bash
sar -r 1
```

Swap:

```bash
sar -S 1
```

Диски:

```bash
sar -d 1
```

Сеть:

```bash
sar -n DEV 1
```

TCP:

```bash
sar -n TCP,ETCP 1
```

Исторические данные:

```bash
sar
```

Или за конкретный день:

```bash
sar -f /var/log/sysstat/sa05
```

`sar` полезен, когда проблема была ночью, а сейчас сервер уже работает нормально.

---

# pidstat

`pidstat` показывает статистику по процессам.

CPU по процессам:

```bash
pidstat 1
```

Память по процессам:

```bash
pidstat -r 1
```

Диск по процессам:

```bash
pidstat -d 1
```

Потоки:

```bash
pidstat -t 1
```

Конкретный процесс:

```bash
pidstat -p 1234 1
```

Очень полезно, когда нужно смотреть не всю систему, а конкретный PID.

---

# dstat

`dstat` — универсальная утилита для просмотра CPU, диска, сети и памяти в одном месте.

Запуск:

```bash
dstat
```

Пример:

```bash
dstat -cdnm
```

Где:

```text
-c  CPU
-d  disk
-n  network
-m  memory
```

Можно использовать как быстрый live-dashboard в терминале.

На некоторых современных системах `dstat` может быть заменён на `pcp-dstat`.

---

# mpstat

`mpstat` показывает загрузку CPU по ядрам.

Запуск:

```bash
mpstat 1
```

По всем CPU cores:

```bash
mpstat -P ALL 1
```

Полезно, если:

```text
общая загрузка CPU вроде не 100%
но одно ядро загружено полностью
приложение однопоточное
```

Пример:

```text
CPU0: 100%
CPU1: 10%
CPU2: 5%
CPU3: 8%
```

Это может означать, что один поток упёрся в одно ядро.

---

# lsof

`lsof` показывает открытые файлы.

Название:

```text
list open files
```

Посмотреть, какие файлы открыл процесс:

```bash
sudo lsof -p 1234
```

Кто открыл конкретный файл:

```bash
sudo lsof /var/log/nginx/access.log
```

Кто слушает порт:

```bash
sudo lsof -i :80
```

Сетевые соединения:

```bash
sudo lsof -i
```

Удалённые, но всё ещё открытые файлы:

```bash
sudo lsof | grep deleted
```

Это важно, когда место на диске закончилось, файл удалили, но место не освободилось.

Причина:

```text
процесс всё ещё держит файл открытым
```

---

# strace

`strace` показывает системные вызовы процесса.

Это полезно, когда нужно понять, что процесс делает на уровне ядра:

```text
читает файл
пишет файл
открывает сокет
ждёт futex
получает permission denied
не может найти файл
```

Запустить команду под `strace`:

```bash
strace command
```

Подключиться к процессу:

```bash
sudo strace -p 1234
```

С таймингами:

```bash
sudo strace -tt -T -p 1234
```

С дочерними процессами:

```bash
sudo strace -f -p 1234
```

Только файловые операции:

```bash
sudo strace -e trace=file -p 1234
```

Только сеть:

```bash
sudo strace -e trace=network -p 1234
```

Важно:

```text
strace может замедлять процесс
на production использовать осторожно
```

---

# Как понять, CPU bottleneck или I/O bottleneck

## Признаки CPU bottleneck

```text
CPU usage высокий
us/sy высокий
iowait низкий
load average высокий
много процессов ждут CPU
r в vmstat высокий
```

Команды:

```bash
top
mpstat -P ALL 1
pidstat 1
vmstat 1
```

Что смотреть:

```text
%CPU процессов
us
sy
r
idle
```

Пример:

```text
CPU idle: 0-5%
iowait: 0-2%
load: выше количества CPU cores
```

Скорее всего, CPU bottleneck.

---

## Признаки I/O bottleneck

```text
load average высокий
CPU idle может быть высоким
iowait высокий
процессы в состоянии D
диск имеет высокий await
диск загружен
```

Команды:

```bash
top
vmstat 1
iostat -xz 1
iotop -oPa
ps aux | awk '$8 ~ /D/ { print }'
```

Что смотреть:

```text
wa в top/vmstat
b в vmstat
await в iostat
%util в iostat
D-state процессы
```

Пример:

```text
CPU idle: 70%
iowait: 25%
load average: 30
много процессов D
```

Это не CPU bottleneck. Это I/O bottleneck.

---

# Как найти, кто пишет на диск

Через `iotop`:

```bash
sudo iotop -oPa
```

Через `pidstat`:

```bash
pidstat -d 1
```

Через `lsof`, если подозреваешь конкретный файл:

```bash
sudo lsof /path/to/file
```

Если быстро растёт директория:

```bash
du -sh /var/log/*
```

Или:

```bash
du -ah /var/log | sort -h | tail
```

Если файл удалён, но место не освободилось:

```bash
sudo lsof | grep deleted
```

---

# Как найти, кто открыл много файлов

Посмотреть лимит открытых файлов:

```bash
ulimit -n
```

Для процесса:

```bash
cat /proc/1234/limits | grep "open files"
```

Сколько файлов открыл процесс:

```bash
ls /proc/1234/fd | wc -l
```

Топ процессов по количеству открытых файлов:

```bash
for pid in /proc/[0-9]*; do
  count=$(ls "$pid/fd" 2>/dev/null | wc -l)
  cmd=$(tr -d '\0' < "$pid/cmdline" 2>/dev/null | cut -c1-80)
  echo "$count ${pid#/proc/} $cmd"
done | sort -nr | head
```

Посмотреть открытые файлы процесса:

```bash
sudo lsof -p 1234
```

Посмотреть общее использование file handles в системе:

```bash
cat /proc/sys/fs/file-nr
```

Пример вывода:

```text
12345 0 9223372036854775807
```

Упрощённо:

```text
первое число — сколько file handles используется
третье число — системный лимит
```

---

# Быстрый алгоритм диагностики производительности

## 1. Сначала понять общую картину

```bash
uptime
top
free -m
df -h
```

Смотреть:

```text
load average
CPU usage
available memory
swap
место на диске
```

---

## 2. Если высокий CPU

```bash
top
mpstat -P ALL 1
pidstat 1
```

Ищем:

```text
какой процесс грузит CPU
одно ядро или все
user CPU или system CPU
```

---

## 3. Если высокий load, но CPU не загружен

```bash
vmstat 1
iostat -xz 1
ps aux | awk '$8 ~ /D/ { print }'
```

Ищем:

```text
iowait
D-state процессы
проблемы с диском
проблемы с NFS/storage
```

---

## 4. Если заканчивается память

```bash
free -m
ps aux --sort=-%mem | head
vmstat 1
dmesg -T | grep -i oom
```

Ищем:

```text
available memory
swap in/out
процессы с большим RSS
OOM killer
```

---

## 5. Если тормозит диск

```bash
iostat -xz 1
sudo iotop -oPa
pidstat -d 1
```

Ищем:

```text
await
%util
какой процесс пишет/читает
```

---

## 6. Если проблема с сетью

```bash
ip -s link
ss -s
sar -n DEV 1
sar -n TCP,ETCP 1
```

Ищем:

```text
ошибки интерфейса
drops
retransmits
много соединений
забитый bandwidth
```

---

# Что важно на собеседовании

Нужно уметь объяснить:

```text
load average — это не процент CPU
Linux load учитывает процессы в D-state
free memory в Linux не значит “память реально свободна”
buff/cache — это нормально
available важнее, чем free
iowait указывает на ожидание I/O
OOM Killer убивает процессы при нехватке памяти
iostat помогает понять проблемы диска
iotop помогает найти процесс, который пишет на диск
lsof помогает найти открытые файлы и удалённые файлы, удерживаемые процессом
strace помогает понять, на каком syscall завис процесс
```

Короткая формула:

```text
CPU проблема      → top, mpstat, pidstat
Memory проблема   → free, ps, vmstat, dmesg
Disk проблема     → iostat, iotop, pidstat -d
Network проблема  → ss, ip -s link, sar -n
Process проблема  → lsof, strace, /proc
```

---

# Мини-шпаргалка

```bash
uptime
```

Понять load average.

```bash
top
```

Общая картина CPU, RAM, процессов.

```bash
htop
```

Удобный интерактивный просмотр процессов.

```bash
free -m
```

Память, cache, available, swap.

```bash
vmstat 1
```

CPU, память, swap, I/O.

```bash
iostat -xz 1
```

Диски, latency, utilization.

```bash
sudo iotop -oPa
```

Кто пишет/читает диск.

```bash
sar -n DEV 1
```

Сетевая статистика.

```bash
pidstat 1
```

CPU по процессам.

```bash
pidstat -r 1
```

Память по процессам.

```bash
pidstat -d 1
```

Диск по процессам.

```bash
mpstat -P ALL 1
```

CPU по ядрам.

```bash
sudo lsof -p PID
```

Открытые файлы процесса.

```bash
sudo strace -tt -T -p PID
```

Посмотреть, на каких syscall процесс тормозит.