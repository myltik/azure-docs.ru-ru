---
title: Создание оповещений для служб Azure с помощью кроссплатформенного интерфейса командной строки | Документация Майкрософт
description: Узнайте, как активировать сообщения электронной почты, уведомления, вызовы URL-адресов веб-сайтов (webhook) или автоматизированные операции при выполнении заданных условий.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 9ea7da35acefc139625e71904c8aa1b01b87e4df
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Создание классических оповещений метрик в Azure Monitor для служб Azure с помощью кроссплатформенного интерфейса командной строки
> [!div class="op_single_selector"]
> * [Портал](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Обзор
> [!NOTE]
> В этой статье объясняется, как создавать классические оповещения метрик прежней версии. Azure Monitor теперь поддерживает [новые улучшенные оповещения на основе метрик](monitoring-near-real-time-metric-alerts.md). Эти оповещения поддерживают мониторинг нескольких метрик и охватывают метрики размерностей. В CLI поддержка новых оповещений на основе метрик ожидается в ближайшее время.
>
>

В этой статье показано, как настроить классические оповещения метрик Azure с помощью кроссплатформенного интерфейса командной строки.

> [!NOTE]
> Azure Monitor — это новое название для Azure Insights, актуальное с 25 сентября 2016 г. При этом пространства имен и соответствующие команды будут по-прежнему содержать фрагмент старого названия ("insights").
>
>

Вы можете получать оповещения на основе отслеживания метрик или событий в службах Azure.

* **Значения метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.    
* **События журнала действий**. Оповещение может активироваться при *каждом* событии или только тогда, когда выполняется определенное событие. Чтобы узнать больше об оповещениях журнала действий, [щелкните здесь](monitoring-activity-log-alerts.md).

Для классического оповещения метрики можно настроить действие, выполняемое при его активации:

* отправка уведомлений по электронной почте администратору службы и соадминистраторам;
* отправка уведомления на указанные дополнительные электронные адреса;
* вызов webhook;
* запуск выполнения Runbook Azure (в настоящее время только на портале Azure).

Для настройки правил генерации классических оповещений метрик и получения сведений о них можно использовать:

* [портал Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [интерфейс командной строки (CLI)](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Справку по командам всегда можно получить, введя команду с параметром -help. Например: 

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Создание правил генерации оповещений с помощью интерфейса командной строки
1. Выполнение необходимые условия и войдите в Azure. См. [примеры CLI для Azure Monitor](insights-cli-samples.md). Если коротко, установите интерфейс командной строки и выполните следующие команды. Вы войдете в систему, увидите, какая подписка используется, и подготовитесь к выполнению команд Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Чтобы получить список существующих правил для группы ресурсов, используйте следующую команду: **azure insights alerts rule list** *[параметры] &lt;группа_ресурсов&gt;*.

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Чтобы создать правило, необходимо сначала получить определенные важные сведения.
  * **Идентификатор ресурса** , для которого необходимо задать оповещение.
  * Доступные **определения метрик** для этого ресурса.

     Получить идентификатор ресурса можно на портале Azure. Если ресурс уже создан, выберите его на портале. В следующей колонке в разделе *Параметры* выберите *Свойства*. В следующей колонке отображается поле *Идентификатор ресурса* . Кроме того, для получения идентификатора ресурса можно использовать [Azure Resource Explorer](https://resources.azure.com/).

     Ниже приведен пример идентификатора ресурса для веб-приложения.

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Чтобы получить список доступных метрик и их единиц измерения для предыдущего примера ресурсов, введите следующую команду в интерфейсе командной строки.  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* представляет собой детализацию доступного измерения (интервалы в 1 минуту). Выбор различных уровней детализации позволяет использовать разные параметры метрик.
4. Чтобы создать правило генерации оповещений на основе метрики, используйте команду следующего вида.

    **azure insights alerts rule metric set** *[параметры] &lt;имя_правила&gt; &lt;расположение&gt; &lt;группа_ресурсов&gt; &lt;размер_окна&gt; &lt;оператор&gt; &lt;пороговое_значение&gt; &lt;ИД_целевого_ресурса&gt; &lt;имя_метрики&gt; &lt;оператор_агрегата_времени&gt;*

    Следующий пример настраивает оповещение о ресурсе веб-сайта. Оповещение активируется, когда любой трафик непрерывно поступает в течении 5 минут, и затем снова активируется, если в течение 5 минут трафик не поступает.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Чтобы при срабатывании классического оповещения метрики создавался веб-перехватчик или отправлялось письмо, необходимо сначала создать это письмо и (или) веб-перехватчики. Сразу после этого следует создать правило. С помощью интерфейса командной строки невозможно связать webhook или электронные адреса с уже созданными правилами.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Можно проверить, правильно ли созданы оповещения, просмотрев отдельное правило.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Для удаления правил используйте следующую команду.

    **insights alerts rule delete** [параметры] &lt;группа_ресурсов&gt; &lt;имя_правила&gt;

    Приведенные ниже команды удаляют правила, созданные ранее в этой статье.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Дополнительная информация
* [Ознакомьтесь с общими сведениями о мониторинге Azure](monitoring-overview.md) , включая типы информации, которую можно собирать и отслеживать.
* Узнайте больше о [настройке веб-перехватчиков webhook в оповещениях](insights-webhooks-alerts.md).
* Узнайте больше о [настройке оповещений о событиях журнала действий](monitoring-activity-log-alerts.md).
* Узнайте больше о [модулях Runbook службы автоматизации Azure](../automation/automation-starting-a-runbook.md).
* Ознакомьтесь с [обзором сбора журналов диагностики](monitoring-overview-of-diagnostic-logs.md) , чтобы собирать подробные метрики о службе с высокой частотой.
* Прочитайте [обзор сбора метрики](insights-how-to-customize-monitoring.md) и узнайте, как можно обеспечить, чтобы служба была доступна и отвечала на запросы.
