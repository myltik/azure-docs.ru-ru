---
title: "Azure Service Fabric: настройка мониторинга с помощью агента OMS | Документация Майкрософт"
description: "Узнайте о том, как настраивать агент OMS для наблюдения за контейнерами и счетчиками производительности для кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 095db20e7d22bd517337f24fc9a81b84988d1465
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Добавление агента OMS в кластер

В этой статье рассматриваются шаги, позволяющие добавить агент OMS как расширение масштабируемого набора виртуальных машин в кластер и подключить его к существующей рабочей области OMS Log Analytics. Это активирует сбор данных диагностики о контейнерах и приложениях, а также мониторинг производительности. Если вы добавите агент в качестве расширения, Azure Resource Manager гарантирует его установку на каждом узле, даже при масштабировании кластера.

> [!NOTE]
> В этой статье предполагается наличие настроенной рабочей области OMS Log Analytics. Если ее нет, перейдите к статье [Настройка OMS Log Analytics для кластера](service-fabric-diagnostics-oms-setup.md).

## <a name="add-the-agent-extension-via-azure-cli"></a>Добавление расширения агента через Azure CLI

Добавить агент OMS в кластер лучше всего через API масштабируемого набора виртуальных машин, доступные с Azure CLI. Если Azure CLI еще не настроен, перейдите на портал Azure и откройте экземпляр [Cloud Shell](../cloud-shell/overview.md) или [установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Как только придет запрос Cloud Shell, убедитесь, что вы работаете в той же подписке, что и ресурс. Проверьте это с помощью `az account show` и убедитесь, что значение "name" соответствует подписке кластера.

2. На портале перейдите к группе ресурсов, где находится рабочая область OMS. В области навигации справа щелкните ресурс Log Analytics (тип ресурса будет Log Analytics), прокрутите вниз и выберите **Свойства**.

    ![Страница свойств OMS](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Запишите значение `workspaceId`. 

3. Вам также понадобится `workspaceKey` для развертывания агента. Чтобы получить ключ, щелкните **Advanced Settings** (Дополнительные параметры) в разделе *Параметры* в области навигации слева. Щелкните **Серверы с Windows**, если используется кластер Windows, и **Серверы с Linux**, если вы создаете кластер Linux. Вам потребуется *первичный ключ*, который отображается для развертывания агентов, в качестве `workspaceKey`.

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
 
    Добавление агента на узлы займет меньше 15 минут. Вы можете проверить, что агенты были добавлены, с помощью API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Добавление агента через шаблон Resource Manager

Примеры шаблонов Resource Manager, которые развертывают рабочую область OMS Log Analytics и добавляют агент на каждый из узлов, доступны для [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) и [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Вы можете скачать и изменить такой шаблон для развертывания кластера с учетом ваших требований.

## <a name="next-steps"></a>Дальнейшие действия

* Включите сбор соответствующих [счетчиков производительности](service-fabric-diagnostics-event-generation-perf.md). Чтобы настроить агент OMS для сбора конкретных счетчиков производительности, перейдите на портал OMS (ссылка в верхней части ресурса OMS Log Analytics). Затем щелкните **Главная > Параметры > Данные > Счетчики производительности Windows** или **Счетчики производительности Linux** и выберите счетчики, данные которых необходимо собрать.
* Настройте OMS, чтобы настроить [автоматические оповещения](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.
