---
title: Краткий справочник по языку запросов Azure Log Analytics | Документация Майкрософт
description: Эта статья содержит информацию, которая поможет перейти на новый язык запросов Log Analytics тем, кто уже знаком с языком прежних версий.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Переход на новый язык запросов Azure Log Analytics
В Log Analytics недавно реализован новый язык запросов.  Эта статья содержит информацию, которая поможет перейти на новый язык запросов Log Analytics тем, кто уже знаком с языком прежних версий.

## <a name="resources"></a>Ресурсы


## <a name="language-converter"></a>Преобразователь языка

Если вы знакомы с прежней версией языка запросов Log Analytics, для создания уже привычных запросов на новом языке вам проще всего будет воспользоваться преобразователем языка, который устанавливается на портале поиска по журналам при преобразовании рабочей области.  Использовать преобразователь очень легко: просто введите запрос на старом языке в текстовое поле вверху и нажмите **Преобразовать**.  После этого вы можете щелкнуть кнопку поиска, чтобы сразу выполнить запрос, или скопировать его в буфер обмена, чтобы вставить в другом месте, где вы будете его использовать.

![Преобразователь языка](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Ресурсы
На [сайте документации по языку запросов Log Analytics](https://docs.loganalytics.io) есть все ресурсы, необходимые для ускорения работы на новом языке:  учебники, примеры и полный справочник по языку.


## <a name="cheat-sheet"></a>Краткий справочник

В следующей таблице на примере нескольких стандартных запросов приводится сравнение эквивалентных команд старого и нового языков запросов Azure Log Analytics.

| ОПИСАНИЕ | Прежняя версия | new |
|:--|:--|:--|
| Поиск по всем таблицам      | error | Поиск по слову "ошибка" (без учета регистра) |
| Выбор данных из таблицы | Type=Event |  Событие |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| Сравнение строк      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (без учета регистра)<br>Event &#124; where Computer contains "Contoso" (с учетом регистра) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| Сравнение данных        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &amp;#124; where TimeGenerated between (datetime(2017-05-01) . datetime(2017-05-31)) |
| Сравнение логических значений     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat \| where IsGatewayInstalled == false |
| Сортировать                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Уникальные значения               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| Расширение столбцов         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| Агрегирование            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| Группирование с ограничением | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| Объединение множеств                  | Type=Event or Type=Syslog | union Event, Syslog |
| Объединение                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Дополнительная информация
- Изучите [руководство по написанию запросов](https://go.microsoft.com/fwlink/?linkid=856078) на новом языке.
- Обратитесь к [справочнику по языку запросов](https://go.microsoft.com/fwlink/?linkid=856079), чтобы подробно изучить все команды, операторы и функции нового языка запросов.  
