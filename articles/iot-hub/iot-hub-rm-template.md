<properties
	pageTitle="Создание центра IoT с помощью программы C# и шаблона диспетчера ресурсов Azure | Microsoft Azure"
	description="Следуйте инструкциям этого учебника, чтобы приступить к работе с помощью шаблонов диспетчера ресурсов и создать центр IoT с помощью программы C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/03/2016"
     ms.author="dobett"/>

# Создание центра IoT с помощью программы C# и шаблона диспетчера ресурсов Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Введение

Диспетчер ресурсов Azure (ARM) можно использовать для создания центров Azure IoT и управления ими программным способом. В этом учебнике показано, как использовать шаблон диспетчера ресурсов для создания центра IoT из программы на C#.

> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе диспетчера ресурсов.

Чтобы пройти этот учебник, требуется:

- Microsoft Visual Studio 2015.
- Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].
- [Учетная запись хранения Azure][lnk-storage-account], в которой можно хранить файлы шаблона.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Подготовка проекта Visual Studio

1. Создайте в Visual Studio новый проект Visual C# Windows с помощью шаблона проекта **Консольное приложение**. Дайте проекту имя **CreateIoTHub**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительный выпуск** и введите **Microsoft.Azure.Management.Resources** в строке поиска. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

5. В диспетчере пакетов NuGet найдите **Microsoft.Azure.Common**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

6. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом.

    ```
    using System;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password** ранее в этом учебнике. **Имя вашей учетной записи хранения** — имя учетной записи хранения Azure, в которой будут храниться файлы шаблона. **Resource group name** — это имя группы ресурсов, используемых при создании центра IoT. Это может быть существующая группа ресурсов или новая. **Deployment name** — это имя развертывания, например **Deployment\_01**.

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

Для создания нового центра IoT в группе ресурсов используйте шаблон JSON и файл параметров. Можно также использовать шаблон для изменения существующего центра IoT.

1. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте новый файл JSON с именем **template.json** в проект.

2. Замените содержимое файла **template.json** следующим определением ресурса для добавления нового стандартного центра IoT для региона **Восток США**:

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

3. В обозревателе решений щелкните правой кнопкой мыши проект, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Добавьте в проект новый файл JSON с именем **parameters.json**.

4. Замените содержимое файла **parameters.json** следующим параметром, который задает имя нового центра IoT — **{ваши инициалы}mynewiothub** (обратите внимание, что это должно быть глобально уникальное имя, поэтому оно должно включать ваше имя или инициалы).

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. В **обозревателе сервера** подключитесь к подписке Azure и в учетной записи хранения создайте новый контейнер с названием **шаблоны**. На панели **Свойства** установите разрешение **Общий доступ на чтение** для контейнера **шаблоны** в значение **Большой двоичный объект**.

6. В **обозревателе сервера** щелкните правой кнопкой мыши контейнер **шаблоны**, а затем нажмите кнопку **Просмотр контейнера больших двоичных объектов**. Нажмите кнопку **Отправить большой двоичный объект**, выберите два файла **parameters.json** и **templates.json**, и нажмите кнопку **Открыть** для передачи файлов JSON в контейнер **шаблоны**. URL-адреса больших двоичных объектов, содержащих данные JSON, таковы:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. Добавьте в класс Program.cs следующий метод:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Добавьте следующий код в метод **CreateIoTHub** для отправки файлов шаблонов и параметров диспетчеру ресурсов Azure:

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

> [AZURE.NOTE] В этом примере приложения добавляется стандартный центр S1 IoT, на который вам будет выставлен счет. Когда закончите, центр IoT можно удалить через [портал][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## Дальнейшие действия

После развертывания центра IoT с использованием шаблона диспетчера ресурсов Azure и программы на C# вас могут заинтересовать следующие статьи:

- Ознакомьтесь с возможностями [API REST поставщика ресурсов центра IoT][lnk-rest-api].
- Сведения о дополнительных возможностях диспетчера ресурсов Azure см. в статье [Обзор диспетчера ресурсов Azure][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0504_2016-->