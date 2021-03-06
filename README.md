# Простой скрипт проверки быстродействия PHP

Работает со всеми версиями ПХП: от 4.3 до 7.2

## Зависимости

Необходимы модули для php:

- pcre
- mbstring
- json
- xmlrpc

Обычно они уже установлены или "вкомпилированны" в php.

Проверить наличие:

- в консоли `php -m`
- или через вывод функции `phpinfo()`

## Запуск

### 1. Через консоль

Команда:
```
php bench.php [-h|--help] [-d|--dont-recalc] [-L|--list-tests] [-I|--system-info] [-m|--memory-limit=256] [-t|--time-limit=600] [-T|--run-test=name1 ...]

	-h|--help		- вывод помощи и выход
	-d|--dont-recalc	- не пересчитывать время выполнения тестов, если ограничения слишком низкие
	-L|--list-tests		- вывод списка доступных тестов и выход
	-I|--system-info	- вывод информации о системе без запуска тестов и выход
	-m|--memory-limit <Mb>	- установка значения параметра `memory_limit` в Мб, по-умолчанию равно 256 (Мб)
	-t|--time-limit <sec>	- установка значения параметра `max_execution_time` в секундах, по-умолчанию равно 600 (сек)
	-T|--run-test <name>	- запустить только указанные тесты, названия тестов из вывода параметра --list-tests, можно указать несколько раз
```
Например: `php bench.php -m=64 -t=30`

Второй вариант передачи значений для параметров - переменные окружения:
```
env PHP_MEMORY_LIMIT=64 PHP_TIME_LIMIT=30 php bench.php
```

Доступные переменные:

- PHP_MEMORY_LIMIT=<Мб>
- PHP_TIME_LIMIT=<Секунды>
- DONT_RECALCULATE_LIMITS=0/1
- LIST_TESTS=0/1
- SYSTEM_INFO=0/1
- RUN_TESTS=test1,test2,...

### 2. Через веб-сервера (apache + php)

Просто положите в любую доступную для выполнения php директорию сайта, например в корень.

Потом скрипт можно будет вызывать с параметрами, как из консоли:
`curl http://www.example.com/bench.php?memory_limit=64&time_limit=30`
или через браузер.

Доступные параметры:

- memory_limit=Мб
- time_limit=Секунды
- dont_recalculate_limits=0/1
- list_tests=0/1
- system_info=0/1
- run_tests=test1,test2,...

### Учет параметров хостинга

На многих хостингах параметры `memory_limit` и `max_execution_time` могут быть жестко зафиксированы.

В этом случа скрипт не сможет установить переданные в него значения параметров,
по крайней мере не выше лимитов.

Пересчет времени выполнения скрипта будет произведен по наименьшим результирующим значениям.

## ChangeLog

@ 2018-08-08, v1.0.32

 * Были неправы - stdClass есть в php-4 тоже

@ 2018-08-08, v1.0.31

 * Исправили тесты с различной сериализацией - объекты есть только в php-5+

@ 2018-08-08, v1.0.30

 * Добавили в некоторых тестах сериализации к объекту тестирования поля с разными типами данных
 * Поправили тесты xmlrpc - в php-7.2+libxmlrpc-epi проблемы со строками с html-тегами

@ 2018-08-08, v1.0.29

 * Добавили параметр -L для вывода списка тестов
 * Добавили параметр -T для запуска только конкретных тестов
 * Добавили параметр -I для вывода только информации о системе без запуска тестов

@ 2018-08-08, v1.0.28.1

 * Поправили вывод секунд - нужно на один символ больше места
 * Немного поменяли вывод информации

@ 2018-08-07, v1.0.28

 * Поправили пересчет размеров в единицы байт, при 0 происходила мат.ошибка

@ 2018-08-07, v1.0.27

 * Добавили новый параметр, отключающий пересчет ограничений по времени для тестов

@ 2018-08-07, v1.0.26

 * Добавили вывод общего кол-ва операций в секунду, и операций в секунду на МГц
 * Добавили вывод включенных необходимых модулей

@ 2018-08-06, v1.0.25

 * Добавили тестирование xmlrpc (xml)
 * Добавили вывод предупреждений, если не все необходимые модули php установлены

@ 2017-09-04, v1.0.24

 * Поправили пересчет времени тестов, если процессор Atom или ARM

@ 2017-09-04, v1.0.23

 * Обновили тест на работу с try-catch блоком - отдельные под-тесты: без блока, блок без exception, и с exception
 * Добавили пересчет времени тестов, если процессор Atom или ARM - они реально медленные

@ 2017-06-03, v1.0.22

 * Добавили тесты производительности новых операций в php-7
 * Вынесли инициализацию переменных за счетчики времени в тестах
 * Обновили счетчики времени для разных версий php
 * Тест array_range - насколько сильно влияет на следующий тест array_unset

@ 2017-05-25, v1.0.21

 * Добавили тесты производительности конвертации простых типов:
   `string => (int)`, `string => intval()`

@ 2017-05-19, v1.0.20

 * Поддержва длинных опций ком.строки только в php-5.3+
 * Добавили проверку форматирования строк - производительность сбора `''` строки с числами,
   или `""` строки с форматированием чисел внутри.
 * Очищаем данные после теста строк, массивов - меньше занятой памяти

@ 2017-05-19, v1.0.19

 * Попытка принудительно включить небуферизированный вывод
 * Спец-заголовок для nginx для отключения буферизации
 * Возможность загрузить основные тесты без файла php5.inc с тестом try/Exception/catch

@ 2017-05-18, v1.0.18

 * Проверка на совместимую версию php
 * Получение значений для настроек php - `max_execution_time` и `memory_limit` - из
   GET / getenv / getopt.

@ 2017-05-18, v1.0.17

 * Попытка укладываться в max_execution_time
   Т.к. зависимость от hardware не линейная - много hack-ов.
   Может не всегда срабатывать.

@ 2017-05-18, v1.0.16

 * Сделали поиск доступных алгоритмов хеширования для crypt()
 * По-умолчанию считаем, что доступен для всех MD5

@ 2017-05-17, v1.0.15

 * Поправили работу скрипта с php-7.x - больше ограничений по памяти
 * Добавили вывод используемой памяти (@ryr)

@ 2017-05-06, v1.0.14

 * Изменили работу скрипта, если доступно памяти менее 256Мб

@ 2017-05-06, v1.0.13

 * Поправили немного code-style (@ryr)
 * Добавили больше данных в тесты сериализации

@ 2017-04-21, v1.0.12

 * Правильная конвертация значений в единицы SI.
 * Считаем операции в секунду на МГц.
 * Обновил вывод - добавил заголовок столбцам

@ 2017-04-20, v1.0.11

 * Нагружаем процессор, чтобы определить MHz только если разница между
   значениями 'cpu MHz' и 'bogomips/2' большая.

@ 2017-04-20, v1.0.10

 * Тесты массивов теперь всегда включены, они больше не съедают много памяти
 * Добавлено определение CPU на Linux-системах, добавлен вывод операций на МГц
 * В выводе uname осталена только необходимая для сравнения информация
 * Обновлен README

@ 2017-04-06, v1.0.9

 * Поправлен подсчет операций в секунду для теста массивов

@ 2017-04-06, v1.0.8

 * Тесты, которых нет в php-4.4 вынесены в отдельный подключаемый файл

@ 2017-04-06, v1.0.7

 * Изменены названия функций-тестов для сортировки перед запуском
 * Обновлено форматирование вывода результатов тестов
 * Добавлены и обновлены тесты:
   - обращение к определенныи и неопределенным переменным/ключам массива
   - исключения (exceptions)
   - к хешированию добавлен тест crypt
   - тест массивов разбит на три уровня - время выполнения то же, памяти занимает меньше

@ 2015-07-16, v1.0.6

 * Добавлены тесты: preg & serialize

@ 2015-07-02, v1.0.5

 * Добавлен тест простейшего копирования строк

@ 2015-07-02, v1.0.4

 * Добавлено увеличение лимита по памяти и времени выполнения

@ 2015-07-02, v1.0.3

 * Исправлено определение доступных функций, сделан пропуск тестов для них

@ 2015-07-02, v1.0.2

 * Добавлено еще больше функций, теперь требуется наличие mbstring и json модулей
 * Потребление памяти увеличено из-за тестирования массивов - нужно более 1Гб

@ 2015-07-01, v1.0.1

 * Добавлен вывод потребления памяти
 * Добавлены новые функции, увеличен размер проверочной строки

## Пример вывода скрипта

```
<pre>
<<< WARNING >>>
CPU is in powersaving mode? Set CPU governor to 'performance'!
 Fire up CPU and recalculate MHz!
</pre>
<pre>
-------------------------------------------------------------------------------------------
|                                  PHP BENCHMARK SCRIPT                                   |
-------------------------------------------------------------------------------------------
Start               : 2018-08-08 03:09:33
Server              : Linux/4.15.0-29-generic x86_64
Platform            : Linux
CPU                 :
              model : Intel(R) Core(TM) i5-6600K CPU @ 3.50GHz
              cores : 4
                MHz : 3660.916MHz
Memory              : 256 Mb available
Benchmark version   : 1.0.28.1
PHP version         : 7.0.23-Ubuntu/16.04-SergeyD/11.1
  available modules :
           mbstring : yes
               json : yes
             xmlrpc : yes
               pcre : yes
Max execution time  : 600 sec
Crypt hash algo     : MD5
-------------------------------------------------------------------------------------------
TEST NAME                      :      SECONDS |       OP/SEC |      OP/SEC/MHz |    MEMORY
-------------------------------------------------------------------------------------------
01_math                        :    2.717 sec | 515.18 kOp/s | 140.72  Ops/MHz |      2 Mb
02_string_concat               :    0.243 sec |  31.67 MOp/s |   8.65 kOps/MHz | 128.84 Mb
03_1_string_number_concat      :    1.760 sec |   2.84 MOp/s | 776.11  Ops/MHz |      4 Mb
03_2_string_number_format      :    1.462 sec |   3.42 MOp/s | 934.05  Ops/MHz |      4 Mb
04_string_simple_functions     :    1.672 sec | 777.53 kOp/s | 212.39  Ops/MHz |      4 Mb
05_string_multibyte            :    4.475 sec |  29.05 kOp/s |   7.94  Ops/MHz |      4 Mb
06_string_manipulation         :    2.488 sec | 522.51 kOp/s | 142.73  Ops/MHz |      4 Mb
07_regex                       :    1.943 sec | 669.17 kOp/s | 182.79  Ops/MHz |      4 Mb
08_1_hashing                   :    2.069 sec | 628.45 kOp/s | 171.66  Ops/MHz |      4 Mb
08_2_crypt                     :    6.094 sec |   1.64 kOp/s |   0.45  Ops/MHz |      4 Mb
09_json_encode                 :    1.570 sec | 828.13 kOp/s | 226.21  Ops/MHz |      4 Mb
10_json_decode                 :    1.946 sec | 667.98 kOp/s | 182.46  Ops/MHz |      4 Mb
11_serialize                   :    1.253 sec |   1.04 MOp/s | 283.35  Ops/MHz |      4 Mb
12_unserialize                 :    1.731 sec | 751.18 kOp/s | 205.19  Ops/MHz |      4 Mb
13_array_fill                  :    2.105 sec |  23.75 MOp/s |   6.49 kOps/MHz |     12 Mb
14_array_range                 :    0.560 sec | 178.56 kOp/s |  48.78  Ops/MHz |     12 Mb
14_array_unset                 :    1.547 sec |  32.31 MOp/s |   8.83 kOps/MHz |     12 Mb
15_loops                       :    1.955 sec | 194.36 MOp/s |  53.09 kOps/MHz |      4 Mb
16_loop_ifelse                 :    1.569 sec |  57.37 MOp/s |  15.67 kOps/MHz |      4 Mb
17_loop_ternary                :    3.066 sec |  29.36 MOp/s |   8.02 kOps/MHz |      4 Mb
18_1_loop_defined_access       :    0.524 sec |  38.20 MOp/s |  10.44 kOps/MHz |      4 Mb
18_2_loop_undefined_access     :    3.680 sec |   5.43 MOp/s |   1.48 kOps/MHz |      4 Mb
19_type_functions              :    1.627 sec |   3.07 MOp/s | 839.30  Ops/MHz |      4 Mb
20_type_conversion             :    1.086 sec |   4.60 MOp/s |   1.26 kOps/MHz |      4 Mb
21_0_loop_exception_none       :    0.030 sec | 133.25 MOp/s |  36.40 kOps/MHz |      4 Mb
21_1_loop_exception_try        :    0.036 sec | 111.55 MOp/s |  30.47 kOps/MHz |      4 Mb
21_2_loop_exception_catch      :    2.125 sec |   1.88 MOp/s | 514.07  Ops/MHz |      4 Mb
22_loop_null_op                :    1.288 sec |  38.83 MOp/s |  10.61 kOps/MHz |      4 Mb
23_loop_spaceship_op           :    1.151 sec |  43.45 MOp/s |  11.87 kOps/MHz |      4 Mb
24_xmlrpc_encode               :    2.653 sec |  75.39 kOp/s |  20.59  Ops/MHz |      4 Mb
25_xmlrpc_decode               :    3.468 sec |   8.65 kOp/s |   2.36  Ops/MHz |      4 Mb
-------------------------------------------------------------------------------------------
Total time:                    :   59.892 sec |  12.36 MOp/s |   3.37 kOps/MHz |
Current PHP memory usage:      :        4 Mb
Peak PHP memory usage:         :   125.26 Mb
</pre>
```
