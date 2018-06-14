---
title: Сбор и анализ сообщений Syslog в OMS Log Analytics | Документация Майкрософт
description: Системный журнал (Syslog) — это протокол ведения журнала событий, который обычно используется в Linux. В этой статье описано, как настроить сбор сообщений системного журнала в службе Log Analytics, а также сведения о записях, создаваемых ими в репозитории OMS.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 011eaf1a4705f9078225b9b871f81b4333b05ee8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011100"
---
# <a name="syslog-data-sources-in-log-analytics"></a>Источники данных системного журнала в Log Analytics
Системный журнал (Syslog) — это протокол ведения журнала событий, который обычно используется в Linux.  Приложения отправляют сообщения, которые могут храниться на локальном компьютере или передаваться в сборщик системного журнала.  При установке агента OMS для Linux он настраивает локальную управляющую программу системного журнала для пересылки сообщений в агент.  Затем агент отправляет сообщение в службу Log Analytics, где в репозитории OMS создается соответствующая запись.  

> [!NOTE]
> Log Analytics поддерживает сбор сообщений, отправленных rsyslog или syslog-ng, где rsyslog является управляющей программой по умолчанию. Управляющая программа syslog по умолчанию не поддерживается для сбора событий системного журнала в Red Hat Enterprise Linux версии 5, CentOS и Oracle Linux (sysklog). Чтобы собирать данные системного журнала из дистрибутивов этих версий, требуется установить и настроить [управляющую программу rsyslog](http://rsyslog.com) , которая заменит sysklog.
>
>

![Сбор сообщений системного журнала](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Настройка системного журнала
Агент OMS для Linux будет собирать события только с тех устройств и только с тем уровнем серьезности, которые заданы в его конфигурации.  Системный журнал можно настроить на портале Azure или с помощью управления файлами конфигурации на агентах Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Настройка системного журнала на портале Azure
Настройте системный журнал в [меню "Данные" раздела "Дополнительные параметры" Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Эта конфигурация передается в файл конфигурации на каждом агенте Linux.

Можно добавить новое устройство, введя его имя и нажав кнопку **+**.  Для каждого устройства будут собираться сообщения только с выбранным уровнем серьезности.  Укажите уровни серьезности сообщений, которые хотите получать от соответствующего устройства.  Дополнительные критерии для фильтрации сообщений задавать нельзя.

![Настройка системного журнала](media/log-analytics-data-sources-syslog/configure.png)

По умолчанию все изменения конфигурации автоматически отправляются во все агенты.  Если вы хотите настроить системный журнал вручную на каждом агенте Linux, снимите флажок *Apply below configuration to my Linux machines*(Применить конфигурацию ниже к моим компьютерам Linux).

### <a name="configure-syslog-on-linux-agent"></a>Настройка системного журнала на агенте Linux
При [установке агента OMS на клиент Linux](log-analytics-linux-agents.md)он устанавливает файл конфигурации системного журнала по умолчанию, который определяет устройства и уровень серьезности для собираемых сообщений.  Можно изменить этот файл, чтобы внести изменения в конфигурацию.  Файл конфигурации отличается в зависимости от того, какую управляющая программу системного журнала установил клиент.

> [!NOTE]
> При изменении конфигурации системного журнала требуется перезапустить управляющую программу syslog, чтобы изменения вступили в силу.
>
>

#### <a name="rsyslog"></a>rsyslog
Файл конфигурации для rsyslog находится в расположении **/etc/rsyslog.d/95-omsagent.conf**.  Его содержимое по умолчанию приведено ниже.  В данном случае собираются сообщения системного журнала, отправленные из локального агента для всех устройств и имеющие уровень серьезности "предупреждение" или выше.

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

Устройство можно удалить, выполнив удаление соответствующего раздела в файле конфигурации.  Можно ограничить уровни серьезности для сообщений, собираемых с конкретного устройства, изменив соответствующую запись устройства.  Например, чтобы ограничить сообщения с пользовательского устройства уровнем серьезности "ошибка" или выше, необходимо изменить соответствующую строку в файле конфигурации таким образом:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Файл конфигурации для syslog-ng находится в расположении **/etc/syslog-ng/syslog-ng.conf**.  Его содержимое по умолчанию приведено ниже.  В данном случае собираются сообщения системного журнала, отправленные из локального агента для всех устройств и всех уровней серьезности.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Устройство можно удалить, выполнив удаление соответствующего раздела в файле конфигурации.  Можно ограничить уровни серьезности для сообщений, собираемых с конкретного устройства, удалив их из его списка.  Например, чтобы ограничить сообщения с пользовательского устройства только уровнями серьезности "оповещение" и "критический", необходимо изменить соответствующий раздел в файле конфигурации таким образом:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Сбор данных из дополнительных портов системного журнала
Агент OMS прослушивает сообщения системного журнала на локальном клиенте через порт 25224.  При установке агента применяется конфигурация системного журнала по умолчанию. Файл конфигурации расположен в следующем расположении:

* rsyslog: `/etc/rsyslog.d/95-omsagent.conf`;
* syslog-ng: `/etc/syslog-ng/syslog-ng.conf`.

Номер порта можно изменить, создав два файла конфигурации: FluentD и rsyslog-or-syslog-ng (в зависимости от установленной управляющей программы системного журнала).  

* Файл конфигурации FluentD должен быть новым и располагаться в папке `/etc/opt/microsoft/omsagent/conf/omsagent.d`. Замените значение записи **port** собственным номером порта.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Для rsyslog в папке `/etc/rsyslog.d/` необходимо создать файл конфигурации. Замените значение %SYSLOG_PORT% собственным номером порта.  

    > [!NOTE]
    > Если изменить это значение в файле конфигурации `95-omsagent.conf`, он перезаписывается, когда агент применяет конфигурацию по умолчанию.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Конфигурацию syslog-ng следует изменить. Для этого скопируйте пример конфигурации ниже и добавьте измененные параметры в конец файла конфигурации в папке `/etc/syslog-ng/`.  **Не** используйте метку **%WORKSPACE_ID%_oms** или **%WORKSPACE_ID_OMS** по умолчанию. Чтобы различить изменения, определите собственную метку.  

    > [!NOTE]
    > Если изменить эти значения по умолчанию в файле конфигурации, он перезаписывается, когда агент применяет конфигурацию по умолчанию.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

После внесения изменений системный журнал и службу агента OMS следует перезапустить, чтобы изменения вступили в силу.   

## <a name="syslog-record-properties"></a>Свойства записей системного журнала
Записи системного журнала имеют тип **Syslog** и свойства, описанные в приведенной ниже таблице.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| Компьютер |Компьютер, с которого было получено событие. |
| Facility |Определяет часть системы, которая создала сообщение. |
| HostIP |IP-адрес системы, отправившей сообщение. |
| HostName |Имя системы, отправившей сообщение. |
| SeverityLevel |Уровень серьезности события. |
| SyslogMessage |Текст сообщения. |
| ProcessID |Идентификатор процесса, создавшего сообщение. |
| EventTime |Дата и время создания события. |

## <a name="log-queries-with-syslog-records"></a>Запросы к журналу для получения записей системного журнала
В следующей таблице представлены различные примеры запросов к журналу, извлекающих записи из системного журнала.

| Запрос | ОПИСАНИЕ |
|:--- |:--- |
| syslog |Все записи системного журнала. |
| Syslog &#124; where SeverityLevel == "error" |Все записи системного журнала с уровнем серьезности "ошибка". |
| Syslog &#124; summarize AggregatedValue = count() by Computer |Число записей системного журнала по компьютеру. |
| Syslog &#124; summarize AggregatedValue = count() by Facility |Число записей системного журнала по устройству. |

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше об [операциях поиска по журналу](log-analytics-log-searches.md) , которые можно применять для анализа данных, собираемых из источников данных и решений.
* Используйте [настраиваемые поля](log-analytics-custom-fields.md) для анализа данных из записей системного журнала в отдельных полях.
* [Настройте агенты Linux](log-analytics-linux-agents.md) для сбора других типов данных.
