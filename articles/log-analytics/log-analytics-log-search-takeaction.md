---
title: "Действие Runbook службы автоматизации Azure в Log Analytics, инициированное пользователем | Документы Майкрософт"
description: "В этой статье описывается запуск модуля runbook службы автоматизации на основе результатов поиска Log Analytics по требованию."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ff938697add98f3d21b4971175432335ee2e39ba
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Выполнение действия с Runbook службы автоматизации на основе результатов поиска Log Analytics

В результатах поиска по журналам в Azure Log Analytics теперь можно выбрать пункт **Выполнить действие** для запуска модуля runbook службы автоматизации.  Runbook может использоваться для устранения проблемы или выполнения некоторых других действий, таких как сбор диагностических сведений, отправка сообщения электронной почты или создание запроса на обслуживание. 

## <a name="components-and-features-used"></a>Используемые компоненты и функции
* [Учетная запись службы автоматизации Azure](../automation/automation-offering-get-started.md)
* [Рабочая область Log Analytics](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Запуск runbook из результатов поиска по журналам

Чтобы отреагировать на событие и запустить runbook из результатов поиска по журналам, выполните поиск по журналам и на основе результатов вызовите runbook по запросу.  Это можно сделать через функцию поиска по журналам в Azure или на [портале OMS](../log-analytics/log-analytics-log-searches.md).  В этом примере мы выполняем поиск по журналам на портале Azure, демонстрируя основные возможности этой функции.

1. На портале Azure в меню "Центр" щелкните **Другие службы** и выберите **Log Analytics**.  
2. В колонке Log Analytics выберите рабочую область Log Analytics и в колонке рабочей области выберите **Поиск по журналам**.  
3. В колонке "Поиск по журналам" выполните поиск.  
4. В результатах поиска по журналам нажмите кнопку с многоточием слева от полей и в контекстном меню выберите пункт **Выполнить действие с**.<br><br> ![Выбор "Выполнить действие" в результатах поиска](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
5. В колонке "Выполнить действие" выберите **Запуск Runbook**, а в колонке **Запуск Runbook** — модуль runbook для запуска.  Вы можете выбрать любой runbook в учетной записи службы автоматизации, связанной с рабочей областью Log Analytics.  Обратите внимание на следующее.

    * Модули runbook группируются по тегам.
    * Значения входных параметров runbook можно выбрать непосредственно из полей результатов поиска.  Появится раскрывающийся список со всеми доступными для выбора полями в результатах.  
    * Можно выбрать выполнение runbook для [гибридной рабочей роли Runbook](../automation/automation-hybrid-runbook-worker.md), установленной на компьютере, на котором возникли проблемы, если у вас есть соответствующая группа гибридных рабочих ролей Runbook, содержащая только этот компьютер.  Если имя группы гибридных рабочих ролей совпадает с именем компьютера, которое содержится в результатах поиска по журналам, группа выбирается автоматически.    

6. После нажатия кнопки **Выполнить** открывается колонка задания runbook, в которой можно просмотреть состояние задания.   

При выборе модуля runbook, который был настроен для [вызова из оповещения Log Analytics](../automation/automation-invoke-runbook-from-omsla-alert.md), он имеет входной параметр с именем **WebhookData** типа **Объект**.  Если входной параметр является обязательным, вам потребуется передать результаты поиска в runbook, чтобы преобразовать строку в формате JSON в тип объекта, что позволит вам выполнить фильтрацию по конкретным элементам, на которые вы будете ссылаться в действиях runbook.  Это можно сделать, выбрав **результат поиска (Объект)** в раскрывающемся списке.<br><br> ![Выбор объекта данных Webhook для параметра runbook](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Дальнейшие действия

* Полный список полей и аспектов для поиска, доступных в Log Analytics, см. в [справочнике по поиску в журналах Log Analytics](log-analytics-search-reference.md).
* Сведения об автоматическом вызове runbook службы автоматизации см. в статье [Вызов модуля Runbook службы автоматизации Azure из оповещения OMS Log Analytics](../automation/automation-invoke-runbook-from-omsla-alert.md).  
