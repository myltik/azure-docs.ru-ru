---
title: "Сбор оповещений Nagios и Zabbix в OMS Log Analytics | Документы Майкрософт"
description: "Nagios и Zabbix — средства мониторинга с открытым исходным кодом. Оповещения от этих средств мониторинга можно собирать в Log Analytics для анализа вместе с оповещениями из других источников.  В этой статье описано, как настроить сбор оповещений из этих средств в агенте OMS для Linux."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 0b64c32e1031e704d50aab0b38eaea41e27d134b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Сбор оповещений Nagios и Zabbix из агента OMS для Linux в Log Analytics 
[Nagios](https://www.nagios.org/) и [Zabbix](http://www.zabbix.com/) — средства мониторинга с открытым исходным кодом.  Оповещения от этих средств мониторинга можно собирать в Log Analytics для анализа вместе с [оповещениями из других источников](log-analytics-alerts.md).  В этой статье описано, как настроить сбор оповещений из этих средств в агенте OMS для Linux.
 
## <a name="configure-alert-collection"></a>Настройка сбора оповещений

### <a name="configuring-nagios-alert-collection"></a>Настройка сбора оповещений Nagios
Для настройки сбора оповещений на сервере Nagios выполните следующие действия.

1. Предоставьте пользователю **omsagent** разрешение на чтение для файла журнала Nagios (`/var/log/nagios/nagios.log`). Если файл nagios.log принадлежит группе `nagios`, можно добавить пользователя **omsagent** в группу **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Измените файл конфигурации (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Для этого введите следующие записи в незакомментированном виде:  

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

3. Перезапустите управляющую программу omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Настройка сбора оповещений Zabbix
Для сбора оповещений сервера Zabbix необходимо указать имя пользователя и пароль в формате *открытого текста*. Это не идеальное решение, но мы рекомендуем создать отдельного пользователя и предоставить ему разрешения только для мониторинга.

Для настройки сбора оповещений на сервере Nagios выполните следующие действия.

1. Измените файл конфигурации (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Убедитесь, что в файле есть следующие параметры и что они не закомментированы.  Измените имя пользователя и пароль на соответствующие значения для вашей среды Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Перезапустите управляющую программу omsagent

    sudo sh /opt/microsoft/omsagent/bin/service_control restart


## <a name="alert-records"></a>Записи оповещений
Записи оповещений из Nagios и Zabbix можно получить с помощью [поиска по журналам](log-analytics-log-searches.md) в Log Analytics.

### <a name="nagios-alert-records"></a>Записи оповещений Nagios

Для записей оповещений Nagios параметр **Type** (тип) имеет значение **Alert** (оповещение), а параметр **SourceSystem** (источник) — значение **Nagios**.  У них есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| Тип |*Предупреждение* |
| SourceSystem |*Nagios* |
| AlertName |Имя оповещения. |
| AlertDescription | Описание оповещения. |
| AlertState | Состояние службы или узла.<br><br>ОК<br>ПРЕДУПРЕЖДЕНИЕ<br>РАБОТАЕТ<br>СБОЙ |
| HostName | Имя узла, который создал оповещение. |
| PriorityNumber | Приоритет оповещения. |
| StateType | Тип состояния оповещения.<br><br>SOFT — проблема, которая не проверялась повторно.<br>HARD — проблема, которая проверялась повторно заданное число раз.  |
| TimeGenerated |Дата и время создания оповещения. |


### <a name="zabbix-alert-records"></a>Записи оповещений Zabbix
Для записей оповещений Nagios параметр **Type** (тип) имеет значение **Alert** (оповещение), а параметр **SourceSystem** (источник) — значение **Zabbix**.  У них есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| Тип |*Предупреждение* |
| SourceSystem |*Zabbix* |
| AlertName | Имя оповещения. |
| AlertPriority | Серьезность оповещения.<br><br>не определен<br>информационный.<br>Предупреждение<br>average<br>высокий<br>очень высокий  |
| AlertState | Состояние оповещения.<br><br>0 — актуальное состояние.<br>1 — состояние неизвестно.  |
| AlertTypeNumber | Указывает, может ли оповещение создавать несколько событий, связанных с проблемой.<br><br>0 — актуальное состояние.<br>1 — состояние неизвестно.    |
| Комментарии | Дополнительные комментарии для оповещения. |
| HostName | Имя узла, который создал оповещение. |
| PriorityNumber | Значение, указывающее уровень серьезности оповещения.<br><br>0 — не определен<br>1 — информация<br>2 — предупреждение<br>3 — средний<br>4 — высокий<br>5 — очень высокий |
| TimeGenerated |Дата и время создания оповещения. |
| TimeLastModified |Дата и время последнего изменения состояния оповещения. |


## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с дополнительными сведениями об [оповещениях](log-analytics-alerts.md) в Log Analytics.
* Узнайте больше об [операциях поиска по журналу](log-analytics-log-searches.md) , которые можно применять для анализа данных, собираемых из источников данных и решений. 
