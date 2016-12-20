---
title: "Журналы событий Windows в Log Analytics | Документация Майкрософт"
description: "Журналы событий Windows — один из самых распространенных источников данных, используемых службой Log Analytics.  В этой статье описано, как настроить коллекцию журналов событий Windows и сведения о записях, созданных ими в репозитории OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 055a60c174c280ba56ea40cb47779e233b54fe8e
ms.openlocfilehash: 867dc9fd84793debc8c8dcd2a27dfc0b0ccf69c4


---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Источники данных для журнала событий Windows в Log Analytics
Журналы событий Windows — это один из самых распространенных [источников данных](log-analytics-data-sources.md), используемый для сбора данных агентами Windows, так как он применяется большинством приложений для записи сведений.  События можно собирать из стандартных журналов, таких как журналы системы и приложений, а также указывать пользовательские журналы приложений, которые необходимо отслеживать.

![События Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Настройка журналов событий Windows
Журналы событий Windows настраиваются в меню ["Данные" в параметрах Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics собирает события только из журналов событий Windows, указанных в параметрах.  Чтобы добавить журнал событий, введите его имя и щелкните **+**.  Из каждого журнала собираются только события с заданной степенью серьезности.  Укажите степени серьезности событий, которые хотите получать из соответствующего журнала.  Дополнительные критерии для фильтрации событий задавать нельзя.

При вводе имени служба Log Analytics предоставляет варианты распространенных имен журналов событий. Если журнал, который нужно добавить, не отображается в списке, вы по-прежнему можете его добавить, введя его полное имя. Полное имя журнала можно просмотреть с помощью средства просмотра событий. Для этого в средстве просмотра событий откройте страницу *Свойства* журнала и скопируйте строку в поле *Полное имя*.

![Настройка событий Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Сбор данных
По мере создания событий служба Log Analytics собирает из отслеживаемого журнала событий все события, соответствующие заданной степени серьезности.  Агент фиксирует место сбора в каждом журнале событий, который используется для сбора данных.  Если агент на некоторое время переходит в автономный режим, Log Analytics собирает события, начиная с места остановки, даже если эти события были созданы, пока агент был отключен.

## <a name="windows-event-records-properties"></a>Свойства записей о событиях Windows
Записи о событиях Windows имеют тип **Событие** и свойства, описанные в приведенной ниже таблице.

| Свойство | Описание |
|:--- |:--- |
| Компьютер |Имя компьютера, с которого было получено событие. |
| EventCategory |Категория события. |
| EventData |Все данные событий в формате RAW. |
| EventID |Номер события. |
| EventLevel |Степень серьезности события в числовом формате. |
| EventLevelName |Степень серьезности события в текстовом формате. |
| EventLog |Имя журнала событий, из которого было получено событие. |
| ParameterXml |Значения параметров события в формате XML. |
| ManagementGroupName |Имя группы управления для агентов System Center Operations Manager.  Для других агентов это AOI-<workspace ID>. |
| RenderedDescription |Описание события со значениями параметров. |
| Источник |Источник события. |
| SourceSystem |Тип агента, из которого было получено событие. <br> OpsManager — агент Windows, подключенный напрямую или управляемый с помощью Operations Manager. <br> Linux — все агенты Linux  <br>  AzureStorage – диагностика Azure |
| TimeGenerated |Дата и время создания события в Windows. |
| UserName |Имя пользователя учетной записи, который зафиксировал событие. |

## <a name="log-searches-with-windows-events"></a>Поиск журналов с помощью событий Windows
Ниже приведены различные примеры запросов поиска по журналу, которые извлекают записи событий Windows.

| Запрос | Описание |
|:--- |:--- |
| Type=Event |Все события Windows. |
| Type=Event EventLevelName=error |Все события Windows с указанием серьезности ошибки. |
| Type=Event &#124; Measure count() by Source |Число событий Windows по источникам. |
| Type=Event EventLevelName=error &#124; Measure count() by Source |Число событий ошибок Windows по источникам. |

## <a name="next-steps"></a>Дальнейшие действия
* Настройте службу Log Analytics для сбора других [источников данных](log-analytics-data-sources.md) для анализа.
* Узнайте больше об [операциях поиска по журналу](log-analytics-log-searches.md) , которые можно применять для анализа данных, собираемых из источников данных и решений.  
* Используйте [настраиваемые поля](log-analytics-custom-fields.md) для анализа записей событий в отдельных полях.
* Настройте [коллекцию счетчиков производительности](log-analytics-data-sources-performance-counters.md) из агентов Windows.




<!--HONumber=Nov16_HO3-->


