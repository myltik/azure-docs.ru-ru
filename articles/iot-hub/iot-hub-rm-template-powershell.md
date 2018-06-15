---
title: Создание центра Интернета вещей Azure с помощью шаблона (PowerShell) | Документация Майкрософт
description: Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager и PowerShell.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: cdc53aeb0ecbea5714a8587c8d03ab6aa292394c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635004"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Диспетчер ресурсов Azure можно использовать для создания Центров Интернета вещей Azure программным способом и управления ими. В этом учебнике показано, как использовать шаблон Azure Resource Manager для создания Центра Интернета вещей с помощью PowerShell.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе Azure Resource Manager.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

> [!TIP]
> Дополнительные сведения об использовании PowerShell и шаблонов Azure Resource Manager для создания ресурсов Azure см. в статье [Управление ресурсами с помощью Azure PowerShell и Resource Manager][lnk-powershell-arm].

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

Чтобы узнать, где можно развернуть Центр Интернета вещей, и ознакомиться с текущими поддерживаемыми версиями API, используйте следующие команды:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Создайте группу ресурсов для хранения Центра Интернета вещей, используя указанную ниже команду в одном из поддерживаемых расположений для Центра Интернета вещей. В этом примере создается группа ресурсов с именем **MyIoTRG1**.

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Отправка шаблона для создания Центра Интернета вещей

Используйте шаблон JSON для создания Центра Интернета вещей в группе ресурсов. Можно также использовать шаблон Azure Resource Manager для изменения существующего Центра Интернета вещей.

1. Используйте текстовый редактор, чтобы создать шаблон Azure Resource Manager с именем **template.json** с помощью следующего определения ресурса для создания стандартного Центра Интернета вещей. В этом примере в регион **Восточная часть США** добавляется Центр Интернета вещей, создаются две группы потребителей (**cg1** и **cg2**) для конечной точки, совместимой с концентраторами событий, и используется версия API **2016-02-03**. При использовании этого шаблона нужно передать имя Центра Интернета вещей в качестве параметра с именем **hubName**. Текущий список расположений, которые поддерживают Центр Интернета вещей, указан на странице [Состояние Azure][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Сохраните файл шаблона Azure Resource Manager на локальном компьютере. В этом примере предполагается, что файл сохраняется в папке **c:\templates**.

3. Выполните следующую команду, чтобы развернуть новый Центр Интернета вещей, передав в качестве параметра имя Центра Интернета вещей. В этом примере имя Центра Интернета вещей — `abcmyiothub`. Имя Центра Интернета вещей должно быть глобально уникальным.

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. В выходных данных отображаются ключи для созданного Центра Интернета вещей.

5. Чтобы убедиться, что в приложение добавлен новый Центр Интернета вещей, посетите [портал Azure][lnk-azure-portal] и просмотрите список ресурсов. Вы также можете воспользоваться командлетом PowerShell **Get-AzureRmResource**.

> [!NOTE]
> В этом примере приложения добавляется стандартный Центр Интернета вещей S1, который подлежит оплате. Когда закончите, Центр Интернета вещей можно удалить через [портал Azure][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## <a name="next-steps"></a>Дополнительная информация

После развертывания Центра Интернета вещей с использованием шаблона Azure Resource Manager и PowerShell вас могут заинтересовать следующие статьи:

* Ознакомьтесь с возможностями [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api].
* Сведения о возможностях Azure Resource Manager см. в статье [Общие сведения об Azure Resource Manager][lnk-azure-rm-overview].

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Развертывание ИИ на пограничных устройствах с использованием Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
