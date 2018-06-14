---
title: Оценка приложений Service Fabric в Log Analytics на портале Azure | Документация Майкрософт
description: Решение Service Fabric в Log Analytics можно использовать для оценки риска и работоспособности приложений, микрослужб, узлов и кластеров Service Fabric на портале Azure.
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nini
ms.openlocfilehash: 10c5394935c59b21139c0e141c348ce20a7c0800
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28932687"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Оценка приложений и микрослужб Azure Service Fabric на портале Azure

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Символ Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

В этой статье описывается, как с помощью решений Service Fabric в Log Analytics определять и устранять неполадки в кластере Service Fabric.

Решение Service Fabric использует данные системы диагностики Azure, полученные от виртуальных машин Service Fabric, собирая эти данные из таблиц Azure WAD. Log Analytics затем считывает события платформы Service Fabric, включая **события надежной службы**, **события субъектов**, **операционные события** и **пользовательские события трассировки событий Windows**. С помощью панели мониторинга решения вы можете просматривать важные проблемы и соответствующие события в среде Service Fabric.

Чтобы начать работу с решением, необходимо подключить кластер Service Fabric к рабочей области Log Analytics. Рассмотрим три важных сценария.

1. Если кластер Service Fabric не развернут, выполните действия, описанные в разделе ***Развертывание кластера Service Fabric, подключенного к рабочей области Log Analytics***. Так вы сможете развернуть новый кластер и настроить в нем функцию отчетности для Log Analytics.
2. Если необходимо собрать счетчики производительности с узлов для использования других решений по управлению (включая решение по обеспечению безопасности в кластере Service Fabric), выполните действия, описанные в разделе, в котором объясняется, как ***развернуть кластер Service Fabric, подключенный к рабочей области Log Analytics с установленным расширением виртуальной машины***.
3. Если кластер Service Fabric уже развернут и его нужно подключить к Log Analytics, выполните действия, описанные в разделе ***Добавление существующей учетной записи хранения в Log Analytics***.

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Развертывание кластера Service Fabric, подключенного к рабочей области Log Analytics
Этот шаблон делает следующее:

1. Развертывает кластер Azure Service Fabric, уже подключенный к рабочей области Log Analytics. При развертывании шаблона можно создать новую рабочую область Log Analytics или указать имя существующей.
2. Добавляет учетную запись хранилища диагностики в рабочую область Log Analytics.
3. Включает решение Service Fabric в рабочую область Log Analytics.

[![Развертывание в Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

После нажатия кнопки развертывания выше откроется портал Azure с параметрами, которые можно изменить. Не забудьте создать группу ресурсов, если вы указываете имя новой рабочей области Log Analytics:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Примите условия и нажмите кнопку **Создать**, чтобы начать развертывание. После завершения развертывания вы увидите новую рабочую область и созданный кластер с добавленными таблицами WADServiceFabric*Event, WADWindowsEventLogs и WADETWEvent:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Развертывание кластера Service Fabric, подключенного к рабочей области Log Analytics с установленным расширением виртуальной машины

Этот шаблон делает следующее:

1. Развертывает кластер Azure Service Fabric, уже подключенный к рабочей области Log Analytics. Вы можете создать новую рабочую область или использовать существующую.
2. Добавляет учетные записи хранилища диагностики в рабочую область Log Analytics.
3. Включает решение Service Fabric в рабочей области Log Analytics.
4. Устанавливает расширение агента MMA в каждом масштабируемом наборе виртуальных машин в кластере Service Fabric. После установки агента MMA вы сможете просматривать метрики производительности своих узлов.

[![Развертывание в Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Выполнив описанные выше действия, укажите необходимые параметры и запустите развертывание. Вы увидите новую рабочую область и созданный кластер с таблицами WAD:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Просмотр данных о производительности

Чтобы просмотреть данные о производительности из узлов, сделайте следующее:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Запустите рабочую область Log Analytics на портале Azure.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Перейдите к параметрам на левой панели и последовательно выберите "Данные" >> "Счетчики производительности Windows" >> "Добавить выбранные счетчики производительности": ![Service Fabric](./media/log-analytics-service-fabric/7.png).
- В функции поиска по журналам используйте следующие запросы, чтобы получить основные метрики производительности узлов:

    a. Сравните среднее использование ЦП на всех узлах за последний час, чтобы определить проблемные узлы и промежуток времени, в течение которого для узла зарегистрирован пик:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    Б. Просмотрите аналогичные графики для доступной памяти на каждом узле, используя этот запрос:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Чтобы просмотреть список всех узлов с отображением точного среднего значения доступной памяти в МБ для каждого узла, используйте этот запрос:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Чтобы просмотреть подробные сведения об определенном узле, включая показатели среднего почасового, минимального и максимального использования ЦП, а также использования по 75-му процентилю, можно выполнить следующий запрос (замените поле Computer своим значением):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Дополнительные сведения о метриках производительности в Log Analytics см. в [блоге по Operations Management Suite](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Добавление существующей учетной записи хранения в Log Analytics

Этот шаблон просто добавляет существующие учетные записи хранения в новую или существующую рабочую область Log Analytics.

[![Развертывание в Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Если вы выбираете группу ресурсов, используя существующую рабочую область Log Analytics, выберите значение "Использовать существующую" и найдите группу ресурсов, содержащую рабочую область Log Analytics. В противном случае создайте новую.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

После развертывания этого шаблона вы увидите учетную запись хранения, подключенную к рабочей области Log Analytics. В нашем случае в рабочую область Exchange, созданную выше, добавлена еще одна учетная запись хранения.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Просмотр событий Service Fabric

Когда развертывание будет завершено и решение Service Fabric будет включено в рабочей области, щелкните плитку **Service Fabric** на портале Log Analytics, чтобы запустить панель мониторинга Service Fabric. Панель мониторинга содержит столбцы, перечисленные в приведенной ниже таблице. В каждом столбце отображены 10 наиболее распространенных событий, соответствующих указанным для столбца критериям, на выбранном диапазоне времени. Можно выполнить поиск по журналам, выводящий весь список, щелкнув элемент **Показать все** в правой нижней части каждого столбца или заголовок этого столбца.

| **Событие Service Fabric** | **description** |
| --- | --- |
| Важные проблемы |Отображение таких проблем, как сбои и отмены RunAsync, а также отключение узлов. |
| Операционные события |Важные операционные события, такие как обновление и развертывание приложения. |
| События надежных служб |Важные события надежных служб, например вызовы RunAsync. |
| События субъектов |Важные события субъектов, создаваемые микрослужбами, например исключения, вызываемые методом субъекта, включения и отключения субъекта и т. д. |
| События приложений |Все пользовательские события ETW, создаваемые приложениями. |

![Панель мониторинга Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Панель мониторинга Service Fabric](./media/log-analytics-service-fabric/sf4.png)

В следующей таблице описаны методы сбора данных, а также приведены сведения о сборе данных для Service Fabric.

| платформа | Direct Agent | Агент Operations Manager | Хранилище Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 минут |

> [!NOTE]
> Область этих событий можно изменить в решении Service Fabric, щелкнув в верхней части панели мониторинга элемент **Data based on last 7 days** (Данные за последние 7 дней). Кроме того, можно отобразить события, созданные за последние семь дней, один день или шесть часов. Можно также выбрать вариант **Custom** (Другое) и указать диапазон дат.
>
>

## <a name="next-steps"></a>Дополнительная информация

* Подробные сведения о данных событий Service Fabric см. в статье [Поиск по журналам в Log Analytics](log-analytics-log-searches.md).
