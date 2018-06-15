---
title: Создание центра Интернета вещей Azure с помощью шаблона (.NET) | Документация Майкрософт
description: Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager и программы C#.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a64749b7218fccfdad6b6eeebfac39a44aa0522
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635548"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Диспетчер ресурсов Azure можно использовать для создания Центров Интернета вещей Azure программным способом и управления ими. В этом учебнике показано, как использовать шаблон Azure Resource Manager для создания Центра Интернета вещей из программы на C#.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания на основе Azure Resource Manager.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Учетная запись хранения Azure][lnk-storage-account], в которой можно хранить файлы шаблона Azure Resource Manager.
* [Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Подготовка проекта Visual Studio

1. В Visual Studio создайте проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Дайте проекту имя **CreateIoTHub**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и на странице **Обзор** найдите **Microsoft.Azure.Management.ResourceManager**. Выберите пакет, щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

5. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Your Azure Storage account name** — это имя учетной записи хранения Azure, в которой вы храните файлы шаблона Azure Resource Manager. **Resource group name** — это имя группы ресурсов, используемой при создании Центра Интернета вещей. Это может быть существующая группа ресурсов или новая. **Deployment name** — это имя развертывания, например **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Отправка шаблона для создания Центра Интернета вещей

Для создания Центра Интернета вещей в группе ресурсов используйте шаблон JSON и файл параметров. Можно также использовать шаблон Azure Resource Manager для изменения существующего Центра Интернета вещей.

1. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте файл JSON с именем **template.json** в проект.

2. Для добавления стандартного Центра Интернета вещей в регион **Восточная часть США** замените содержимое файла **template.json** следующим определением ресурса. Текущий список регионов, которые поддерживают Центр Интернета вещей, указан на странице [Состояние Azure][lnk-status].

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

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. В **обозревателе сервера** подключитесь к подписке Azure и в учетной записи хранения Azure создайте контейнер с именем **templates**. На панели **Свойства** задайте разрешениям **Общий доступ на чтение** для контейнера **templates** значение **Большой двоичный объект**.

6. В **обозревателе сервера** щелкните правой кнопкой мыши контейнер **templates**, а затем выберите **Просмотреть контейнер больших двоичных объектов**. Нажмите кнопку **Передать BLOB-объект**, выберите файлы **parameters.json** и **templates.json**, а затем нажмите кнопку **Открыть**, чтобы передать файлы JSON в контейнер **templates**. URL-адреса больших двоичных объектов, содержащих данные JSON, таковы:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Добавьте в класс Program.cs следующий метод:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Добавьте следующий код в метод **CreateIoTHub** для отправки файлов шаблонов и параметров в Azure Resource Manager:

    ```csharp
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

9. Добавьте следующий код в метод **CreateIoTHub** , который отображает состояние и ключи для нового Центра Интернета вещей:

    ```csharp
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

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.

3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.

4. Чтобы убедиться, что в приложение добавлен новый Центр Интернета вещей, посетите [портал Azure][lnk-azure-portal] и просмотрите список ресурсов. Вы также можете воспользоваться командлетом PowerShell **Get-AzureRmResource**.

> [!NOTE]
> В этом примере приложения добавляется стандартный Центр Интернета вещей S1, который подлежит оплате. Когда закончите, Центр Интернета вещей можно удалить через [портал Azure][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## <a name="next-steps"></a>Дополнительная информация
После развертывания Центра Интернета вещей с использованием шаблона Azure Resource Manager и программы на C# вас могут заинтересовать следующие статьи:

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
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
