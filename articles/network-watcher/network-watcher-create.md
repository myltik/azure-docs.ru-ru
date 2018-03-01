---
title: "Создание экземпляра Наблюдателя за сетями Azure | Документация Майкрософт"
description: "Эта страница содержит инструкции по созданию экземпляра Наблюдателя за сетями с помощью портала и Azure REST API."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fb1e3f9a93d18e949cb42ac0a4e09129cfe414f6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-azure-network-watcher-instance"></a>Создание экземпляра Наблюдателя за сетями Azure

Наблюдатель за сетями — это региональная служба, обеспечивающая мониторинг и диагностику условий на уровне сетевого сценария на платформе Azure. Мониторинг на уровне сценария позволяет диагностировать проблемы в сети с помощью комплексного представления сетевого уровня. Инструменты диагностики сети и визуализации, доступные в Наблюдателе за сетями, помогают понять, как работает сеть в Azure, диагностировать ее и получить ценную информацию.

> [!NOTE]
> Наблюдатель за сетями в настоящее время поддерживает только интерфейс командной строки 1.0, поэтому приведены инструкции по созданию нового экземпляра Наблюдателя за сетями именно для этой версии.

## <a name="create-a-network-watcher-in-the-portal"></a>Создание Наблюдателя за сетями на портале

Последовательно выберите **Все службы** > **Сети** > **Наблюдатель за сетями**. Можно выбрать все подписки, для которых необходимо включить Наблюдатель за сетями. Это действие создаст экземпляр Наблюдателя за сетями в каждом регионе, который доступен.

![Создание Наблюдателя за сетями][1]

При включении наблюдателя за сетями с помощью портала экземпляр наблюдателя автоматически получит имя NetworkWatcher_имя_региона, где имя_региона соответствует региону Azure, в котором был включен этот экземпляр наблюдателя.  Например, наблюдатель за сетями, включенный в западной части центрального региона США, получит имя NetworkWatcher_westcentralus

Кроме того, экземпляр наблюдателя за сетями будет автоматически добавлен в группу ресурсов под названием NetworkWatcherRG.  Если эта группа ресурсов не существует, она будет создана.

Если вы хотите настроить имя экземпляра наблюдателя за сетями и имя группы ресурсов, в которую добавляется этот экземпляр наблюдателя, используйте Powershell, API-интерфейс REST или методы ARMClient, описанные ниже.  В любом случае группа ресурсов должна существовать, чтобы наблюдатель за сетями можно было добавить в нее.  

## <a name="create-a-network-watcher-with-powershell"></a>Создание Наблюдателя за сетями с помощью PowerShell

Чтобы создать экземпляр Наблюдателя за сетями, выполните приведенный ниже пример.

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Создание Наблюдателя за сетями с помощью REST API

Чтобы вызвать REST API при помощи командлетов PowerShell, вам потребуется ARMClient. Пакет ARMClient можно скачать на сайте [Chocolatey](https://chocolatey.org/packages/ARMClient).

### <a name="log-in-with-armclient"></a>выполните вход с помощью ARMClient;

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Создание Наблюдателя за сетями

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, имея экземпляр Наблюдателя за сетями, узнайте о его возможностях:

* [Топология](network-watcher-topology-overview.md)
* [Запись пакетов](network-watcher-packet-capture-overview.md)
* [Проверка IP-потока](network-watcher-ip-flow-verify-overview.md)
* [Определение следующего прыжка](network-watcher-next-hop-overview.md)
* [Представление групп безопасности](network-watcher-security-group-view-overview.md)
* [Ведение журнала потоков NSG](network-watcher-nsg-flow-logging-overview.md)
* [Устранение неполадок шлюза виртуальной сети](network-watcher-troubleshoot-overview.md)

После создания экземпляра Наблюдателя за сетями вы можете настроить запись пакетов, следуя указаниям в статье [Использование записи пакетов для упреждающего мониторинга сети с помощью Функций Azure](network-watcher-alert-triggered-packet-capture.md).

[1]: ./media/network-watcher-create/figure1.png











