# Программное обеспечение ГРИД КП для Linux (OpenWRT19.01\Debian 10:AMD64\Debian 10:ARM\Windows)

> для контроллеров ICPDAS LP-8821, LP-8781, MOXA, iRZ, Teleofis, компьютеров с ОС Linux\Windows

Протестировано Buster 64-bit OS (Debian Version 10) only

установка

```con
sudo dpkg -i /tmp/1/tm_cpps_RPi4b64_13633.deb
```

удаление

```con
sudo apt remove tm-cpps
```

## Описание

__ПО ГРИД__ - программный комплекс, который позволяет создавать информационно-управляющие системы (системы телемеханики, диспетчеризации, удаленного контроля, оперативной блокировки) на основе практически любых аппаратных средств. Программное обеспечение состоит из одного испольняемого пакета, в состав которого входят:

__Веб-сервер__, для которого не требуется установка дополнительных пакетов (в т.ч. для ОС Windows)
Исполняемое программное обеспечение, включающее коммуникационные функции, обработки логики и условий, разграничения прав и многого другого
*Среда конфигурирования через Веб-интерфейс
Среда отображения через Веб-интерфейс
Опциональные аппаратно-зависимые пакеты, которые позволяют использовать уникальные возможности устройства совместно с ПО ГРИД

![Структуруная схема ПО ГРИД](/pictures/structure.png)

Структуруная схема ПО ГРИД

### Кросс-платформенность
Программное обеспечение ГРИД КП для Linux \ Windows является кросс-платформенным и предназачнено для установки на любые промышленные контроллеры и компьютеры с операционными системами Linux (OpenWRT) или Windows. Предпочтительно использование операционной системы Linux из-за более высокой надежности и функциональности.

Промышленные контроллеры, как правило, имеют энергоэффективную архитектуру ARM, работающие под управлением ОС Linux или RT-Linux

Комплект программного обеспечения включает в себя:

* Исполнительное программное обеспечение для Linux для целей платформы (ARM, x32, x64)
* Исполнительное программное обеспечение для Windows
* Исполнительное программное обеспечение используется для создание систем АСУТП, телемеханики, оперативной блокировки, подсистем управления в составе более крупным систем, устройств удаленного сбора и диспетчеризации.

### Резервирование устройств
Для увеличения надежности в составе комплекса может находиться два аппаратных комплекта устройств для поддержки "горячего" резервирования. Резервное устройство контроллирует состояние основного через Ethernet и при выходе из строя основного берет на себя его функции.

Основным функциями программного обеспечения ГРИД КП являются:
* сбор телеметрии (ТС и ТИ) состояния основного оборудования энергообъекта прямым вводом и с устройств по различным интерфейсам и протоколам
* сбор аварийно-предупредительной сигнализации (АПТС) энергообъекта и смежных энергообъектов по каналам связи
* обработка получаемой информации по заданным алгоритмам, дорасчет (функциональный вычислитель на Java-подобном языке), преобразование форматов
* хранение и архивирование получаемой информации
* передача телеметрии на уровни диспетчерских центров с гибкой настройкой нумерации и параметров протоколов передачи
* конфигурирование системы через встроенный Web-интерфейс
* визуализация через Web-интерфейс оперативных данных в виде мнемосхемы, таблиц и списка событий
* точность привязки меток времени дискретных сигналов к астрономическому времени не хуже 1 мс., аналоговых сигналов - не хуже 50 мс
* мониторинг протоколов в реальном времени
* ограничение доступа и разграничения прав
* логирование диагностической информации и производимых операций пользователями
* функции горячего резерва и частичного горячего резерва
* задание фильтра дребезга для каждого дискретного сигнала
* синхронизация времени контроллера с точностью до 1 мкс
* создание цепей оперативной блокировки из принимаемых по различным каналам связи телеметрии
* функция удаленного или автоматического управления
* автоматическое диагностирование работоспособности основных модулей и формирование выходных сигналов о сбоях
* поддержка функции ручного ввода значений

### Протоколы приема

* Шина I-8000/I-87000 модулей ICPDAS
* DCON
* Modbus ASCII/RTU/TCP
* МЭК 60870-5-101
* МЭК 60870-5-103 (с функцией приема, хранение и передачи) осциллограмм
* МЭК 60870-5-104
* МЭК 61850-8-1 (GOOSE, MMS)
* FTP
* SNMP
* OPC UA
* DNP3
* MQTT (TLS)
* DLMS/COSEM - протоколы счетчиков электроэнергии нового поколения
* Проприетарные протоколы счетчиков электроэнергии (Меркурий 230 и другие)

### Протоколы передачи

* МЭК 60870-5-101
* МЭК 60870-5-104
* Modbus RTU/TCP
* ГРАНИТ
* CRQ
* МЭК 61850-8-1 (GOOSE, MMS)
* FTP
* SNMP
* OPC UA
* MQTT

### Протоколы синхронизации

* SNTP/NTP в режиме клиента и сервера
* NMEA (GPS) с использованием PPS
* МЭК 60870-5-101/104

![Точность синхронизации контроллера от GPS приемника через ntpd - 11 мкс](/pictures/4.png)

