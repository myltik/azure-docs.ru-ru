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
     ms.date="08/16/2016"
     ms.author="dobett"/>

# Учебник. Создание центра IoT с помощью программы C# и API REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Введение

Вы можете использовать [API REST поставщика ресурсов центра IoT][lnk-rest-api]для создания и управления центрами Azure IoT программными методами. В этом учебнике показано, как использовать API REST диспетчера ресурсов для создания центра IoT из программы на C#.

> [AZURE.NOTE] В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе диспетчера ресурсов.

Для работы с этим учебником требуется:

- Microsoft Visual Studio 2015.
- Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Подготовка проекта Visual Studio

1. Создайте в Visual Studio проект Visual C# Windows с помощью шаблона проекта **Консольное приложение**. Дайте проекту имя **CreateIoTHubREST**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и найдите **Microsoft.Azure.Management.ResourceManager**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**. Щелкните **Установить**, на странице **Просмотр изменений** щелкните **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

6. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом.

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Resource group name** — это имя группы ресурсов, используемых при создании центра IoT. Это может быть существующая группа ресурсов или новая. **IoT Hub name** — это имя создаваемого центра IoT, например **MyIoTHub** (это должно быть глобально уникальное имя, поэтому оно должно включать ваше имя или инициалы). **Deployment name** — это имя развертывания, например **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Использование API REST для создания центра IoT

Используйте [REST API центра IoT][lnk-rest-api] для создания центра IoT в группе ресурсов. API REST также можно использовать для изменения существующего центра IoT.

1. Добавьте в класс Program.cs следующий метод:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Добавьте следующий код в метод **CreateIoTHub** для создания объекта **HttpClient** с маркером проверки подлинности в заголовках:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Добавьте в метод **CreateIoTHub** следующий код, чтобы описать центр IoT для создания и генерирования представления JSON (текущий список расположений, поддерживающих центр IoT, см. на странице [Состояние Azure][lnk-status]).

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
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Добавьте следующий код в метод **CreateIoTHub** для отправки запроса REST в Azure, проверки ответа и получения URL-адреса, который можно использовать для контроля состояния задачи развертывания:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Добавьте следующий код в конец метода **CreateIoTHub** для ожидания завершения развертывания с помощью адреса **asyncStatusUri**, полученного на предыдущем шаге:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{"status":"Running"}");
    ```

6. Добавьте следующий код в конец метода **CreateIoTHub** для получения созданных ключей центра IoT и их вывода на консоль:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## Завершение и запуск приложения

Теперь можно завершить приложение, вызвав метод **CreateIoTHub**, а затем собрать и запустить приложение.

1. Добавьте в конец метода **Main** следующий код:

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.

3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.

4. Убедитесь, что в приложение добавлен новый центр IoT, посетив [портал][lnk-azure-portal] и просмотрев список ресурсов, или с помощью командлета PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] В этом примере приложения добавляется стандартный центр IoT S1, который подлежит оплате. По завершении можете удалить центр IoT через [портал][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## Дальнейшие действия

После развертывания центра IoT с использованием API REST вам могут понадобиться дополнительные сведения:

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
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->