<properties
	pageTitle="Создание центра IoT с помощью шаблона ARM и PowerShell | Microsoft Azure"
	description="Следуйте инструкциям в этом учебнике, чтобы приступить к использованию шаблонов диспетчера ресурсов для создания центра IoT с помощью PowerShell."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/03/2016"
     ms.author="dobett"/>

# Создание центра IoT с помощью PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Введение

Диспетчер ресурсов Azure (ARM) можно использовать для создания центров Azure IoT и управления ими программным способом. В этом учебнике показано, как использовать шаблон Resource Manager для создания центра IoT с помощью PowerShell.

> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе диспетчера ресурсов.

Чтобы пройти этот учебник, требуется:

- Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

> [AZURE.TIP] Дополнительные сведения об использовании сценариев PowerShell и шаблонов ARM для создания ресурсов Azure см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure][lnk-powershell-arm].

## Подключение к подписке Azure

В командной строке PowerShell введите следующую команду, чтобы войти в подписку Azure:

```
Login-AzureRmAccount
```

Чтобы узнать, где можно развернуть центр IoT, и ознакомиться с текущими поддерживаемыми версиями API, используйте следующие команды:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Создайте группу ресурсов для хранения центра IoT, используя указанную ниже команду в одном из поддерживаемых расположений для центра IoT. В этом примере создается группа ресурсов с именем **MyIoTRG1**.

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## Отправка шаблона для создания центра IoT

Используйте шаблон JSON для создания нового центра IoT в группе ресурсов. Можно также использовать шаблон для изменения существующего центра IoT.

1. Используйте текстовый редактор для создания шаблона ARM с именем **template.json** с помощью следующего определения ресурса для создания стандартного центра IoT. В этом примере в регион **Восток США** добавляется центр IoT, создаются две группы потребителей (**cg1** и **cg2**) для конечной точки, совместимой с концентраторами событий, и используется версия API **2016-02-03**. При использовании этого шаблона нужно передать имя центра IoT в качестве параметра с именем **hubName**.

    ```
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

2. Сохраните файл шаблона на локальном компьютере. В этом примере предполагается, что файл сохраняется в папке **c:\\templates**.

3. Выполните следующую команду, чтобы развернуть новый центр IoT, передав в качестве параметра имя центра IoT. В данном примере это имя создаваемого центра IoT, например **abcmyiothub** (обратите внимание, что это должно быть глобально уникальное имя, поэтому оно должно включать ваше имя или инициалы).

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. В выходных данных отображаются ключи для созданного центра IoT.

5. Убедитесь, что в приложение добавлен новый центр IoT, посетив [портал][lnk-azure-portal] и просмотрев список ресурсов, или с помощью командлета PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] В этом примере приложения добавляется стандартный центр S1 IoT, на который вам будет выставлен счет. Когда закончите, центр IoT можно удалить через [портал][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## Дальнейшие действия

После развертывания центра IoT с использованием шаблона ARM и PowerShell вам могут понадобиться дополнительные сведения:

- Ознакомьтесь с возможностями [API REST поставщика ресурсов центра IoT][lnk-rest-api].
- Сведения о дополнительных возможностях диспетчера ресурсов Azure см. в статье [Обзор диспетчера ресурсов Azure][lnk-azure-rm-overview].

Дополнительные сведения о разработке для центра IoT см. в следующих руководствах.

- [Знакомство с пакетом SDK для устройств Azure IoT для C][lnk-c-sdk]
- [Пакеты SDK для центра IoT][lnk-sdks]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

- [Разработка решения][lnk-design]
- [Обзор управления устройствами центра IoT с помощью примера пользовательского интерфейса][lnk-dmui]
- [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
- [Управление центрами IoT через портал Azure][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->