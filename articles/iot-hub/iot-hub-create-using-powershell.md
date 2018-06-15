---
title: Создание Центра Интернета вещей Azure с помощью командлета PowerShell | Документация Майкрософт
description: Узнайте, как с помощью командлетов PowerShell создать Центр Интернета вещей.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 78cf7844223b660eef3dea0a32cd7c325e88e083
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634052"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Создание Центра Интернета вещей с помощью командлета New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение

Для создания Центров Интернета вещей и управления ими можно использовать командлеты Azure PowerShell. В этом руководстве показано, как создать Центр Интернета вещей с помощью PowerShell.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе Azure Resource Manager.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Командлеты Azure PowerShell][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Подключение к подписке Azure
В командной строке PowerShell введите следующую команду, чтобы войти в подписку Azure.

```powershell
Connect-AzureRmAccount
```

Если у вас есть несколько подписок Azure, то при входе в Azure вы получите доступ ко всем подпискам Azure, связанным с вашими учетными данными. Используйте следующую команду, чтобы просмотреть подписки Azure, доступные для использования:

```powershell
Get-AzureRMSubscription
```

Используйте следующую команду, чтобы выбрать подписку, которая будет использоваться для выполнения команд для создания Центра Интернета вещей. Вы можете использовать имя подписки или идентификатор из выходных данных предыдущей команды:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Для развертывания Центра Интернета вещей необходима группа ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую.

Чтобы узнать, где можно развернуть Центр Интернета вещей, используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Чтобы создать группу ресурсов для Центра Интернета вещей в одном из поддерживаемых расположений, используйте указанную ниже команду. В этом примере создается группа ресурсов с именем **MyIoTRG1**, размещенная в регионе **Восточная часть США**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Чтобы создать Центр Интернета вещей в группе ресурсов, созданной на предыдущем шаге, используйте указанную ниже команду. В этом примере создается центр категории **S1** с именем **MyTestIoTHub**, размещенный в регионе **Восточная часть США**:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Имя Центра Интернета вещей должно быть уникальным.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Для вывода списка всех Центров Интернета вещей в подписке используйте следующую команду:

```powershell
Get-AzureRmIotHub
```

В предыдущем примере добавляется Центр Интернета вещей уровня S1 Standard, который подлежит оплате. Чтобы удалить Центр Интернета вещей, используйте следующую команду:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Вы также можете удалить группу ресурсов и все входящие в нее ресурсы, используя следующую команду:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Дополнительная информация

После развертывания Центра Интернета вещей с помощью командлета PowerShell вам могут понадобиться дополнительные сведения:

* Узнайте о других [командлетах PowerShell для работы с Центром Интернета вещей][lnk-iothub-cmdlets].
* Ознакомьтесь с возможностями [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api].

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Развертывание ИИ на пограничных устройствах с использованием Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
