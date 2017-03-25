---
title: "Разработка Функций Azure с помощью служб мультимедиа"
description: "В этой статье описывается разработка Функций Azure с помощью служб мультимедиа на портале Azure."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Разработка Функций Azure с помощью служб мультимедиа
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

В этой статье рассматривается разработка функций Azure с помощью служб мультимедиа на портале Azure. 

Также вы можете развернуть существующие Функции Azure служб мультимедиа [отсюда](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Для этого нажмите кнопку **Развертывание в Azure**. Этот репозиторий содержит примеры Функций Azure, которые используют службы мультимедиа Azure, чтобы показать рабочие процессы, связанные с приемом содержимого напрямую от хранилища BLOB-объектов, шифрованием и записью содержимого обратно в хранилище BLOB-объектов. В нем также содержатся примеры отслеживания уведомлений заданий через объекты webhook и очереди Azure.

Вы также можете разрабатывать собственные функции на основе примеров в [этом репозитории](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration). В этой статье описывается создание Функций Azure, использующих службы мультимедиа. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы создавать функции, вам нужна активная учетная запись Azure. Если у вас ее нет, воспользуйтесь [бесплатной учетной записью Azure](https://azure.microsoft.com/free/).

Если вы хотите создать Функции Azure, которые выполняют определенные действия в учетной записи служб мультимедиа Azure (AMS) или прослушивают события, отправляемые службами мультимедиа, вам необходимо создать учетную запись AMS, следуя приведенным [здесь](media-services-portal-create-account.md) инструкциям.

## <a name="create-a-function-app"></a>Создание приложения-функции

Создайте приложение-функцию, как описано [здесь](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app).

## <a name="create-a-function"></a>Создание функции

После развертывания приложения-функции его можно найти в Функциях Azure **службы приложений**. 

1. Выберите свое приложение-функцию и щелкните **Новая функция**.
3. Выберите язык **C#** и сценарий **Webhook + API**.
3. Выберите **GenericWebHook-CSharp** (будет запускаться при каждом получении webhook-запроса) или **HttpTrigger-CSharp** (будет запускаться при каждом получении HTTP-запроса) и присвойте имя функции.
4. Щелкните **Создать**. 

## <a name="get-function-url"></a>Получение URL-адреса функции

Чтобы запустить выполнение функций в средстве тестирования HTTP или в другом окне браузера, вам потребуется значение URL-адреса функции. 

![Параметры](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>Файлы

Функция Azure связана с файлами кода и другими файлами, описание которых представлено в данной статье. По умолчанию она связана с файлами **function.json** и **run.csx**. Вам потребуется добавить файл **project.json**. Ниже приведены определения этих файлов.

![файлов](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Файл function.json определяет привязки функций и другие параметры конфигурации. В среде выполнения этот файл используется для определения событий, которые необходимо отслеживать, и способа передачи данных в выполнение функции и возвращения данных из него. 

Ниже приведен пример файла **function.json**.

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

Файл project.json содержит зависимости. Ниже приведен пример файла **function.json**, который включает в себя библиотеки AMS.

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Это код C# для функции. Пример функции webhook приведен в [этой статье](media-services-dotnet-check-job-progress-with-webhooks.md). 

После определения функции щелкните **Выполнить**.
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>Настройка параметров приложения-функции

При разработке функций служб мультимедиа в раздел **Параметры приложения** можно добавить параметры, которые будут использоваться для всех функций. 

Например:

![Параметры](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>Дальнейшие действия

На этом этапе вы готовы начать разработку приложения служб мультимедиа. Дополнительные сведения см. в статье [Использование объектов Webhook Azure для наблюдения за уведомлениями о заданиях служб мультимедиа с использованием .NET](media-services-dotnet-check-job-progress-with-webhooks.md).   

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


