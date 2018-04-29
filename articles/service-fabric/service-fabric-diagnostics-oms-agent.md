---
title: Мониторинг производительности с помощью Log Analytics в Azure Service Fabric | Документация Майкрософт
description: Узнайте о том, как настраивать агент OMS для наблюдения за контейнерами и счетчиками производительности для кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 6e1c870458f43bcc5d6d40f0e40e2b2a95bee2af
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>Мониторинг производительности с помощью Log Analytics

В этой статье рассматриваются шаги, позволяющие добавить Log Analytics, также известного как агент OMS, в качестве расширения масштабируемого набора виртуальных машин в кластер и подключить его к существующей рабочей области Azure Log Analytics. Это активирует сбор данных диагностики о контейнерах и приложениях, а также мониторинг производительности. Если вы добавите агент в качестве расширения ресурса масштабируемого набора виртуальных машин, Azure Resource Manager гарантирует его установку на каждом узле, даже при масштабировании кластера.

> [!NOTE]
> В этой статье предполагается наличие настроенной рабочей области Azure Log Analytics. Если ее нет, перейдите к статье о [настройке Azure Log Analytics](service-fabric-diagnostics-oms-setup.md).

## <a name="add-the-agent-extension-via-azure-cli"></a>Добавление расширения агента через Azure CLI

Добавить агент OMS в кластер лучше всего через API масштабируемого набора виртуальных машин, доступные с Azure CLI. Если Azure CLI еще не настроен, перейдите на портал Azure и откройте экземпляр [Cloud Shell](../cloud-shell/overview.md) или [установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Как только придет запрос Cloud Shell, убедитесь, что вы работаете в той же подписке, что и ресурс. Проверьте это с помощью `az account show` и убедитесь, что значение "name" соответствует подписке кластера.

2. На портале перейдите к группе ресурсов, где находится рабочая область Log Analytics. Щелкните ресурс Log Analytics (тип ресурса будет Log Analytics). Перейдя на страницу обзора ресурсов, щелкните **Дополнительные настройки** в разделе "Параметры" в меню слева.

    ![Страница свойств Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Щелкните **Серверы с Windows**, если используется кластер Windows, и **Серверы с Linux**, если вы создаете кластер Linux. На этой странице отображаются ваши `workspace ID` и `workspace key` (отображается на портале как первичный ключ). Они необходимы для выполнения следующего шага.

4. Выполните команду, чтобы установить агент OMS на кластере с помощью API `vmss extension set` в Cloud Shell:

    Для кластера Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Для кластера Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Ниже приведен пример агента OMS, добавляемого в кластер Windows.

    ![Команда cli агента OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Добавление агента на узлы займет меньше 15 минут. Вы можете проверить, что агенты были добавлены, с помощью API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Добавление агента через шаблон Resource Manager

Примеры шаблонов Resource Manager, при помощи которых развертывается рабочую область Azure Log Analytics и добавляется агент на каждый из узлов, доступны для [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) и [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Вы можете скачать и изменить такой шаблон для развертывания кластера с учетом ваших требований.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Просмотр счетчиков производительности на портале Log Analytics

После добавления агента OMS перейдите на портал Log Analytics, чтобы выбрать счетчики производительности, данные которых необходимо собирать. 

1. На портале Azure перейдите в группу ресурсов, в которой вы создали решение "Аналитика Service Fabric". Выберите **ServiceFabric\<имя_рабочей_области_OMS\>** и перейдите на страницу обзора. Чтобы перейти к порталу OMS, щелкните ссылку в верхней части страницы.

2. После входа на портал вы увидите плитки в форме графика для каждого включенного решения, включая одну для Service Fabric. Щелкните плитку Service Fabric, чтобы перейти к решению "Аналитика Service Fabric". 

3. Теперь вы увидите несколько плиток с графиками по событиям в операционном канале и в Reliable Services. Чтобы перейти на страницу "Параметры", щелкните правой кнопкой мыши значок шестеренки.

    ![Параметры OMS](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. На странице "Параметры" щелкните "Данные" и выберите счетчики производительности Windows или Linux. Существует список значений по умолчанию, которые можно выбрать. Вы также можете задать интервал коллекции. Можно также выбрать [дополнительные счетчики производительности](service-fabric-diagnostics-event-generation-perf.md), чтобы они выполняли сбор данных. Правильный формат см. в [этой статье](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

После настройки счетчиков вернитесь на страницу решений, где вы вскоре увидите поток данных и их отображение на графиках в разделе **Метрики узла**. Вы можете запрашивать данные счетчиков производительности аналогично событиям кластера и задавать фильтры по узлам, именам счетчиков производительности и значениям с помощью языка запросов Kusto. 

![Запрос счетчика производительности OMS](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>Дополнительная информация

* Включите сбор соответствующих [счетчиков производительности](service-fabric-diagnostics-event-generation-perf.md). Чтобы настроить агент OMS для сбора данных определенных счетчиков производительности, ознакомьтесь с разделом [Настройка источников данных](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Настройте в Log Analytics [автоматические оповещения](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.
* В качестве альтернативы можно собирать счетчики производительности с помощью [расширения Azure Diagnostics и отправлять их в Application Insight](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).