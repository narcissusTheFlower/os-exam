<h1>Экзамен по предмету "Операционные системы"</h1>
<li>Сдающий: <b>Ващук Лев Александрович</b> 
<li>Группа: <b>з5130902/10202</b> 
<li>Вопрос: <b>"Вопрос 9. Файлы; пайпы (pipes), трубы; сигналы; разделяемая память;
сокет межпроцессное взаимодействие  (IPC)ы.</b> </li>
<hr>
<h2>Файлы</h2>
<b>LINUX</b><br>

Стоит начать с того, что в операционной системе Linux все файлы являются потоком байтов. 
Это позволяет эффективно работать с ними в потоках ввода и вывода, а так же представить любой объект ОС в виде файла.
Например, каждое подключенное устройство внешней периферии в операционной системе Linux может быть найдено в виде файла
в директории  `/dev`.<br> 
Именовать файл можно следуя следующим правилам:

* Длина не больше 256 символов;
* Избегать имен, начинающихся на точку (в некоторых случаях, однако, можно игнорировать это правило);
* Не начинать имена со слешей, звездочек и вопросительных знаков, так как они зарезервированы.
* Можно использовать цифры

Файлы в ОС Linux подразделяются больше на типы, чем на расширения. 
В качестве примера введем команду ``cd ~; ls -la``, которая даст нам следующий вывод:

```
user@pop-os:~$ cd ~; ls -la
total 1012
drwxr-x--- 37 user user   4096 Dec 10 21:44 .
drwxr-xr-x  3 root root   4096 Oct  1 14:26 ..
drwxrwxr-x  2 user user   4096 Oct 28 01:36 .android
drwxrwxr-x  5 user user   4096 Oct  4 20:38 .audacity-data
-rw-------  1 user user  15770 Dec 10 22:44 .bash_history
-rw-r--r--  1 user user    220 Jan  6  2022 .bash_logout
-rw-r--r--  1 user user   3979 Dec  4 17:21 .bashrc
```

Возьмем конкретную строку, например `drwxrwxr-x  5 user user   4096 Oct  4 20:38 .audacity-data` и рассмотрим ее.
Мы можем наблюдать, что файл является директорией `drwxrwxr-x` по первому символу `d` и имеет свои права доступа `rwxrwxr-x`.
Отсюда делаем вывод, что каждый файл в ОС Linux имеет свой **тип** и **права доступа**.<br>

Типы файлов бывают следующие:
* `-` означает файл, который хранит символьные или текстовые данные
* `d` означает каталог, директорию
* `l` означает ссылку, условный ярлык, может быть "мягким" и "твердым"
* `b` означает блочные устройства - вид файла устройств, обеспечивающий интерфейс к устройству
* `c` означает символьные устройства - вид файла устройства, обеспечивающий интерфейс к устройству с возможностью посимвольного обмена информацией<br>

Еще одной интересной особенностью файла `.audacity-data` является точка в начале имени. Эта директория является примером так называемого "дот файла", `dotfile`.
Этот файл хранит в себе конфигурацию какой-либо программы в домашней директории пользователя (и не только), для которого установленна данная программа.<br> 

Одной удобной особенностью таких файлов является их переносимость. Их можно переносить с одной системы на другую, чтобы сохранять настройки для программы, сделанные однажды
и не тратить время на перенастройку.<br>

Помимо всего прочего файл обладает правами доступа, которые можно отобразить как цифрами `777`, так и символами `rwx`, где:

* `r` дает право на чтение
* `w` дает право на изменение
* `x` дает право на исполнение


<h2>Pipes</h2>

Pipe (конвеер) – это однонаправленный канал межпроцессного взаимодействия. <br>
Термин был придуман Дугласом Макилроем для командной оболочки Unix и назван по аналогии с 
трубопроводом. <br>

Конвейеры чаще всего используются в shell-скриптах для связи нескольких команд путем перенаправления 
вывода одной команды (stdout) на вход (stdin) последующей, используя символ конвеера `|`.<br>

Приведу пример из повседневной жизни. Пусть я хочу найти полное имя пакета JDK в своем дистрибутиве:
```
apt list --installed | grep jdk
```
мы получим следующий вывод:
```
user@pop-os:~$ apt list --installed | grep jdk

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

openjdk-11-jre-headless/now 11.0.20.1+1-0ubuntu1~22.04 amd64 [installed,upgradable to: 11.0.21+9-0ubuntu1~22.04]
openjdk-17-doc/now 17.0.8.1+1~us1-0ubuntu1~22.04 all [installed,upgradable to: 17.0.9+9-1~22.04]
openjdk-17-jdk-headless/now 17.0.8.1+1~us1-0ubuntu1~22.04 amd64 [installed,upgradable to: 17.0.9+9-1~22.04]
openjdk-17-jdk/now 17.0.8.1+1~us1-0ubuntu1~22.04 amd64 [installed,upgradable to: 17.0.9+9-1~22.04]
openjdk-17-jre-headless/now 17.0.8.1+1~us1-0ubuntu1~22.04 amd64 [installed,upgradable to: 17.0.9+9-1~22.04]
openjdk-17-jre/now 17.0.8.1+1~us1-0ubuntu1~22.04 amd64 [installed,upgradable to: 17.0.9+9-1~22.04]
openjdk-17-source/now 17.0.8.1+1~us1-0ubuntu1~22.04 all [installed,upgradable to: 17.0.9+9-1~22.04]
```
Я получил все пакеты, которые содержат в своем имени `jdk`. Из этого списка намного проще фильтровать информацию и выбрать тот пакет, который меня интересует. 
Под капотом произошло следующее:
* Команда `apt list --installed` подала на стандартный поток вывода (stdout) результат своей работы;
* Далее `|` перенаправил эту информацию в новую команду `grep jdk`. <br>

Очень важно отметить, что команды исполняются одновременно в своих индивидуальных процессах параллельно, то есть все, что поступает на вывод из 
первой команды (`apt list --installed`) немедленно поступает на вход второй, не дожидаясь завершения предыдущей команды.
Таким образом конвеер обеспечивает асинхронное выполнение с использованием буферизации ввода/вывода. <br>

<h2>IPC</h2>
Inter-process communication - это межпроцессное взаимодействие, обмен данными между потоками одного или разных процессов. <br>

Реализуется посредством механизмов, предоставляемых ядром ОС или процессом, использующим механизмы ОС и реализующим возможности IPC. 

Из механизмов, предоставляемых ОС можно выделить:

* Механизмы обмена сообщениями;
* Механизмы синхронизации;
* Механизмы разделения памяти;
* Механизмы удалённых вызовов (RPC).

Так же IPC делится на методы, указанные в таблице 1.


| Метод                              | Реализуется ОС или процессом |
|------------------------------------| --------- |
| Файл                               | Все ОС |
| Сигнал                             | Большинство ОС |
| Сокет                              | Большинство ОС |
| Канал                              | Все ОС, совместимые со стандартом POSIX |
| Именованный канал                  | Все ОС, совместимые со стандартом POSIX |
| Неименованный канал канал          | Все ОС, совместимые со стандартом POSIX |
| Семафор                            | Все ОС, совместимые со стандартом POSIX |
| Разделяемая память                 | Все ОС, совместимые со стандартом POSIX |
| Обмен сообщениями (без разделения) | Используется в парадигме MPI, Java RMI, CORBA и других |
| Проецируемый в память файл                             | Все ОС, совместимые со стандартом POSIX |
| Очередь сообщений                            | Большинство ОС |
| Почтовый ящик                           | Некоторые ОС  |

<p align=center>Таблица 1 - методы межпроцессного взаимодействия</p>

<h2>Разделяемая память</h2>
В первую очередь разделяемая память это одно из средств IPC (inter-process communication), суть которого заключается в 
улучшении быстродействия.<br>

Разделенная память по сути своей физическое пространство памяти, которое было создано условным программистом _**(при использовании С языков)**_ для работы с одним
"пулом" информации. Программист может настроить логику работы с такой памятью, чтобы **разные** программы (процессы, потоки) обращались к этой области
памяти и имели возможность манипулировать информацией в данной области без лишних затрат обращения к ядру ОС, как продемонстрированною
на рисунке 1. 


<img src="https://upload.wikimedia.org/wikipedia/commons/4/44/%D0%A0%D0%B0%D0%B7%D0%B4%D0%B5%D0%BB%D1%8F%D0%B5%D0%BC%D0%B0%D1%8F_%D0%BF%D0%B0%D0%BC%D1%8F%D1%82%D1%8C_%28Shared_Memory%29.png">
<p align=center>Рисунок 1 - Визуальная репрезентация разделенной памяти.</p>

Несмотря на то, что тема данного экзамена - это операционные системы, где главенствуют С языки должен отметить, что данный 
механизм на разных языках программирования реализован с некоторыми отличиями. Если на С или С++ программист вручную выделяет память, то на 
Java память регулируется рычагами JVM, а значит и механизмы синхронизации там свои. Должен, однако, отметить, что под капотом используются одни и теже паттерны
управления синхронизацией, например, `Semaphore`ы. <br>

**_Из личного опыта могу сказать, что разделенную память можно приписать к теме многопоточного программирования. Многопоточное программирование способно в разы 
улучшить быстродействие, но внедряет кучу подводных камней, которые, если вовремя не заметить способны неимоверно навредить.
Некоторые проблемы не обнаруживаются ни усиленным Unit/Integration тестированием, ни командами QA инженеров._** <br>

**_И, например, гуру Java считают, что любая программа, которая включает в себя многопоточное программирование изначально сломана
из-за непредсказуемости поведениях потоков, в том числе с разделенной памятью, которую надо усиленно синхронизировать._** 





<h2>Сокеты</h2>
Для меня было сюрпризом, что сокеты относятся к IPC, но действительно, если призадуматься, они помогают обеспечить совместное выполнение
процессов, играя роль эдакого помощника почтальона, где сам почтальон - это протокол передачи данных, а сокет полный адрес
информации, которую надо передать.<br>

И тут я попал в ловушку Джокера, думая, что речь идет о веб-сокетах, когда вероятней всего надо описать
_**Unix domain socket**_. <br>

**_UDS_** или IPC-сокет (сокет межпроцессного взаимодействия) — конечная точка обмена данными, подобная интернет-сокету, 
но не использующая сетевого протокола для взаимодействия (обмена данными). 
Используется в операционных системах, поддерживающих стандарт POSIX, для межпроцессного взаимодействия.<br>

Суть работы UDS такова: UDS используют файловую систему как адресное пространство имен.<br>
Они представляются процессами как **_иноды_** (структура данных в UNIX OС, индексный дескриптор, inode) в файловой системе, пример которой
изображен на рисунке 2.<br>

**_Это позволяет двум различным процессам открывать один и тот же сокет для взаимодействия между собой, где
непосредственный обмен данными идёт не через файловую систему, а через буфера памяти ядра, который, я могу со смелостью предположить,
значительно быстрее, чем файловая система._**

<img src="https://pq.hosting/uploads/mini/base-content/8e/23363772a03e66c70c864ed78a96d7.jpg">

<p align=center>Рисунок 2 - визуализация inode</p>
Кстати, сокеты оперируют потоками байтов, а это, в свою очередь, пересекается в главой про файлы, где я отметил, что все файлы в ОС
Linux являются потоком байтов. Такой подход обеспечивает эффективную передачу данных. Помимо байтовых потоков UDS способны
работать в режиме передачи пакетов (TCP) и датаграмм (UDP).



<h2>Сигналы</h2>







