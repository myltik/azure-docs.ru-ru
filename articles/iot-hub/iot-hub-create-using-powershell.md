---
title: "Создание Центра Интернета вещей Azure с помощью командлета PowerShell | Документация Майкрософт"
description: "Узнайте, как с помощью командлетов PowerShell создать Центр Интернета вещей."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7914b9f1f747d246f58c19b7faf56357bd39a201
ms.contentlocale: ru-ru
ms.lasthandoff: 05/04/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Создание Центра Интернета вещей с помощью командлета New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение

Для создания Центров Интернета вещей и управления ими можно использовать командлеты Azure PowerShell. В этом руководстве показано, как создать Центр Интернета вещей с помощью PowerShell.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания на основе Azure Resource Manager.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Командлеты Azure PowerShell][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Подключение к подписке Azure
В командной строке PowerShell введите следующую команду, чтобы войти в подписку Azure.

```powershell
Login-AzureRmAccount
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

## <a name="create-an-iot-hub"></a>Создание центра IoT

Чтобы создать Центр Интернета вещей в группе ресурсов, созданной на предыдущем шаге, используйте указанную ниже команду. В этом примере создается центр категории **S1** с именем **MyTestIoTHub**, размещенный в регионе **Восточная часть США**:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

> [!NOTE]
> Имя Центра Интернета вещей должно быть уникальным.

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

## <a name="next-steps"></a>Дальнейшие действия

После развертывания Центра Интернета вещей с помощью командлета PowerShell вам могут понадобиться дополнительные сведения:

* Узнайте о других [командлетах PowerShell для работы с Центром Интернета вещей][lnk-iothub-cmdlets].
* Ознакомьтесь с возможностями [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api].

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Знакомство с пакетом SDK для устройств Azure IoT для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

