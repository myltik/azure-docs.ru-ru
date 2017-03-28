---
title: "Подключение компьютеров Linux к Azure Log Analytics | Документация Майкрософт"
description: "Log Analytics позволяет собирать и обрабатывать данные, созданные компьютерами Linux."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: fba4e68e78b8267ff2413f94d5ca5066325f9c76
ms.lasthandoff: 02/28/2017


---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Подключение компьютеров Linux к Log Analytics
Log Analytics позволяет собирать и обрабатывать данные, созданные компьютерами Linux. Добавив данные, собранные с компьютера Linux, в OMS, вы можете управлять системами Linux и решениями контейнера, например Docker, практически из любого места. Источники данных могут находиться в локальном центре обработки данных в качестве физических серверов, на виртуальных компьютерах в облачной службе, такой как Amazon Web Services (AWS) или Microsoft Azure, или даже на настольном ноутбуке. Кроме того, OMS аналогичным образом собирает данные с компьютеров Windows. Так что это решение поддерживает гибридную ИТ-среду.

С помощью Log Analytics в OMS можно просматривать данные из всех этих источников и управлять ими на едином портале управления. Поэтому вам не нужно отслеживать решение с помощью разных систем, его использование упрощается, а все необходимые данные можно экспортировать в любое существующее решение или систему бизнес-аналитики.

Эта статья представляет собой краткое руководство, которое поможет выполнить сбор данных и управлять ими на компьютерах Linux с помощью агента OMS для Linux. Дополнительные технические сведения, такие как конфигурация прокси-сервера, сведения о метриках CollectD и пользовательских источниках данных JSON, см. в [обзоре агента OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) и [полной документации по агенту OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) в GitHub.

В настоящее время с компьютеров Linux можно собирать данные следующих типов:

* Метрики производительности
* события системного журнала;
* оповещения Nagios и Zabbix;
* метрики производительности, данные инвентаризации и журналов контейнера Docker.

## <a name="supported-linux-versions"></a>Поддерживаемые версии Linux
x86 и x64 являются официально поддерживаемыми версиями в различных дистрибутивах Linux. Однако агент OMS для Linux может выполняться и на других дистрибутивах, помимо следующих:

* выпуски Amazon Linux 2012.09–2015.09;
* CentOS Linux 5, 6 и 7;
* Oracle Linux 5, 6 и 7;
* сервер Red Hat Enterprise Linux 5, 6 и 7;
* Debian GNU/Linux 6, 7 и 8;
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10;
* SUSE Linux Enterprise Server 11 и 12.

## <a name="oms-agent-for-linux"></a>Агент OMS для Linux
Агент Operations Management Suite для Linux состоит из нескольких пакетов. Файл выпуска содержит указанные ниже пакеты, к которым можно получить доступ, запустив пакет оболочки с помощью команды `--extract`.

| **Package** | **Версия** | **Описание** |
| --- | --- | --- |
| omsagent |1.1.0 |Агент Operations Management Suite для Linux |
| omsconfig |1.1.1 |Агент конфигурации для агента OMS |
| omi |1.0.8.3 |OMI (Open Management Infrastructure) — упрощенная версия сервера CIM |
| scx |1.6.2 |Поставщики OMI CIM для метрик производительности операционной системы |
| apache-cimprov |1.0.0 |Поставщик мониторинга производительности сервера Apache HTTP Server для OMI. Устанавливается только при обнаружении сервера Apache HTTP Server |
| mysql-cimprov |1.0.0 |Поставщик мониторинга производительности сервера MySQL для OMI. Устанавливается только при обнаружении сервера MySQL или MariaDB |
| docker-cimprov |0.1.0 |Поставщик Docker для OMI. Устанавливается только при обнаружении Docker |

### <a name="additional-installation-artifacts"></a>Дополнительные артефакты установки
После установки пакетов агента OMS для Linux применяются системные изменения конфигурации, приведенные ниже. Эти артефакты удаляются после удаления пакета omsagent.

* Создается непривилегированный пользователь с именем `omsagent` . Это учетная запись, под которой запускается управляющая программа omsagent.
* Создается файл sudoers с директивами include в каталоге /etc/sudoers.d/omsagent. Таким образом пользователь omsagent получает право на перезапуск управляющих программ syslog и omsagent. Если директивы include sudo не поддерживаются в установленной версии sudo, эти записи будут записываться в каталог /etc/sudoers.
* Конфигурация системного журнала настраивается для перенаправления подмножества событий агенту. Дополнительные сведения см. в разделе о **настройке сбора данных** ниже.

### <a name="linux-data-collection-details"></a>Сведения о сборе данных Linux
В следующей таблице содержатся методы сбора данных и другие связанные сведения.

| источник | Direct Agent | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![Да](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 минута |
| Nagios |![Да](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |При получении |
| syslog |![Да](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |Из хранилища Azure — 10 минут, из агента — при получении |
| Счетчики производительности Linux |![Да](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |По расписанию, не менее 10 секунд |
| Отслеживание изменений |![Да](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Нет](./media/log-analytics-linux-agents/oms-bullet-red.png) |Ежечасно |

### <a name="package-requirements"></a>Требования к пакетам
| **Требуемый пакет** | **Описание** | **Минимальная версия** |
| --- | --- | --- |
| Glibc |Библиотека C GNU |2.5-12 |
| Openssl |Библиотеки OpenSSL |0.9.8e или 1.0 |
| Curl |Веб-клиент cURL |7.15.5 |
| Python-ctypes |Библиотеки функций |Недоступно |
| PAM |Подключаемые модули аутентификации |Недоступно |

> [!NOTE]
> Для сбора сообщений системного журнала требуется rsyslog или syslog-ng. Управляющая программа syslog по умолчанию не поддерживается для сбора событий системного журнала в Red Hat Enterprise Linux версии 5, CentOS и Oracle Linux (sysklog). Чтобы собирать данные системного журнала из дистрибутивов этих версий, требуется установить и настроить управляющую программу rsyslog, которая заменит sysklog.
>
>

## <a name="quick-install"></a>Быстрая установка
Выполните следующие команды, чтобы скачать omsagent, проверить контрольную сумму, а также установить и внедрить агент. Команды предназначены для 64-разрядных систем. Идентификатор рабочей области и первичный ключ можно найти на портале OMS в разделе **Параметры** на вкладке **Подключенные источники**.

![сведения о рабочей области](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Существует множество других методов установки и обновления агента. Дополнительные сведения об этих методах см. в разделе об [установке агента OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).

Кроме того, можно просмотреть [видеоруководство Azure](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Выбор метода сбора данных Linux
Выбор типов данных для сбора зависит от того, хотите ли вы использовать портал OMS или изменять различные файлы конфигурации непосредственно в клиентах Linux. Если вы решили использовать портал, конфигурация автоматически отправляется на все клиентские компьютеры Linux. Если требуются различные конфигурации для различных клиентов Linux, клиентские файлы потребуется изменять по отдельности или воспользоваться альтернативным вариантом, например средствами PowerShell DSC, Chef или Puppet.

События системного журнала и счетчиков производительности, данные которых нужно собирать, можно указать с помощью файлов конфигурации на компьютерах Linux. *Выбрав настройку сбора данных путем изменения файлов конфигурации агента, следует отключить централизованную конфигурацию.*  Ниже представлены инструкции по настройке сбора данных в файлах конфигурации агента, а также по отключению центральной конфигурации для всех агентов OMS для Linux или отдельных компьютеров.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Отключение управления OMS для отдельного компьютера Linux
Централизованный сбор данных конфигурации для отдельного компьютера Linux отключается с помощью скрипта OMS_MetaConfigHelper.py. Это может быть полезно, если подмножеству компьютеров требуется специальная конфигурация.

Чтобы отключить централизованную конфигурацию, выполните следующую команду:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Чтобы включить централизованную конфигурацию, выполните следующую команду:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Счетчики производительности Linux
Счетчики производительности Linux функционируют подобно счетчикам производительности Windows. Чтобы добавить и настроить их, можно использовать шаги, приведенные ниже. После добавления счетчиков производительности в OMS сбор данных для них осуществляется каждые 30 секунд.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Добавление счетчиков производительности Linux в OMS
1. Чтобы настроить агенты OMS для Linux с помощью портала OMS, можно добавить счетчики производительности Linux на странице "Параметры" и щелкнуть **Данные**.  
2. На странице **Параметры** в разделе **Данные** щелкните **Счетчики производительности Linux** и выберите или введите имя счетчика, который нужно добавить.  
    ![Данные](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Если полное имя счетчика неизвестно, можно начать вводить часть имени, после чего отобразится список доступных счетчиков. Найдя счетчик, который требуется добавить, щелкните его имя в списке и значок плюса.
4. После добавления счетчика он появляется в списке счетчиков, выделенный цветной линией.
5. По умолчанию установлен флажок **Apply below configuration to my machines** (Применить конфигурации ниже к моим компьютерам). Если нужно отключить отправку данных конфигурации, снимите этот флажок.
6. Изменив счетчики производительности, щелкните **Сохранить** в нижней части страницы для окончательной регистрации этих изменений. Затем выполненные изменения конфигурации отправляются во все агенты OMS для Linux, зарегистрированные в OMS. Обычно это занимает 5 минут.

### <a name="configure-linux-performance-counters-in-oms"></a>Настройка счетчиков производительности Linux в OMS
Для каждого счетчика производительности Windows можно выбрать конкретный экземпляр. Однако в случае счетчиков производительности Linux любой экземпляр выбранного счетчика применяется ко всем дочерним счетчикам родительского счетчика. В следующей таблице представлены распространенные экземпляры для счетчиков производительности Linux и Windows.

| **Имя экземпляра** | **Значение** |
| --- | --- |
| \_Total |Общее количество всех экземпляров |
| \* |Все экземпляры |
| (/&#124;/var) |Сопоставление экземпляров с именем / или/var |

Аналогичным образом, интервал выборки, выбранный для родительского счетчика, применяется ко всем дочерним счетчикам. Другими словами, все интервалы выборки и экземпляры дочерних счетчиков связаны друг с другом.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Добавление и настройка метрик производительности в Linux
Собираемые метрики производительности определяются конфигурацией в файле /etc/opt/microsoft/omsagent/conf/omsagent.conf. Дополнительные сведения о доступных классах и метриках для агента OMS для Linux см. [здесь](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics).

Каждый объект или категорию метрики производительности для сбора следует определить в файле конфигурации в качестве одного элемента `<source>` . Синтаксис соответствует шаблону ниже.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Для этого элемента можно настроить следующие параметры:

* **Object\_name** — имя объекта в коллекции.
* **Instance\_regex** — *регулярное выражение*, определяющее экземпляры, которые следует собирать. Значение `.*` указывает все экземпляры. Для сбора метрик процессора только для экземпляра \_Total можно указать `_Total`. Для сбора метрик обработки только для экземпляра crond или sshd можно указать `(crond|sshd)`.
* **Counter\_name\_regex** — *регулярное выражение*, определяющее счетчики для сбора (для объекта). Для сбора всех счетчиков для объекта укажите `.*`. Для сбора только счетчиков области подкачки для объекта памяти можно указать `.+Swap.+`
* **Interval**— частота сбора счетчиков объекта.

Конфигурация по умолчанию для метрики производительности:

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Включение счетчиков производительности MySQL с помощью команд Linux
Если при установке пакета omsagent на компьютере обнаружен сервер MySQL или MariaDB, поставщик мониторинга производительности устанавливается автоматически для сервера MySQL. Этот поставщик подключается к локальному серверу MySQL или MariaDB, чтобы предоставить статистику производительности. Чтобы поставщик смог получить доступ к серверу MySQL, необходимо настроить учетные данные пользователя MySQL.

Чтобы определить учетную запись пользователя по умолчанию для сервера MySQL для localhost, используйте в качестве примера команду ниже.

> [!NOTE]
> Файл учетных данных должен быть доступен для чтения для учетной записи omsagent. Рекомендуется выполнить команду mycimprovauth, используя учетную запись omsgent.
>
>

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Кроме того, необходимые учетные данные MySQL можно указать в файле. Для этого создайте файл /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Дополнительные сведения об управлении учетными данными MySQL для мониторинга с помощью файла mysql-auth см. в разделе [Управление учетными данными для мониторинга MySQL в файле аутентификации](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Дополнительные сведения о разрешениях объектов, необходимых пользователю MySQL для сбора данных производительности сервера MySQL, см. в разделе [Разрешения базы данных, необходимые для счетчиков производительности MySQL](#database-permissions-required-for-mysql-performance-counters).

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Включение счетчиков производительности сервера Apache HTTP Server с помощью команд Linux
Если при установке пакета omsagent на компьютере обнаружен сервер Apache HTTP Server, для него автоматически устанавливается поставщик мониторинга производительности. Этот поставщик использует модуль Apache, который нужно загрузить на сервер Apache HTTP Server для доступа к данным производительности.

Модуль можно загрузить с помощью следующей команды:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Чтобы выгрузить модуль мониторинга Apache, выполните следующую команду:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Просмотр данных производительности с помощью Log Analytics
1. На портале Operations Management Suite щелкните плитку поиска по журналам.
2. В строке поиска введите `* (Type=Perf)` , чтобы просмотреть все счетчики производительности.

Так как OMS также собирает данные счетчика производительности Windows, следует ограничить область поиска данными Linux. В следующем примере приведены данные о производительности для сервера Linux с именем Chorizo21.

```
Type=Perf Computer=chorizo*
```

![пример сервера, отображающийся на странице результатов поиска](./media/log-analytics-linux-agents/oms-perfsearch01.png)

В результатах можно щелкнуть **Метрики** , чтобы просмотреть счетчики, для которых осуществлялся сбор данных. Данные в реальном времени отображаются в виде графиков для каждого счетчика.

![Метрики](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
Системный журнал — это протокол ведения журнала событий, аналогичный журналу событий Windows. При отображении в OMS они работают одинаково.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Добавление нового устройства системного журнала Linux в OMS
1. На странице **Параметры** на вкладке **Данные** щелкните раздел **Системный журнал** и слева от значка плюса введите имя устройства системного журнала, которое нужно добавить.
    ![Системный журнал Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. Если полное имя устройства неизвестно, можно начать вводить часть имени, после чего отобразится список доступных устройств системного журнала. Чтобы добавить найденное устройство системного журнала, щелкните его имя в списке, а затем — значок плюса.
3. После добавления устройства оно появляется в списке устройств, выделенное цветной линией. Затем выберите степени серьезности (категории сведений устройства системного журнала) для сбора.
4. В нижней части страницы щелкните **Сохранить** для окончательной регистрации этих изменений. Затем выполненные изменения конфигурации отправляются во все агенты OMS для Linux, зарегистрированные в OMS. Обычно это занимает 5 минут.

### <a name="configure-linux-syslog-facilities-in-linux"></a>Настройка устройств системного журнала Linux в Linux
События системного журнала отправляются из управляющей программы syslog, например rsyslog или syslog-ng, в локальный порт, который прослушивает агент. Порт по умолчанию — 25224. При установке агента применяется конфигурация системного журнала по умолчанию. Ее можно найти в следующих каталогах:

rsyslog — /etc/rsyslog.d/rsyslog-oms.conf;

syslog-ng — /etc/syslog-ng/syslog-ng.conf.

При конфигурации по умолчанию передаются события системного журнала агента OMS всех устройств с уровнем серьезности "Предупреждение" или выше.

> [!NOTE]
> При изменении конфигурации системного журнала требуется перезапустить управляющую программу syslog, чтобы изменения вступили в силу.
>
>

Ниже приведена конфигурация по умолчанию системного журнала для агента OMS для Linux для OMS.

#### <a name="rsyslog"></a>При использовании rsyslog:
```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>При использовании syslog-ng:
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Просмотр всех событий системного журнала с помощью Log Analytics
1. На портале Operations Management Suite щелкните плитку **поиска по журналам** .
2. В группе **Log Management** (Управление журналом) выберите предопределенный поиск по системному журналу, а затем — системный журнал, для которого требуется выполнить такой поиск.

В этом примере представлены все события системного журнала.

![события системного журнала, отображаемые на странице поиска по журналам](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Теперь вы можете подробно рассмотреть результаты поиска.

## <a name="linux-alerts"></a>Оповещения Linux
При использовании Nagios или Zabbix для управления компьютерами Linux OMS может получать оповещения, созданные с помощью этих средств. Однако сейчас невозможно настроить данные входящих оповещений на портале OMS. Вместо этого для отправки оповещений в OMS необходимо изменить файл конфигурации.

### <a name="collect-alerts-from-nagios"></a>Сбор оповещений из Nagios
Для сбора оповещений сервера Nagios необходимо внести следующие изменения в конфигурацию:

1. Предоставьте пользователю **omsagent** доступ для чтения к файлу журнала Nagios (например, /var/log/nagios/nagios.log/var/log/nagios/nagios.log). Если файл nagios.log принадлежит группе **nagios**, пользователя **omsagent** можно добавить в группу **nagios**.

    ```
    sudo usermod –a -G nagios omsagent
    ```
2. Измените файл конфигурации omsagent.conf (etc/opt/microsoft/omsagent/conf/omsagent.conf). Для этого введите следующие записи в незакомментированном виде:

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```
3. Перезапустите управляющую программу omsagent:

    ```
    sudo service omsagent restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Сбор оповещений из Zabbix
Для сбора оповещений с сервера Zabbix выполните такие же действия, как и для Nagios выше, однако укажите имя пользователя и пароль в *текстовом виде*. Это не очень удобно, но в скором времени это требование может измениться. Чтобы устранить эту проблему, рекомендуется создать пользователя и предоставить ему разрешение только для мониторинга.

Раздел файла конфигурации omsagent.conf (etc/opt/microsoft/omsagent/conf/omsagent.conf) для Zabbix должен выглядеть примерно следующим образом.

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Просмотр оповещений с использованием поиска Log Analytics
После настройки компьютеров Linux для отправки оповещений в OMS можно выбрать несколько простых запросов на поиск по журналу для просмотра оповещений. В следующем примере запроса на поиск возвращаются все записанные созданные оповещения. Например, если в ИТ-инфраструктуре возникает какая-либо проблема, ее источник можно определить по результатам в указанном ниже примере запроса. Область исследования можно ограничить, просмотрев подробные сведения об оповещениях исходной системы. Преимущество заключается в том, что вам не нужно сначала просматривать различные системы управления при условии, что оповещения отправляются в OMS. Поиск можно начать прямо в этом решении.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Просмотр всех событий Nagios с помощью Log Analytics
1. На портале Operations Management Suite щелкните плитку **поиска по журналам** .
2. На панели запросов введите следующий запрос на поиск:

    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![оповещения Nagios, отображаемые на странице поиска по журналам](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Просмотрев результаты поиска, можно ознакомиться с дополнительными сведениями, например с *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Просмотр всех событий Zabbix с помощью Log Analytics
1. На портале Operations Management Suite щелкните плитку **поиска по журналам** .
2. На панели запросов введите следующий запрос на поиск:

    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![оповещения Zabbix, отображаемые на странице поиска по журналам](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Просмотрев результаты поиска, можно ознакомиться с дополнительными сведениями, например с *AlertName*.

## <a name="compatibility-with-system-center-operations-manager"></a>Совместимость с System Center Operations Manager
Агент OMS для Linux совместно использует двоичные файлы агента с агентом System Center Operations Manager. При установке агента OMS для Linux в системе под управлением Operations Manager пакеты OMI и SCX на компьютере обновляются до более новой версии. Агент OMS для Linux и System Center 2012 R2 совместимы. Тем не менее **System Center 2012 SP1 и более ранние версии не совместимы с агентом OMS для Linux.**

> [!NOTE]
> Если агент OMS для Linux устанавливается на компьютер, которым в настоящее время не управляет Operations Manager, но такое управление требуется в будущем, нужно изменить конфигурацию OMI до обнаружения компьютера. **Не нужно выполнять этот шаг, если агент Operations Manager установлен до агента OMS для Linux.**
>
>

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Обеспечение взаимодействия агента OMS для Linux с Operations Manager
1. Измените файл /etc/opt/omi/conf/omiserver.conf.
2. Укажите для **httpsport=** номер порта 1270. Таким образом — `httpsport=1270`
3. Перезапустите сервер OMI, выполнив следующую команду:

    ```
    service omiserver restart or systemctl restart omiserver
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>Разрешения базы данных, необходимые для счетчиков производительности MySQL
Чтобы предоставить разрешения пользователю мониторинга MySQL, у пользователя, предоставляющего разрешения, должна быть привилегия с параметром GRANT, а также предоставляемая привилегия.

Чтобы пользователь MySQL смог возвращать данные о производительности, ему потребуется доступ к следующим запросам:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

В дополнение к этим запросам пользователю MySQL необходим доступ с разрешением SELECT для следующих таблиц по умолчанию:

* information_schema;
* mysql

Эти привилегии можно предоставить, выполнив следующие команды:

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Управление учетными данными для мониторинга MySQL в файле аутентификации
В следующих разделах представлены инструкции по управлению учетными данными MySQL.

### <a name="configure-the-mysql-omi-provider"></a>Настройка поставщика OMI MySQL
Поставщику OMI MySQL требуется предварительно настроенный пользователь MySQL и установленные клиентские библиотеки MySQL, чтобы запрашивать сведения о производительности и состоянии из экземпляра MySQL.

### <a name="mysql-omi-authentication-file"></a>Файл аутентификации OMI MySQL
Поставщик OMI MySQL использует файл аутентификации, чтобы определить адрес привязки и порт, который прослушивает экземпляр MySQL, а также учетные данные, которые нужно использовать для сбора метрик. Во время установки поставщик OMI MySQL проверит файлы конфигурации MySQL my.cnf (расположение по умолчанию) для адреса привязки и порта и частично укажет файл аутентификации OMI MySQL.

Чтобы завершить мониторинг экземпляра сервера MySQL, добавьте предварительно созданный файл аутентификации OMI MySQL в соответствующий каталог.

### <a name="authentication-file-format"></a>Формат файла аутентификации
Файл аутентификации OMI MySQL — текстовый файл, содержащий следующие сведения:

* Порт
* адрес привязки;
* имя пользователя MySQL;
* пароль в кодировке Base64.

Файл аутентификации OMI MySQL предоставляет привилегии только для чтения и записи для пользователя Linux, который создал его.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Файл аутентификации OMI MySQL по умолчанию содержит экземпляр по умолчанию и номер порта в зависимости от доступных сведений и данных, полученных в результате анализа найденного файла конфигурации MySQL.

Экземпляр по умолчанию позволяет упростить управление несколькими экземплярами MySQL на одном узле Linux. Он обозначается экземпляром с портом 0. Все добавленные экземпляры наследуют свойства, заданные в экземпляре по умолчанию. Например, при добавлении экземпляра MySQL, который прослушивает порт 3308, для его мониторинга будет использоваться адрес привязки, имя пользователя и пароль в кодировке Base64 экземпляра по умолчанию. Если экземпляр на порте 3308 привязан к другому адресу и использует те же имя пользователя и пароль MySQL, необходимо переопределить только адрес привязки, а другие свойства будут унаследованы.

Содержание файлов аутентификации выглядит, как показано ниже.

Экземпляр по умолчанию и экземпляр с портом 3308:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Экземпляр по умолчанию и экземпляр с портом 3308 и другим паролем в кодировке Base 64:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **Свойство** | **Описание** |
| --- | --- |
| Порт |Это текущий порт, который прослушивает экземпляр MySQL.  Порт 0 означает, что для экземпляра по умолчанию используются следующие свойства. |
| адрес привязки; |Это текущий адрес привязки MySQL. |
| Имя пользователя |Это имя пользователя MySQL, которое нужно использовать для мониторинга экземпляра сервера MySQL. |
| пароль в кодировке Base64. |Это пароль пользователя мониторинга MySQL в кодировке Base64. |
| Автоматическое обновление |При обновлении поставщик OMI MySQL повторно выполнит поиск изменений в файле my.cnf и перезапишет файл аутентификации OMI MySQL. Задайте для этого флага значение true или false в зависимости от необходимости обновления файла аутентификации OMI MySQL. |

#### <a name="authentication-file-location"></a>Расположение файла аутентификации
Файл аутентификации OMI MySQL должен называться mysql-auth и находиться в следующем расположении:

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

Файл (и каталог auth/omsagent) должен принадлежать пользователю omsagent.

## <a name="agent-logs"></a>Журналы агента
Журналы агента OMS для Linux находятся в следующем каталоге:

/var/opt/microsoft/omsagent/log/

Журналы агента OMS для Linux для программы omsconfig (конфигурация агента) расположены в следующем каталоге:

/var/opt/microsoft/omsconfig/log/

Журналы для компонентов OMI и SCX (предоставляют данные метрики производительности) расположены в следующих каталогах:

/var/opt/omi/log/ и /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Устранение неполадок, связанных с агентом OMS для Linux
Используйте следующие сведения для диагностики и устранения типичных неполадок.

Если предоставленные в этом разделе сведения не помогут, для решения проблем также можно использовать следующие источники:

* Клиенты с поддержкой Premier могут обратиться в службу поддержки [здесь](https://premier.microsoft.com/).
* Клиенты с соглашением на поддержку Azure могут обратиться в службу поддержки на [портале Azure](https://manage.windowsazure.com/?getsupport=true).
* Можно [сообщить о проблеме в GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Можно воспользоваться форумом обратной связи для получения предложений и создания отчета об ошибках ([http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)).

### <a name="important-log-locations"></a>Важные расположения журналов
| Файл | Путь |
| --- | --- |
| Файл журнала агента OMS для Linux |`/var/opt/microsoft/omsagent/log/omsagent.log ` |
| Файл журнала конфигурации агента OMS |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>Важные файлы конфигурации
| Категория | Расположение файла |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf`, `/etc/rsyslog.conf` или `/etc/rsyslog.d/95-omsagent.conf` |
| Данные производительности, Nagios, Zabbix, выходные данные OMS и общая конфигурация агента |`/etc/opt/microsoft/omsagent/conf/omsagent.conf` |
| Дополнительные конфигурации |`/etc/opt/microsoft/omsagent/conf.d/*.conf` |

> [!NOTE]
> Если конфигурация портала OMS включена, при редактировании файлы конфигурации счетчиков производительности и системных журналов переопределяются. Конфигурацию можно отключить для всех узлов (полностью) на портале OMS или для одного узла с помощью следующей команды.
>
>

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Включение ведения журнала отладки
Чтобы включить ведение журнала отладки, можно использовать подключаемый модуль выходных данных OMS и подробные выходные данные.

#### <a name="oms-output-plugin"></a>Подключаемый модуль выходных данных OMS
Подключаемый модуль позволяет указывать уровни ведения журнала для разных входных и выходных данных через FluentD. Чтобы определить для выходных данных OMS другой уровень ведения журнала, измените общую конфигурацию агента в файле `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

В нижней части файла конфигурации, измените значение свойства `log_level` с `info` на `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Ведение журнала отладки позволяет просматривать групповые отправки данных в службу OMS по типу, количеству элементов данных и времени, затраченному на отправку.

*Пример включенного журнала отладки.*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Подробные выходные данные
Вместо использования подключаемого модуля выходных данных OMS элементы данных можно отправлять непосредственно в `stdout`, который отображается в файле журнала агента OMS для Linux.

В файле общей конфигурации агента OMS в разделе `/etc/opt/microsoft/omsagent/conf/omsagent.conf` закомментируйте подключаемый модуль выходных данных OMS, добавив в начало каждой строки символ `#`.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

В нижней части подключаемого модуля удалите комментарий в следующем разделе, удалив в начале каждой строки символ `#`.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>Перенаправленные сообщения системного журнала не отображаются в журнале
#### <a name="probable-causes"></a>Возможные причины
* В конфигурации сервера Linux не включен сбор данных об отправленных средствах и (или) данных об уровнях ведения журнала.
* Системный журнал не перенаправляется на сервер Linux должным образом.
* Количество сообщений, перенаправленных за одну секунду, слишком большое и агент OMS для Linux не может их обработать.

#### <a name="resolutions"></a>Способы устранения
* Настройте на портале OMS все средства и правильные уровни ведения журнала, необходимые для системного журнала:
  * **OMS Portal (Портал OMS) > Параметры > Данные > Системный журнал**.
* Настройте получение перенаправленных сообщений в управляющих программах обмена сообщениями системных журналов (`rsyslog`, `syslog-ng`).
* Отключите блокировку сообщений в параметрах брандмауэра на сервере системного журнала.
* Отправьте смоделированное сообщение системного журнала на портал OMS, используя команду `logger`, например:
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Проблемы с подключением к OMS при использовании прокси-сервера
#### <a name="probable-causes"></a>Возможные причины
* Во время установки и настройки агента указан неправильный прокси-сервер.
* Конечные точки службы OMS не включены в список разрешенных в вашем центре обработки данных.

#### <a name="resolutions"></a>Способы устранения
* Переустановите агент OMS для Linux, используя следующую команду с включенным параметром `-v`. За счет этого подробные выходные данные агента могут подключиться к службе OMS через прокси-сервер.
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * Просмотрите сведения для прокси-сервера OMS в разделе [Configuring the agent for use with an HTTP proxy server](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server) (Настройка агента для использования с прокси-сервером HTTP).
* Добавьте следующие конечные точки службы OMS в список разрешенных.

| Ресурс агента | порты; |
| --- | --- |
| &#42;.ods.opinsights.azure.com |Порт 443 |
| &#42;.oms.opinsights.azure.com |Порт 443 |
| ods.systemcenteradvisor.com |Порт 443 |
| &#42;.blob.core.windows.net/ |Порт 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>При подключении отобразилась ошибка с кодом 403
#### <a name="probable-causes"></a>Возможные причины
* На сервере Linux установлено неправильное время или дата.
* Используется неправильный идентификатор или ключ рабочей области.

#### <a name="resolution"></a>Способы устранения:
* Проверьте время на вашем сервере Linux с помощью команды `date`. Если время отличается больше или меньше чем на 15 минут от текущего времени, при подключении произойдет сбой. Чтобы исправить это, обновите дату и (или) часовой пояс на сервере Linux.
* Агент OMS для Linux последней версии отправляет пользователям уведомление, если сбой подключения связан с разницей во времени.
* Повторно установите подключение, используя правильный идентификатор и ключ рабочей области. Дополнительные сведения см. в разделе [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Подключение с помощью командной строки).

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>После подключения в файле журнала отображается ошибка с кодом 500 или 404
Это известная проблема, возникающая во время передачи данных Linux в рабочую область OMS впервые. Это не влияет на отправляемые данные и не вызывает другие проблемы. Во время первого подключения эти ошибки можно игнорировать.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Данные Nagios не отображаются на портале OMS
#### <a name="probable-causes"></a>Возможные причины
* У пользователя omsagent отсутствуют разрешения на чтение данных в файле журнала Nagios.
* В файле omsagent.conf для Nagios все еще закомментированы разделы source и filter.

#### <a name="resolutions"></a>Способы устранения
* Предоставьте пользователю omsagent разрешения на чтение файла Nagios. Дополнительные сведения см. в разделе об [оповещениях Nagios](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
* В файле общей конфигурации агента OMS для Linux в разделе `/etc/opt/microsoft/omsagent/conf/omsagent.conf` удалите комментарии для **разделов** Nagios source и filter, как в примере ниже.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Данные Linux не отображаются на портале OMS
#### <a name="probable-causes"></a>Возможные причины
* Сбой при подключении к службе OMS.
* Подключение к службе OMS заблокировано.
* Создана резервная копия данных агента OMS для Linux.

#### <a name="resolutions"></a>Способы устранения
* Проверьте подключение к службе OMS (должен быть создан файл `/etc/opt/microsoft/omsagent/conf/omsadmin.conf`).
* Повторно установите подключение, используя командную строку omsadmin.sh. Дополнительные сведения см. в разделе [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Подключение с помощью командной строки).
* При использовании прокси-сервера выполните действия по устранению неполадок, описанные выше.
* В некоторых случаях сбой подключения между агентом OMS для Linux и порталом OMS связан с тем, что в буфере достигнут максимальный размер резервных копий данных агента (50 МБ). Перезапустите агент OMS для Linux, выполнив команду `service omsagent restart` или `systemctl restart omsagent`.
  >[AZURE.NOTE] Эта проблема исправлена в агенте версии 1.1.0-28 и выше.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>Конфигурация счетчика производительности системного журнала Linux не применена на портале OMS
#### <a name="probable-causes"></a>Возможные причины
* Агент конфигурации в агенте OMS для Linux не получил сведения о последней конфигурации с портала OMS.
* Измененные параметры не были применены на портале.

#### <a name="resolutions"></a>Способы устранения
Агент конфигурации `omsconfig` в агенте OMS для Linux извлекает сведения об изменениях конфигурации портала OMS каждые 5 минут. После этого эта конфигурация применяется к файлам конфигурации агента OMS для Linux, расположенным в `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

* В некоторых случаях агент OMS для Linux не может взаимодействовать со службой конфигурации портала, в результате чего последние изменения не применяются.
* Для агента `omsconfig` должны быть установлены следующие параметры:

  * `dpkg --list omsconfig` или `rpm -qi omsconfig`
  * Если они не установлены, переустановите последнюю версию агента OMS для Linux.
* Убедитесь, что агент `omsconfig` может взаимодействовать со службой OMS.

  * Выполните команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.
    * В результате выполнения этой команды возвращается конфигурация, полученная агентом с портала, в том числе параметры системного журнала, счетчики производительности Linux и пользовательские журналы.
    * Если при выполнении команды выше произойдет сбой, выполните команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Эта команда позволяет активировать принудительное взаимодействие агента omsconfig со службой OMS для получения сведений о последней конфигурации.

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>Пользовательские данные журнала Linux не отображаются на портале OMS
#### <a name="probable-causes"></a>Возможные причины
* Сбой при подключении к службе OMS.
* Не установлен флажок **Apply the following configuration to my Linux Servers** (Применить следующую конфигурацию к моим серверам Linux).
* Агент omsconfig не получил последний пользовательский журнал с портала.
* Агенту `omsagent` не удалось получить доступ к пользовательскому журналу из-за отсутствующих разрешений или отсутствия `omsagent`. В этом случае вы увидите следующие выходные данные:
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* Это известная проблема с состоянием гонки, исправленная в агенте OMS для Linux версии 1.1.0-217.

#### <a name="resolutions"></a>Способы устранения
* Проверьте подключение (должен быть создан файл `/etc/opt/microsoft/omsagent/conf/omsadmin.conf`).
  * При необходимости повторно установите подключение, используя командную строку omsadmin.sh. Дополнительные сведения см. в разделе [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Подключение с помощью командной строки).
* На портале OMS в разделе **Параметры** на вкладке **Данные** установите флажок **Apply the following configuration to my Linux Servers** (Применить следующую конфигурацию к моим серверам Linux).  
  ![Применить конфигурацию](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* Убедитесь, что агент `omsconfig` может взаимодействовать со службой OMS.

  * Выполните команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.
  * В результате выполнения этой команды возвращается конфигурация, полученная агентом с портала, в том числе параметры системного журнала, счетчики производительности Linux и пользовательские журналы.
  * Если при выполнении команды выше произойдет сбой, выполните команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Эта команда позволяет активировать принудительное взаимодействие агента omsconfig со службой OMS для получения сведений о последней конфигурации.

Вместо привилегированного пользователя `root`, агент OMS для Linux выполняется от имени пользователя `omsagent`. В большинстве случаев для чтения определенных файлов пользователю необходимо предоставить явное разрешение.

Чтобы предоставить разрешения пользователю `omsagent`, выполните следующие команды:

1. Добавьте пользователя `omsagent` в определенную группу с помощью команды `sudo usermod -a -G <GROUPNAME> <USERNAME>`.
2. Предоставьте универсальный доступ на чтение для требуемого файла с помощью команды `sudo chmod -R ugo+rw <FILE DIRECTORY>`.

Это известная проблема с состоянием гонки, исправленная в агенте OMS для Linux версии 1.1.0-217. После обновления агента до последней версии выполните следующую команду, чтобы получить последнюю версию подключаемого модуля выходных данных.

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/conf/omsagent.conf
```

## <a name="known-limitations"></a>Известные ограничения
Ознакомьтесь со следующими разделами, чтобы узнать о текущих ограничениях агента OMS для Linux.

### <a name="azure-diagnostics"></a>Диагностика Azure
Для виртуальных машин Linux в Azure может потребоваться выполнить дополнительные действия, чтобы разрешить сбор данных с помощью системы диагностики Azure и Operations Management Suite. **версии 2.2** .

Дополнительные сведения об установке и настройке расширения диагностики для Linux см. в разделе [Включение диагностического расширения Linux с помощью команды Azure CLI](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension).

**Обновление расширения диагностики с версии 2.0 до версии 2.2 ASM интерфейса командной строки Azure:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

В этих примерах команд указан файл PrivateConfig.json. Этот файл должен выглядеть следующим образом:

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>sysklog не поддерживается
Для сбора сообщений системного журнала требуется rsyslog или syslog-ng. Управляющая программа syslog по умолчанию не поддерживается для сбора событий системного журнала в Red Hat Enterprise Linux версии 5, CentOS и Oracle Linux (sysklog). Чтобы собирать данные системного журнала из дистрибутивов этих версий, требуется установить и настроить управляющую программу rsyslog, которая заменит sysklog. Дополнительные сведения о замене sysklog на rsyslog см. в разделе [Install the newly built rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM) (Установка созданного RPM rsyslog).

## <a name="next-steps"></a>Дальнейшие действия
* [добавьте решения Log Analytics из коллекции решений](log-analytics-add-solutions.md) .
* Подробные сведения, которые собирают решения, описаны в статье [про поиск по журналам](log-analytics-log-searches.md) .
* Используйте [панели мониторинга](log-analytics-dashboards.md) для сохранения и отображения настраиваемых систем поиска.

