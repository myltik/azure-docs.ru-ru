---
title: "Создание Центра Интернета вещей с помощью программы C# и шаблона Azure Resource Manager | Документация Майкрософт"
description: "Следуйте инструкциям этого учебника, чтобы приступить к работе с помощью шаблонов Azure Resource Manager и создать Центр Интернета вещей с помощью программы C#."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a447b40c-c728-487e-875d-db554db5adc3
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 118ab90d7886ad48e7476abd732b16b69375d214


---
# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Создание Центра Интернета вещей с помощью программы C# и шаблона Azure Resource Manager
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение
Диспетчер ресурсов Azure можно использовать для создания центров Azure IoT программным способом и управления ими. В этом учебнике показано, как использовать шаблон Azure Resource Manager для создания Центра Интернета вещей из программы на C#.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания на основе Azure Resource Manager.
> 
> 

Для работы с этим учебником требуется:

* Microsoft Visual Studio 2015.
* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Учетная запись хранения Azure][lnk-storage-account], в которой можно хранить файлы шаблона Azure Resource Manager.
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Подготовка проекта Visual Studio
1. Создайте в Visual Studio проект Visual C# Windows с помощью шаблона проекта **Консольное приложение** . Дайте проекту имя **CreateIoTHub**.
2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.
3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и найдите **Microsoft.Azure.Management.ResourceManager**. Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.
4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.
5. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом.
   
    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
6. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Your Azure Storage account name** — это имя учетной записи хранения Azure, в которой вы храните файлы шаблона Azure Resource Manager. **Resource group name** — это имя группы ресурсов, используемых при создании Центра Интернета вещей. Это может быть существующая группа ресурсов или новая. **Deployment name** — это имя развертывания, например **Deployment_01**.
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Отправка шаблона Azure Resource Manager для создания Центра Интернета вещей
Для создания центра IoT в группе ресурсов используйте шаблон JSON и файл параметров. Можно также использовать шаблон Azure Resource Manager для изменения существующего Центра Интернета вещей.

1. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте файл JSON с именем **template.json** в проект.
2. Замените содержимое файла **template.json** следующим определением ресурса для добавления стандартного Центра Интернета вещей в регион **Восточная часть США**. Текущий список регионов, которые поддерживают Центр Интернета вещей, указан на странице [Состояние Azure][lnk-status].
   
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
3. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте в проект файл JSON с именем **parameters.json** .
4. Замените содержимое файла **parameters.json** следующим параметром, который задает имя нового Центра Интернета вещей, например **{ваши_инициалы}mynewiothub**. Имя Центра Интернета вещей должно быть глобально уникальным, поэтому оно должно включать ваше имя или инициалы:
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
5. В **обозревателе сервера** подключитесь к подписке Azure и в учетной записи хранения Azure создайте контейнер с именем **templates**. На панели **Свойства** задайте разрешениям **Общий доступ на чтение** для контейнера **templates** значение **Большой двоичный объект**.
6. В **обозревателе сервера** щелкните правой кнопкой мыши контейнер **templates**, а затем выберите **Просмотреть контейнер больших двоичных объектов**. Нажмите кнопку **Передать BLOB-объект**, выберите файлы **parameters.json** и **templates.json**, а затем нажмите кнопку **Открыть**, чтобы передать файлы JSON в контейнер **templates**. URL-адреса больших двоичных объектов, содержащих данные JSON, таковы:
   
    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Добавьте в класс Program.cs следующий метод:
   
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
   
    }
    ```
8. Добавьте следующий код в метод **CreateIoTHub** для отправки файлов шаблонов и параметров в Azure Resource Manager:
   
    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```
9. Добавьте следующий код в метод **CreateIoTHub** , который отображает состояние и ключи для нового центра IoT:
   
    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);
   
    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Завершение и запуск приложения
Теперь можно завершить приложение, вызвав метод **CreateIoTHub** , а затем собрать и запустить приложение.

1. Добавьте в конец метода **Main** следующий код:
   
    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.
3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.
4. Убедитесь, что в приложение добавлен новый Центр Интернета вещей, посетив [портал Azure][lnk-azure-portal] и просмотрев список ресурсов, или с помощью командлета PowerShell **Get-AzureRmResource**.

> [!NOTE]
> В этом примере приложения добавляется стандартный центр IoT S1, который подлежит оплате. Когда закончите, Центр Интернета вещей можно удалить через [портал Azure][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
После развертывания центра IoT с использованием шаблона Azure Resource Manager и программы на C# вас могут заинтересовать следующие статьи:

* Ознакомьтесь с возможностями [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api].
* Сведения о возможностях Azure Resource Manager см. в статье [Общие сведения об Azure Resource Manager][lnk-azure-rm-overview].

Дополнительные сведения о разработке для центра IoT см. в следующих руководствах.

* [Знакомство с пакетом SDK для устройств Azure IoT для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


