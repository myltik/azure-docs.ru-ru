<properties
	pageTitle="Создание центра IoT с помощью API REST | Microsoft Azure"
	description="Следуйте указаниям этого учебника, чтобы создать центр IoT Azure с помощью API REST."
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
     ms.date="11/23/2015"
     ms.author="dobett"/>

# Учебник. Создание центра IoT с помощью программы C#

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Введение

Вы можете использовать [API REST поставщика ресурсов центра IoT][lnk-rest-api]для создания и управления центрами Azure IoT программными методами. В этом учебнике показано, как использовать API REST диспетчера ресурсов для создания центра IoT из программы на C#.

Чтобы пройти этот учебник, требуется:

- Microsoft Visual Studio 2015.
- Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Подготовка проекта Visual Studio

1. Создайте в Visual Studio новый проект Visual C# Windows с помощью шаблона проекта **Консольное приложение**. Дайте проекту имя **CreateIoTHubREST**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительный выпуск** и выполните поиск по тексту **Microsoft.Azure.Management.Resources**. Выберите версию **2.18.11-preview**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**. Выберите версию **2.19.208020213**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

5. В диспетчере пакетов NuGet найдите **Microsoft.Azure.Common**. Выберите версию **2.1.0**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

6. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом.

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    ```
    
7. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password** ранее в этом учебнике. **Resource group name** — это имя группы ресурсов, используемых при создании центра IoT. Это может быть существующая группа ресурсов или новая. **IoT Hub name** — это имя создаваемого центра IoT, например **MyIoTHub**. **Deployment name** — это имя развертывания, например **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Использование API REST для создания центра IoT

Используйте API REST поставщика ресурсов центра IoT для создания нового центра IoT в группе ресурсов. Можно также использовать [API REST][lnk-rest-api] для изменения существующего центра IoT.

1. Добавьте в класс Program.cs следующий метод:
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client, string token)
    {
        
    }
    ```

2. Добавьте следующий код в метод **CreateIoTHub** для добавления маркера проверки подлинности в запрос:

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
      new AuthenticationHeaderValue("Bearer", token);
    ```

3. Добавьте следующий код в метод **CreateIoTHub** для описания центра IoT, а также создания и генерирования представления JSON:

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var content = new StringContent(
      JsonConvert.SerializeObject(description),
      Encoding.UTF8, "application/json");
    ```

4. Добавьте следующий код в метод **CreateIoTHub**, чтобы отправить запрос REST в Azure и проверить ответ:

    ```
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2015-08-15-preview",
      subscriptionId, rgName, iotHubName);
    var httpsRepsonse = client.HttpClient.PutAsync(
      requestUri, content).Result;

    if (!httpsRepsonse.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", httpsRepsonse.Content.ReadAsStringAsync().Result);
      return false;
    }
    ```

5. Добавьте следующий код в конец метода **CreateIoTHub**,чтобы дождаться успешного завершения развертывания:

    ```
    ResourceGetResult resourceGetResult = null;
    do
    {
    resourceGetResult = client.Resources.GetAsync(
      rgName,
      new ResourceIdentity()
      {
        ResourceName = iotHubName,
        ResourceProviderApiVersion = "2015-08-15-preview",
        ResourceProviderNamespace = "Microsoft.Devices",
        ResourceType = "IotHubs"
      }).Result;
    Console.WriteLine("IoTHub state {0}", 
      resourceGetResult.Resource.ProvisioningState);
    } while (resourceGetResult.Resource.ProvisioningState != "Succeeded" 
          && resourceGetResult.Resource.ProvisioningState != "Failed");
    
    if (resourceGetResult.Resource.ProvisioningState != "Succeeded")
    {
        Console.WriteLine("Failed to create iothub");
        return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## Завершение и запуск приложения

Теперь можно завершить приложение путем вызова методов **CreateIoTHub** и **ShowIoTHubKeys**, а затем собрать и запустить его.

1. Добавьте в конец метода **Main** следующий код:

    ```
    if (CreateIoTHub(client, token.AccessToken))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.

3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.

4. Убедитесь, что в приложение добавлен новый центр IoT, посетив [портал][lnk-azure-portal] и просмотрев список ресурсов, или с помощью командлета PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE]В этом примере приложения добавляется стандартный центр S1 IoT, на который вам будет выставлен счет. Когда закончите, центр IoT можно удалить через [портал][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## Дальнейшие действия

- Ознакомьтесь с возможностями [API REST поставщика ресурсов центра IoT][lnk-rest-api].
- Сведения о дополнительных возможностях диспетчера ресурсов Azure см. в статье [Обзор диспетчера ресурсов Azure][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/ru-RU/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_1203_2015-->