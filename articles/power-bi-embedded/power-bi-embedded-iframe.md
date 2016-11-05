---
title: Использование Power BI Embedded с REST | Microsoft Docs
description: 'Узнайте, как использовать Power BI Embedded с REST. '
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="how-to-use-power-bi-embedded-with-rest"></a>Использование Power BI Embedded с REST
## <a name="power-bi-embedded:-what-it-is-and-what-it's-for"></a>Power BI Embedded: что это такое и как это использовать
Обзор службы Power BI Embedded приводится на официальном [сайте Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/), но давайте кратко рассмотрим общие сведения, прежде чем углубляться в подробности ее использования с REST.

На самом деле все довольно просто. Независимые поставщики программного обеспечения часто стремятся использовать визуализации динамических данных [Power BI](https://powerbi.microsoft.com) в собственных приложениях в качестве стандартных блоков пользовательского интерфейса.

Но теперь отчеты или элементы Power BI можно внедрять в веб-страницу без использования службы Azure Power BI Embedded. Для этого существует интерфейс **API Power BI**. Когда вам требуется открыть совместный доступ к отчетам в пределах своей организации, вы можете внедрить отчеты с аутентификацией Azure AD. Для просмотра отчетов пользователь должен будет войти в систему, используя свою учетную запись Azure AD. Когда вам требуется открыть совместный доступ к отчетам для всех пользователей (включая внешних), вы можете просто выполнить внедрение с анонимным доступом.

Но, как вы видите, такое простое внедрение не полностью соответствует потребностям приложений независимых поставщиков программного обеспечения.
В большинстве приложений независимых поставщиков программного обеспечения требуется предоставлять данные для клиентов, которые могут не являться пользователями одной организации. Например, если вы обслуживаете компанию A и компанию Б, то пользователи компании А должны видеть только данные для своей компании. То есть, для такого обслуживания необходима мультитенантность.

Приложения независимых поставщиков программного обеспечения также могут предлагать свои собственные способы проверки подлинности, такие как проверка подлинности на основе форм, обычная проверка подлинности и другие... Учитывайте, что реализация внедрения должна безопасно взаимодействовать с существующими способами проверки подлинности. Также необходимо, чтобы пользователи могли использовать приложения независимых поставщиков программного обеспечения без дополнительного приобретения или лицензирования подписки Power BI.

 **Power BI Embedded** разработана именно для такого рода сценариев. Теперь, когда у нас есть общее представление, давайте рассмотрим некоторые возможности подробнее.

Можно воспользоваться пакетом SDK для .NET \(C#) или Node.js, чтобы быстро интегрировать в свое приложение службу Power BI Embedded. Но в этой статье мы рассмотрим, как создать поток HTTP \(включая AuthN) Power BI без использования пакетов SDK. Изучив особенности потока, вы сможете создавать приложения **на любом языке программирования**, а также поймете принцип работы Power BI Embedded.

## <a name="create-power-bi-workspace-collection,-and-get-access-key-\(provisioning)"></a>Создание коллекции рабочих областей Power BI и получение ключа доступа \(подготовка)
Power BI Embedded — это одна из служб Azure. Плата за использование взимается только с независимых поставщиков программного обеспечения, которые используют портал Azure \(за каждый час сеанса пользователя). А пользователи, просматривающие отчеты, ничего не оплачивают, и им даже не обязательно иметь подписку Azure.
Прежде чем приступать к разработке приложения, необходимо создать **коллекцию рабочих областей Power BI** с помощью портала Azure.

Каждая рабочая область Power BI Embedded является рабочей областью для одного пользователя (клиента), и в каждую коллекцию можно добавить множество рабочих областей. В каждой коллекции рабочих областей используется один ключ доступа. Коллекция рабочих областей фактически является периметром безопасности для Power BI Embedded.

![](media\\power-bi-embedded-iframe\\create-workspace.png)

По завершении создания коллекции рабочих областей скопируйте ключ доступа из портала Azure.

![](media\\power-bi-embedded-iframe\\copy-access-key.png)

> [!NOTE]
> Также можно подготовить коллекцию рабочих областей и получить ключ доступа через REST API. Дополнительные сведения см. в статье [Power BI Resource Provider APIs](https://msdn.microsoft.com/library/azure/mt712306.aspx) (Интерфейсы Power BI API для поставщика ресурсов).
> 
> 

## <a name="create-.pbix-file-with-power-bi-desktop"></a>Создание PBIX-файла с помощью Power BI Desktop
Теперь нам необходимо создать подключение к данным и отчеты, которые будут внедряться.
Эта задача не выполняется программно или с помощью кода. Мы просто воспользуемся Power BI Desktop.
В этой статье мы не будем подробно описывать, как пользоваться Power BI Desktop. Дополнительные сведения по этому вопросу см. в руководстве [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). В нашем примере мы воспользуемся [образцом "Анализ розничной торговли"](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\\power-bi-embedded-iframe\\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Создание рабочей области Power BI
Теперь, когда подготовка выполнена, давайте приступим к созданию рабочей области пользователя в коллекции рабочих областей, используя интерфейсы REST API. Следующий HTTP-запрос POST (REST) создает новую рабочую область в существующей коллекции рабочих областей. В нашем примере имя коллекции рабочих областей — **mypbiapp**.
Нам нужно просто задать ключ доступа, скопированный ранее, как **AppKey**. Это очень простая аутентификация!

**HTTP-запрос**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**HTTP-ответ**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Возвращаемый параметр **workspaceId** используется для последующих вызовов API. Это значение должно сохраниться в нашем приложении.

## <a name="import-.pbix-file-into-the-workspace"></a>Импорт PBIX-файла в рабочую область
В каждой рабочей области может размещаться один файл Power BI Desktop с набором данных \(включая параметры источника данных) и отчетами. PBIX-файл можно импортировать в рабочую область, как показано в приведенном ниже коде. Как вы видите, мы можем передать двоичную версию PBIX-файла, используя составное сообщение MIME в HTTP.

Фрагмент URI **32960a09-6366-4208-a8bb-9e0678cdbb9d** — это идентификатор рабочей области, а параметр запроса **datasetDisplayName** — это имя создаваемого набора данных. Созданный набор содержит все артефакты PBIX-файла, связанные с данными, такие как импортированные данные, указатель на источник данных и т. д.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Выполнение этой задачи импорта может занять некоторое время. По завершении наше приложение может запросить состояние задачи с помощью идентификатора импорта. В нашем примере идентификатор импорта — **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Следующим образом запрашивается состояние с помощью этого идентификатора импорта:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Если выполнение задачи не завершено, то HTTP-ответ может быть следующим:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Если выполнение задачи завершено, то HTTP-ответ скорее всего будет следующим:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-\(and-multi-tenancy-of-data)"></a>Подключение к источнику данных \(и мультитенантность данных)
Почти все артефакты PBIX-файла импортируются в рабочую область, но учетные данные для источников данных не импортируются. В результате при использовании **режима DirectQuery** внедренный отчет не может отображаться правильно. Но в **режиме импорта** можно просмотреть отчет, используя существующие импортированные данные. В таком случае нам необходимо задать учетные данные. Для этого мы воспользуемся вызовами REST и выполним следующие действия.

Во-первых, мы должны получить источник данных шлюза. Мы знаем, что **id** для набора данных — это ранее возвращенный идентификатор.

**HTTP-запрос**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**HTTP-ответ**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Используя полученный идентификатор шлюза и идентификатор источника данных \(см. **gatewayId** и **id** в ранее возвращенном результате) мы можем изменить учетные данные для этого источника данных, как показано ниже.

**HTTP-запрос**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**HTTP-ответ**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

В рабочей среде с помощью REST API можно задать отдельную строку подключения для каждой рабочей области. \(т. е. мы можем использовать разные базы данных для каждого клиента.)

В следующем примере с помощью REST изменяется строка подключения источника данных.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Также можно использовать безопасность на уровне строк в Power BI Embedded, чтобы разделить данные для каждого пользователя в одном отчете. В результате мы можем подготовить отчет для каждого клиента, используя один PBIX-файл \(который описывает пользовательский интерфейс и т. д.) и разные источники данных.

> [!NOTE]
> Если вы используете **режим импорта**, а не **режим DirectQuery**, у вас не будет возможности обновить модели через API. Локальные источники данных через шлюз Power BI пока поддерживаются в Power BI Embedded. Тем не менее, рекомендуем следить за [блогом Power BI](https://powerbi.microsoft.com/blog/) и узнавать о новых возможностях и обновлениях, ожидаемых в будущих выпусках.
> 
> 

## <a name="authentication-and-hosting-(embedding)-reports-in-our-web-page"></a>Аутентификация и размещение (внедрение) отчетов на веб-странице
В предыдущих версиях REST API можно использовать ключ доступа **AppKey** в качестве заголовка авторизации. Так как эти вызовы могут обрабатываться на стороне внутреннего сервера, это безопасно.

Но если внедрить отчет в веб-страницу, подобные сведения о безопасности будут обрабатываться с помощью JavaScript \(внешнего интерфейса). В этом случае требуется защитить значение заголовка авторизации. Если наш ключ доступа будет обнаружен пользователем-злоумышленником или вредоносным кодом, то злоумышленник сможет вызывать любые операции с помощью этого ключа.

При внедрении отчета в веб-страницу вместо ключа доступа **AppKey**необходимо использовать вычисляемый маркер. Наше приложение должно создать маркер OAuth Json Web Token \(JWT), состоящий из утверждений и вычисляемой цифровой подписи. Как показано ниже, OAuth JWT — это маркер закодированной строки с разделителями-точками.

![](media\\power-bi-embedded-iframe\\oauth-jwt.png)

Сначала нам необходимо подготовить входное значение, которое подписывается позже. Это значение представляет собой строки из следующего кода JSON в кодировке Base64 (rfc4648), разделенные точкой \(.). Позднее мы объясним, как получить идентификатор отчета.

> [!NOTE]
> Если мы хотим использовать безопасность на уровне строк (RLS) со службой Power BI Embedded, нам необходимо также указать в утверждениях **имя пользователя** и **роли**.
> 
> 

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Затем мы должны создать строку HMAC \(подпись) в кодировке Base64 с использованием алгоритма SHA256. Это подписанное входное значение является предыдущей строкой.

Наконец, нам необходимо объединить входное значение и строку подписи с помощью точки \(.). Готовая строка является маркером приложения для внедрения отчета. Даже если маркер приложения будет обнаружен пользователем-злоумышленником, ему не удастся получить исходный ключ доступа. Срок действия этого маркера приложения быстро закончится.

Ниже приведен пример PHP для описанных шагов.

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally,-embed-the-report-into-the-web-page"></a>Последний шаг: внедрение отчета в веб-страницу
Для внедрения отчета нам необходимо получить URL-адрес внедрения и **идентификатор** отчета с помощью следующего REST API.

**HTTP-запрос**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**HTTP-ответ**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Можно внедрить отчет в веб-приложение с помощью предыдущего маркера приложения.
Если взглянуть на следующий пример кода, то мы увидим, что его начальная часть такая же, как в предыдущем примере. В последующей части этого примера в iframe отображается **embedUrl** \(см. предыдущий результат), а также в iframe публикуется маркер приложения.

> [!NOTE]
> Значение идентификатора отчета необходимо заменить собственным. Кроме того, из-за ошибки в нашей системе управления содержимым тег iframe в примере кода считывается буквально. Если вы будете копировать и вставлять этот пример кода, удалите из тега прописной текст.
> 
> 

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

А вот и наш результат:

![](media\\power-bi-embedded-iframe\\view-report.png)

В настоящее время Power BI Embedded отображает отчет только в iframe. Но рекомендуем следить за [блогом Power BI](). Улучшения в будущих обновлениях смогут использовать новые клиентские интерфейсы API, которые позволят нам передавать данные в iframe и получать их оттуда. Восхитительные функции!

## <a name="see-also"></a>Дополнительные материалы
* [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!--HONumber=Oct16_HO2-->


