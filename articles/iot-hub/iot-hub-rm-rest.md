---
title: "Создание Центра Интернета вещей Azure с помощью REST API поставщика ресурсов | Документация Майкрософт"
description: "Создание Центра Интернета вещей с помощью REST API поставщика ресурсов."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2abfeebeac222f4371b0945e1aeb6fcf8e51595d
ms.openlocfilehash: 826e359ebeaf9af4df3c3b1559549a57e8228f72


---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Создание Центра Интернета вещей с помощью REST API поставщика ресурсов (.NET)
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение
[REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api] можно использовать для создания Центров Интернета вещей Azure и управления ими программными методами. В этом учебнике показано, как использовать REST API поставщика ресурсов Центра Интернета вещей для создания Центра Интернета вещей из программы на C#.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания на основе Azure Resource Manager.
> 
> 

Для работы с этим учебником требуется:

* Microsoft Visual Studio 2015.
* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Подготовка проекта Visual Studio
1. Создайте в Visual Studio проект Visual C# Windows с помощью шаблона проекта **Консольное приложение** . Дайте проекту имя **CreateIoTHubREST**.
2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.
3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и найдите **Microsoft.Azure.Management.ResourceManager**. Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.
4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.
5. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом.
   
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
6. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Resource group name** — это имя группы ресурсов, используемых при создании Центра Интернета вещей. Это может быть существующая группа ресурсов или новая. **IoT Hub name** — это имя создаваемого Центра Интернета вещей, например **MyIoTHub** (это должно быть глобально уникальное имя, поэтому оно должно включать ваше имя или инициалы). **Deployment name** — это имя развертывания, например **Deployment_01**.
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
   
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Использование REST API поставщика ресурсов для создания Центра Интернета вещей
Используйте [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api] для создания Центра Интернета вещей в группе ресурсов. REST API поставщика ресурсов также можно использовать для изменения имеющегося Центра Интернета вещей.

1. Добавьте в класс Program.cs следующий метод:
   
    ```
    static void CreateIoTHub(string token)
    {
   
    }
    ```
2. Добавьте следующий код в метод **CreateIoTHub** для создания объекта **HttpClient** с маркером аутентификации в заголовках:
   
    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```
3. Добавьте следующий код в метод **CreateIoTHub** для описания Центра Интернета вещей, а также создания и генерирования представления JSON. Текущий список расположений, которые поддерживают Центр Интернета вещей, указан на странице [Состояние Azure][lnk-status].
   
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
    } while (body == "{\"status\":\"Running\"}");
    ```
6. Добавьте следующий код в конец метода **CreateIoTHub** для получения созданных ключей центра IoT и их вывода на консоль:
   
    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
   
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Завершение и запуск приложения
Теперь можно завершить приложение, вызвав метод **CreateIoTHub** , а затем собрать и запустить приложение.

1. Добавьте в конец метода **Main** следующий код:
   
    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.
3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.
4. Убедитесь, что в приложение добавлен новый Центр Интернета вещей, посетив [портал Azure][lnk-azure-portal] и просмотрев список ресурсов, или с помощью командлета PowerShell **Get-AzureRmResource**.

> [!NOTE]
> В этом примере приложения добавляется стандартный центр IoT S1, который подлежит оплате. По завершении можете удалить Центр Интернета вещей через [портал Azure][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
После развертывания Центра Интернета вещей с использованием REST API вам могут понадобиться дополнительные сведения:

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
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Dec16_HO1-->


