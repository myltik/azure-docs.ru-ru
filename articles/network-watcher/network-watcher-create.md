---
title: "Создание экземпляра Наблюдателя за сетями Azure | Документация Майкрософт"
description: "Эта страница содержит инструкции по созданию экземпляра Наблюдателя за сетями с помощью портала и Azure REST API."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: a39ce143796408f8e44b0d2c877e631e92473462
ms.lasthandoff: 03/28/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Создание экземпляра Наблюдателя за сетями Azure

Наблюдатель за сетями — это региональная служба, обеспечивающая мониторинг и диагностику условий на уровне сетевого сценария на платформе Azure. Мониторинг на уровне сценария позволяет диагностировать проблемы в сети с помощью комплексного представления сетевого уровня. Инструменты диагностики сети и визуализации, доступные в Наблюдателе за сетями, помогают понять, как работает сеть в Azure, диагностировать ее и получить ценную информацию.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="register-the-preview-capability"></a>Регистрация возможностей предварительной версии

Сейчас Наблюдатель за сетями находится на этапе предварительной версии. Чтобы использовать функции этого компонента, его нужно зарегистрировать. Для этого выполните следующие командлеты PowerShell.

```powershell
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Чтобы проверить, была ли регистрация успешно завершена, выполните приведенный ниже командлет PowerShell.

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

Если компонент был правильно зарегистрирован, выходные данные должны выглядеть следующим образом.

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

### <a name="instructions-for-cli-10"></a>Инструкции для интерфейса командной строки 1.0

Для регистрации выполните следующие команды.

```CLI
azure feature register  Microsoft.Network AllowNetworkWatcher
azure provider register Microsoft.Network
```

Чтобы проверить, была ли регистрация успешно завершена, выполните приведенную ниже команду интерфейса командной строки.

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

Если компонент был правильно зарегистрирован, выходные данные должны выглядеть следующим образом.

```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

> [!NOTE]
> Наблюдатель за сетями в настоящее время поддерживает только интерфейс командной строки 1.0, поэтому приведены инструкции по созданию нового экземпляра Наблюдателя за сетями именно для этой версии. Для регистрации поставщика с помощью интерфейса командной строки 2.0 используйте `az feature register -n AllowNetworkWatcher --namespace Microsoft.Network`.

## <a name="create-a-network-watcher-in-the-portal"></a>Создание Наблюдателя за сетями на портале

Выберите **Больше служб** > **Сети** > **Наблюдатель сети**. Можно выбрать все подписки, для которых необходимо включить Наблюдатель за сетями. Это действие создаст экземпляр Наблюдателя за сетями в каждом регионе, который доступен.

![Создание Наблюдателя за сетями][1]

## <a name="create-a-network-watcher-with-powershell"></a>Создание Наблюдателя за сетями с помощью PowerShell

Чтобы создать экземпляр Наблюдателя за сетями, выполните приведенный ниже пример.

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Создание Наблюдателя за сетями с помощью REST API

Чтобы вызвать REST API при помощи PowerShell, потребуется ARMClient. Пакет ARMClient можно скачать на сайте [Chocolatey](https://chocolatey.org/packages/ARMClient).

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

## <a name="next-steps"></a>Дальнейшие действия

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












