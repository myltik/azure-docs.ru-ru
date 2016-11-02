<properties
    pageTitle="Создание Центра Интернета вещей с помощью интерфейса командной строки | Microsoft Azure"
    description="Следуйте указаниям в этой статье, чтобы создать Центр Интернета вещей с помощью интерфейса командной строки Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>


# <a name="create-an-iot-hub-using-cli"></a>Создание Центра Интернета вещей с помощью интерфейса командной строки

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение

Интерфейс командной строки Azure можно использовать для создания Центров Интернета вещей Azure программным способом и для управления ими. В этой статье показано, как создать Центр Интернета вещей с помощью интерфейса командной строки Azure.

Для работы с этим учебником требуется:

- Активная учетная запись Azure. Вы можете создать [бесплатную пробную учетную запись Azure][lnk-free-trial] всего за несколько минут.
- [Интерфейс командной строки Azure версии 0.10.4][lnk-Cli-install] или более поздней. При наличии интерфейса командной строки Azure можно проверить его текущую версию в командной строке, выполнив следующую команду:
```
    azure --version
```

> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). Интерфейс командной строки Azure нужно переключить в режим Azure Resource Manager:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Настройка учетной записи и подписки Azure 

1. Выполните вход, введя в командной строке следующую команду:
```
    azure login
```
Пройдите аутентификацию, используя предлагаемый браузер и код.

2. Если у вас есть несколько подписок Azure, при подключении вы получаете доступ ко всем подпискам, связанным с вашими учетными данными. Вы можете просмотреть подписки, а также узнать, какая из них используется по умолчанию, с помощью команды
```
    azure account list 
```

Чтобы задать контекст подписки, в котором будут выполняться остальные команды, используйте следующую команду:

```
    azure account set <subscription name>
```

3. Создайте группу ресурсов с именем **exampleResourceGroup**, если она не создана: 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] Дополнительные сведения об управлении ресурсами Azure с помощью интерфейса командной строки Azure см. в статье [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure][lnk-CLI-arm]. 


## <a name="create-and-iot-hub"></a>Создание Центра Интернета вещей

Ниже перечислены необходимые параметры:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Чтобы просмотреть все параметры для создания, можно воспользоваться командой справки в командной строке
```
    azure iothub create -h 
```
Краткий пример:

 Чтобы создать Центр Интернета вещей **exampleIoTHubName** в группе ресурсов **exampleResourceGroup**, просто выполните следующую команду:
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Эта команда интерфейса командной строки создает стандартный Центр Интернета вещей S1, за который взимается плата. Чтобы удалить Центр Интернета вещей **exampleIoTHubName**, выполните следующую команду: 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о разработке для центра IoT см. в следующих руководствах.
- [Пакеты SDK для Центра Интернета вещей][lnk-sdks]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

- [Управление центрами IoT через портал Azure][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


