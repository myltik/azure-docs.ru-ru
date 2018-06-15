---
title: Создание экземпляра Наблюдателя за сетями Azure | Документация Майкрософт
description: Узнайте, как включить Наблюдатель за сетями в регионе Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9d3e579cd58bc6c7d67b29998ea5a48a65548b0a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904006"
---
# <a name="create-an-azure-network-watcher-instance"></a>Создание экземпляра Наблюдателя за сетями Azure

Наблюдатель за сетями — это региональная служба, обеспечивающая мониторинг и диагностику условий на уровне сетевого сценария на платформе Azure. Мониторинг на уровне сценария позволяет диагностировать проблемы в сети с помощью комплексного представления сетевого уровня. Инструменты диагностики сети и визуализации, доступные в Наблюдателе за сетями, помогают понять, как работает сеть в Azure, диагностировать ее и получить ценную информацию.

## <a name="create-a-network-watcher-in-the-portal"></a>Создание Наблюдателя за сетями на портале

Последовательно выберите **Все службы** > **Сети** > **Наблюдатель за сетями**. Можно выбрать все подписки, для которых необходимо включить Наблюдатель за сетями. Это действие создаст экземпляр Наблюдателя за сетями в каждом регионе, который доступен.

![Создание Наблюдателя за сетями](./media/network-watcher-create/figure1.png)

Когда вы включаете Наблюдатель за сетями с помощью портала, созданному экземпляру наблюдателя автоматически присваивается имя *NetworkWatcher_имя_региона*, где *имя_региона* соответствует региону Azure, в котором был включен этот экземпляр наблюдателя. Например, включенный в центрально-западной части США Наблюдатель за сетями получит имя *NetworkWatcher_westcentralus*.

Экземпляр Наблюдателя за сетями автоматически создается в группе ресурсов с именем *NetworkWatcherRG*. Если эта группа ресурсов не существует, она создается.

Если вы хотите указать имя экземпляра Наблюдателя за сетями и имя группы ресурсов, в которую он добавляется, используйте PowerShell, интерфейс командной строки Azure, REST API или методы ARMClient, описанные в следующих разделах. Во всех этих вариантах должна заранее существовать группа ресурсов, в которой вы создаете Наблюдатель за сетями.  

## <a name="create-a-network-watcher-with-powershell"></a>Создание Наблюдателя за сетями с помощью PowerShell

Чтобы создать экземпляр Наблюдателя за сетями, выполните приведенный ниже пример.

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Создание Наблюдателя за сетями с помощью Azure CLI

Чтобы создать экземпляр Наблюдателя за сетями, выполните приведенный ниже пример.

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Создание Наблюдателя за сетями с помощью REST API

Чтобы вызвать REST API с помощью командлетов PowerShell, вам потребуется ARMClient. Пакет ARMClient можно скачать на сайте [Chocolatey](https://chocolatey.org/packages/ARMClient).

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

Создав экземпляр Наблюдателя за сетями, вы можете включить захват пакетов на виртуальных машинах. Сведения об этом см. в статье [Использование записи пакетов для упреждающего мониторинга сети с помощью оповещений и функций Azure](network-watcher-alert-triggered-packet-capture.md).
