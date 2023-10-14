---
## Front matter
title: "Лабораторная работа №6"
subtitle: "Информационная безопасность"
author: "Маметкадыров Ынтымак | НПМбд-02-20"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Развить навыки администрирования ОС Linux. Получить первое практическое знакомство с технологией SELinux. Проверить работу SELinux на практике совместно с веб-сервером Apache.

# Теоретическое введение

SELinux (Security-Enhanced Linux) обеспечивает усиление защиты путем внесения изменений как на уровне ядра, так и на уровне пространства пользователя, что превращает ее в действительно «непробиваемую» операционную систему. Впервые эта система появилась в четвертой версии CentOS, а в 5 и 6 версии реализация была существенно дополнена и улучшена. 

SELinux имеет три основных режим работы:

* Enforcing: Режим по-умолчанию. При выборе этого режима все действия, которые каким-то образом нарушают текущую политику безопасности, будут блокироваться, а попытка нарушения будет зафиксирована в журнале.

* Permissive: В случае использования этого режима, информация о всех действиях, которые нарушают текущую политику безопасности, будут зафиксированы в журнале, но сами действия не будут заблокированы.

* Disabled: Полное отключение системы принудительного контроля доступа.

Политика SELinux определяет доступ пользователей к ролям, доступ ролей к доменам и доступ доменов к типам. 

Контекст безопасности — все атрибуты SELinux — роли, типы и домены.
 
Более подробно см. в [@SELinux:bash].

Apache — это свободное программное обеспечение, с помощью которого можно создать веб-сервер. Данный продукт возник как доработанная версия другого HTTP-клиента от национального центра суперкомпьютерных приложений (NCSA).

Для чего нужен Apache сервер:

* чтобы открывать динамические PHP-страницы,

* для распределения поступающей на сервер нагрузки,

* для обеспечения отказоустойчивости сервера,

* чтобы потренироваться в настройке сервера и запуске PHP-скриптов.

Apache является кроссплатформенным ПО и поддерживает такие операционные системы, как Linux, BSD, MacOS, Microsoft, BeOS и другие.

Более подробно см. в [@Apache:bash].

# Выполнение лабораторной работы

Вошли в систему под своей учетной записью и убедились, что SELinux работает в режиме enforcing политики targeted с помощью команд "getenforce" и "sestatus" (рис. [-@fig:001]).
 
![Проверка режима enforcing политики targeted](image/screenshot_1.png){ #fig:001 width=110% }
 
Обратились с помощью браузера к веб-серверу, запущенному на нашем компьютере, и убедились, что последний работает с помощью команды "service httpd status" (рис. [-@fig:002]).

![Проверка работы веб-сервера](image/screenshot_2.png){ #fig:002 width=110% }

С помощью команды "ps auxZ | grep httpd" определили контекст безопасности веб-сервера Apache - httpd_t (рис. [-@fig:003]).

![Контекст безопасности веб-сервера Apache](image/screenshot_3.png){ #fig:003 width=110% }

Посмотрели текущее состояние переключателей SELinux для Apache с помощью команды "sestatus -bigrep httpd", многие из переключателей находятся в положении "off" (рис. [-@fig:004]).

![Текущее состояние переключателей SELinux](image/screenshot_4.png){ #fig:004 width=110% }

Посмотрели статистику по политике с помощью команды "seinfo". Множество пользователей - 8, ролей - 14, типов 4995 (рис. [-@fig:005]).

![Статистика по политике](image/screenshot_5.png){ #fig:005 width=110% }

С помощью команды "ls -lZ /var/www" посмотрели файлы и поддиректории, находящиеся в директории /var/www. Используя команду "ls -lZ /var/www/html", определили, что в данной директории файлов нет. Только владелец/суперпользователь может создавать файлы в директории /var/www/html (рис. [-@fig:006]).

![Просмотр файлов и поддиректориий в директории /var/www](image/screenshot_6.png){ #fig:006 width=110% }

От имени суперпользователя создали html-файл /var/www/html/test.html. Контекст созданного файла - httpd_sys_content_t (рис. [-@fig:007]).

![Создание файла /var/www/html/test.html](image/screenshot_7.png){ #fig:007 width=110% }

Обратились к файлу через веб-сервер, введя в браузере адрес "http://127.0.0.1/test.html". Файл был успешно отображен (рис. [-@fig:008]).

![Обращение к файлу через веб-сервер](image/screenshot_8.png){ #fig:008 width=110% }

Изучив справку man httpd_selinux, выяснили, что для httpd определены следующие контексты файлов: httpd_sys_content_t, httpd_sys_script_exec_t, httpd_sys_script_ro_t, httpd_sys_script_rw_t, httpd_sys_script_ra_t, httpd_unconfined_script_exec_t. Контекст моего файла - httpd_sys_content_t (в таком случае содержимое должно быть доступно для всех скриптов httpd и для самого демона). Изменили контекст файла на samba_share_t командой "sudo chcon -t samba_share_t /var/www/html/test.html" и проверили, что контекст поменялся (рис. [-@fig:009]).

![Изменение контекста](image/screenshot_9.png){ #fig:009 width=110% }

Попробовали еще раз получить доступ к файлу через веб-сервер, введя в браузере адрес "http://127.0.0.1/test.html" и получили сообщение об ошибке (т.к. к установленному ранее контексту процесс httpd не имеет доступа) (рис. [-@fig:010]).

![Обращение к файлу через веб-сервер](image/screenshot_10.png){ #fig:010 width=110% }

Командой "ls -l /var/www/html/test.html" убедились, что читать данный файл может любой пользователь. Просмотрели системный лог-файл веб-сервера Apache командой "sudo tail /var/log/messages", отображающий ошибки (рис. [-@fig:011]).

![Просмотр log-файла](image/screenshot_11.png){ #fig:011 width=110% }

В файле /etc/httpd/conf/httpd.conf заменили строчку "Listen 80" на "Listen 81", чтобы установить веб-сервер Apache на прослушивание TCP-порта 81 (рис. [-@fig:012]).

![Установка веб-сервера Apache на прослушивание TCP-порта 81](image/screenshot_12.png){ #fig:012 width=110% }

Перезапускаем веб-сервер Apache и анализирует лог-файлы командой "tail -nl /var/log/messages" (рис. [-@fig:013]).

![Перезапуск веб-сервера и анализ лог-файлов](image/screenshot_13.png){ #fig:013 width=110% }

Выполнили команду "semanage port -a -t http_port_t -р tcp 81" и убедились, что порт TCP-81 установлен. Проверили список портов командой "semanage port -l | grep http_port_t", убедились, что порт 81 есть в списке и запускаем веб-сервер Apache снова (рис. [-@fig:014]).

![Проверка установки порта 81](image/screenshot_14.png){ #fig:014 width=110% }

Вернули контекст "httpd_sys_cоntent_t" файлу "/var/www/html/test.html" командой "chcon -t httpd_sys_content_t /var/www/html/test.html" (рис. [-@fig:015]) и после этого попробовали получить доступ к файлу через веб-сервер, введя адрес "http://127.0.0.1:81/test.html", в результате чего увидели содежимое файла - слово "test" (рис. [-@fig:015]).

![Возвращение исходного контекста файлу и обращение к файлу через веб-сервер](image/screenshot_15.png){ #fig:015 width=110% }

Исправили обратно конфигурационный файл apache, вернув "Listen 80". Попытались удалить привязку http_port к 81 порту командой "semanage port -d -t http_port_t -p tcp 81", но этот порт определен на уровне политики, поэтому его нельзя удалить (рис. [-@fig:016]).

![Возвращение Listen 80 и попытка удалить порт 81](image/screenshot_16.png){ #fig:016 width=110% }

Удалили файл "/var/www/html/test.html" командой "rm /var/www/html/test.html" (рис. [-@fig:017]).

![Удаление файла test.html](image/screenshot_17.png){ #fig:017 width=110% }

# Выводы

В ходе выполнения данной лабораторной работы мы развили навыки администрирования ОС Linux, получили первое практическое знакомство с технологией SELinux и проверили работу SELinux на практике совместно с веб-сервером Apache.

# Список литературы{.unnumbered}

::: {#refs}
:::


