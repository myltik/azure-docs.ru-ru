---
title: Создание Центра Интернета вещей Azure с помощью REST API поставщика ресурсов | Документация Майкрософт
description: Создание центра Интернета вещей с помощью REST API поставщика ресурсов.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 06f05da839ebca2ae53e255acce7f07d1989673c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635225"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Создание Центра Интернета вещей с помощью REST API поставщика ресурсов (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

[REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api] можно использовать для создания центров Интернета вещей Azure и управления ими программными методами. В этом учебнике показано, как использовать REST API поставщика ресурсов Центра Интернета вещей для создания Центра Интернета вещей из программы на C#.

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания на основе Azure Resource Manager.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. <br/>Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Azure PowerShell 1.0][lnk-powershell-install] или более поздней версии.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Подготовка проекта Visual Studio

1. В Visual Studio создайте проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Дайте проекту имя **CreateIoTHubREST**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и на странице **Обзор** найдите **Microsoft.Azure.Management.ResourceManager**. Выберите пакет, щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

5. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом:

    ```csharp
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

6. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Resource group name** — это имя группы ресурсов, используемой при создании Центра Интернета вещей. Вы можете использовать существующую группу ресурсов или новую. **IoT Hub name** — это имя создаваемого Центра Интернета вещей, например **MyIoTHub**. Имя Центра Интернета вещей должно быть глобально уникальным. **Deployment name** — это имя развертывания, например **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Использование REST API поставщика ресурсов для создания Центра Интернета вещей

Используйте [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api] для создания Центра Интернета вещей в группе ресурсов. REST API поставщика ресурсов также можно использовать для изменения имеющегося Центра Интернета вещей.

1. Добавьте в класс Program.cs следующий метод:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Добавьте в метод **CreateIoTHub** следующий код. Этот код создает объект **HttpClient** с маркером аутентификации в заголовках:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Добавьте в метод **CreateIoTHub** следующий код. Этот код описывает создаваемый Центр Интернета вещей и создает представление JSON. Текущий список расположений, которые поддерживают Центр Интернета вещей, указан на странице [Состояние Azure][lnk-status].

    ```csharp
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

4. Добавьте в метод **CreateIoTHub** следующий код. Этот код отправляет запрос REST в Azure. Затем код проверяет ответ и получает URL-адрес, который можно использовать для контроля состояния задачи развертывания:

    ```csharp
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

5. Добавьте в конец метода **CreateIoTHub** следующий код. Этот код ожидает завершения развертывания с помощью адреса **asyncStatusUri**, полученного на предыдущем шаге:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Добавьте в конец метода **CreateIoTHub** следующий код. Этот код получает созданные ключи Центра Интернета вещей и выводит их на консоль:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Завершение и запуск приложения

Теперь можно завершить приложение, вызвав метод **CreateIoTHub** , а затем собрать и запустить приложение.

1. Добавьте в конец метода **Main** следующий код:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.

3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.

4. Чтобы убедиться, что в приложение добавлен новый Центр Интернета вещей, посетите [портал Azure][lnk-azure-portal] и просмотрите список ресурсов. Вы также можете воспользоваться командлетом PowerShell **Get-AzureRmResource**.

> [!NOTE]
> В этом примере приложения добавляется стандартный Центр Интернета вещей S1, который подлежит оплате. По завершении можете удалить Центр Интернета вещей через [портал Azure][lnk-azure-portal] или с помощью командлета PowerShell **Remove-AzureRmResource**.

## <a name="next-steps"></a>Дополнительная информация
После развертывания Центра Интернета вещей с использованием REST API вам могут понадобиться дополнительные сведения:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
