<properties
	pageTitle="Создание центра IoT с помощью программы C# и шаблона диспетчера ресурсов Azure | Microsoft Azure"
	description="Следуйте инструкциям этого учебника, чтобы приступить к работе с помощью шаблонов диспетчера ресурсов и создать центр IoT с помощью программы C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

.<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# Создание центра IoT с помощью программы C# и шаблона Resource Manager

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Введение

Диспетчер ресурсов Azure можно использовать для создания центров Azure IoT программным способом и управления ими. В этом учебнике показано, как использовать шаблон Resource Manager для создания центра IoT из программы на C#.

> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе диспетчера ресурсов.

Для работы с этим учебником требуется:

- Microsoft Visual Studio 2015.
- Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].
- [Учетная запись хранения Azure][lnk-storage-account], в которой можно хранить файлы шаблона.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Подготовка проекта Visual Studio

1. Создайте в Visual Studio проект Visual C# Windows с помощью шаблона проекта **Консольное приложение**. Дайте проекту имя **CreateIoTHub**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и найдите **Microsoft.Azure.Management.ResourceManager**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

5. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом.

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Your storage account name** — это имя учетной записи хранения Azure, в которой вы храните файлы шаблона. **Resource group name** — это имя группы ресурсов, используемых при создании центра IoT. Это может быть существующая группа ресурсов или новая. **Deployment name** — это имя развертывания, например **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Отправка шаблона для создания центра IoT

Для создания центра IoT в группе ресурсов используйте шаблон JSON и файл параметров. Можно также использовать шаблон для изменения существующего центра IoT.

1. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте файл JSON с именем **template.json** в проект.

2. Замените содержимое файла **template.json** следующим определением ресурса для добавления стандартного центра IoT в регион **Восток США** (текущий список регионов, поддерживающих центр IoT, см. на странице [Состояние Azure][lnk-status]).

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

3. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте в проект файл JSON с именем **parameters.json**.

4. Замените содержимое файла **parameters.json** следующим параметром, который задает имя нового центра IoT, например **{ваши\_инициалы}mynewiothub**. Обратите внимание, что имя центра IoT должно быть глобально уникальным, поэтому оно должно включать ваше имя или инициалы:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. В **обозревателе сервера** подключитесь к подписке Azure и в учетной записи хранения создайте контейнер с именем **templates**. На панели **Свойства** задайте разрешениям **Общий доступ на чтение** для контейнера **templates** значение **Большой двоичный объект**.

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

5. Добавьте следующий код в метод **CreateIoTHub** для отправки файлов шаблонов и параметров в Azure Resource Manager:

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

6. Добавьте следующий код в метод **CreateIoTHub**, который отображает состояние и ключи для нового центра IoT:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## Завершение и запуск приложения

Теперь можно завершить приложение, вызвав метод **CreateIoTHub**, а затем собрать и запустить приложение.

1. Добавьте в конец метода **Main** следующий код:

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.

3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.

4. Убедитесь, что в приложение добавлен новый центр IoT, посетив [портал][lnk-azure-portal] и просмотрев список ресурсов, или с помощью командлета PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] В этом примере приложения добавляется стандартный центр IoT S1, который подлежит оплате. Когда закончите, центр IoT можно удалить через [портал][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## Дальнейшие действия

После развертывания центра IoT с использованием шаблона Azure Resource Manager и программы на C# вас могут заинтересовать следующие статьи:

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

.<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->