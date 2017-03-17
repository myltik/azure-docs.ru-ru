---
title: "Использование записи пакетов для упреждающего мониторинга сети с помощью функций Azure | Документация Майкрософт"
description: "В этой статье описывается, как создавать активируемую с использованием оповещений запись пакетов в службе наблюдения за сетями Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Использование записи пакетов для упреждающего мониторинга сети с помощью функций Azure

Функция записи пакетов службы наблюдения за сетями (Наблюдатель за сетями) отслеживает входящий и исходящий трафик виртуальной машины. Файл записи можно создавать с использованием фильтра, чтобы отслеживать только нужный для мониторинга трафик. Эти данные затем сохраняются в хранилище BLOB-объектов или локально на гостевом компьютере. Эта возможность допускает удаленный запуск из других сценариев службы автоматизации, включая функции Azure. Возможность записи пакетов позволяет создавать упреждающие записи пакетов, активируемые при обнаружении определенных сетевых аномалий. Она также помогает выполнять сбор сетевой статистики, получать сведения о сетевых вторжениях, выполнять отладку передачи данных между клиентом и сервером и многое другое.

Развернутые в Azure ресурсы выполняются круглосуточно и без выходных. И вам и вашим сотрудникам не удастся эффективно отслеживать состояние всех ресурсов без перерывов. Что вы будете делать, если сбой произойдет в два часа ночи?

С помощью Наблюдателя за сетями, оповещений и функций экосистемы Azure вы можете не только заранее подготовиться к проблемам в сети, но также подготовить данные и средства для устранения проблем.

## <a name="before-you-begin"></a>Перед началом работы

В этом примере мы рассмотрим виртуальную машину, которая отправляет больше TCP-сегментов, чем обычно. Вы хотите получать оповещения о такой ситуации. TCP-сегменты используются только в качестве примера. Вы можете использовать любое условие оповещения. Когда создается оповещение, вам нужно получить данные на уровне пакетов, чтобы найти причину необычной активности и восстановить на компьютере обычного режима обмена данными.
В этом сценарии предполагается, что у вас есть экземпляр Наблюдателя за сетями, а также группа ресурсов с допустимой виртуальной машиной.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Сценарий

Чтобы автоматизировать этот процесс, мы создадим и подключим на виртуальной машине оповещение, которое будет срабатывать при возникновении проблемы. Также мы создадим функцию Azure для вызова Наблюдателя за сетями.

Вы узнаете:

* как создать функцию Azure, которая запускает запись пакетов;
* как создать правило оповещения на виртуальной машине;
* как настроить правила оповещения для вызова функции Azure.

## <a name="creating-an-azure-function-and-overview"></a>Создание и обзор функции Azure

Первым делом нам нужно создать функцию Azure для обработки оповещения и создания записи пакетов. 

Ниже приведен обзор соответствующего рабочего процесса.

1. На виртуальной машине активируется оповещение.
1. Это оповещение вызывает функцию Azure через webhook.
1. Функция Azure обрабатывает оповещение и запускает сеанс записи пакетов в Наблюдателе за сетями.
1. На виртуальной машине выполняется запись пакетов, которая собирает данные о трафике. 
1. Файл записи пакетов передается в учетную запись хранения для проверки и диагностики. 

Функцию Azure можно создать на портале, выполнив инструкции из статьи [Создание первой функции Azure](../azure-functions/functions-create-first-azure-function.md). В этом примере мы выбрали функцию типа HttpTrigger-CSharp. 

> [!NOTE]
> В функциях Azure реализована поддержка и других языков, но некоторые из них имеют статус экспериментальных и не поддерживаются полностью, как PowerShell и Python.

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Обработка оповещения и запуск сеанса записи пакетов

Пришло время выполнить вызов Наблюдателя за сетями из функции Azure. Реализация этой функции будет отличаться в зависимости от конкретных требований. Но общая схема потока кода будет иметь такой вид.

1. Обработка входных параметров.
2. Запрос уже существующих записей пакетов для проверки ограничений и разрешения конфликтов имен.
3. Создание записи пакетов с необходимыми параметрами.
4. Периодический опрос процесса записи пакетов вплоть до его завершения.
5. Уведомление пользователя о завершении сеанса записи пакета.

Следующий пример на языке C# можно напрямую использовать в функции Azure. Достаточно заменить несколько значений, которые определяют подписку, идентификаторы и секрет клиента.

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

Когда функция будет готова, настройте в оповещении вызова URL-адреса, связанного с этой функцией. Чтобы получить нужное значение, скопируйте URL-адрес функции из приложения функции.

![Определение URL-адреса функции][2]

Если вы хотите передавать пользовательские свойства в полезных данных запроса POST, передаваемого в webhook, изучите статью [Настройка объектов webhook для оповещений на основе метрик Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Настройка оповещения на виртуальной машине

Оповещения можно настроить так, чтобы они уведомляли определенных пользователей о превышении порогового значения для определенных метрик. В нашем примере оповещение создается по числу отправленных сегментов TCP, но можно использовать и другие метрики. Наше оповещение обращается к webhook для вызова функции.

### <a name="create-the-alert-rule"></a>Создание правила для оповещения

Перейдите к существующей виртуальной машине и добавьте правило оповещения. Дополнительные сведения о настройке оповещений см. в статье [Создание оповещений в Azure Monitor для служб Azure с помощью портала Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![Добавление правила оповещения на виртуальной машине][1]

> [!NOTE]
> Некоторые метрики по умолчанию отключены. Дополнительные сведения о том, как включить такие метрики, см. в статье [Включение мониторинга и диагностики](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

И наконец, вставьте URL-адрес из предыдущего шага в текстовое поле webhook для созданного оповещения. Щелкните **OK**, чтобы сохранить правило оповещения.

![Вставка URL-адреса в правило оповещения][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Скачивание и просмотр файла записи

Если вы сохраните запись в учетную запись хранения, файл записи можно будет скачать на портале или программным образом. Если же файл записи хранится локально, для доступа к нему войдите в виртуальную машину. 

Инструкции по скачиванию файлов из учетных записей хранения Azure см. в статье [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md). Кроме того, можно использовать такое средство, как обозреватель хранилищ. Дополнительные сведения об обозревателе хранилищ см. [здесь](http://storageexplorer.com/).

Когда вы скачаете нужный файл записи, его можно просмотреть с помощью любого средства с поддержкой чтения файлов **.cap**. Ниже приведены ссылки на два таких средства.

[Анализатор сообщений (Майкрософт)](https://technet.microsoft.com/en-us/library/jj649776.aspx).  
[Wireshark](https://www.wireshark.org/).  

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как просмотреть запись пакетов, открыв статью [Packet capture analysis with Wireshark](network-watcher-alert-triggered-packet-capture.md) (Анализ записи пакетов с помощью Wireshark).

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

