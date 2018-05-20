---
title: API сборщика данных HTTP в Log Analytics | Документация Майкрософт
description: API сборщика данных HTTP в Log Analytics можно использовать для добавления данных POST JSON в репозиторий Log Analytics из любого клиента, который может вызывать REST API. В этой статье описывается, как использовать API, и приводятся примеры публикации данных с использованием разных языков программирования.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: d42069e8ed72a834973b56df55488955d62e71f2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Отправка данных в Log Analytics c помощью API сборщика данных HTTP (общедоступная предварительная версия)
В этой статье показано, как с помощью API сборщика данных HTTP отправить данные в Log Analytics из клиента REST API.  Здесь также описано, как отформатировать данные, собранные сценарием или приложением, добавить их в запрос и авторизовать этот запрос в Log Analytics.  В этой статье приведены примеры для PowerShell, C# и Python.

> [!NOTE]
> API сборщика данных HTTP службы Log Analytics находится на этапе предварительной версии.

## <a name="concepts"></a>Основные понятия
API сборщика данных HTTP можно использовать, чтобы отправить данные в Log Analytics из любого клиента, который может вызвать REST API.  Это может быть модуль runbook в службе автоматизации Azure, который собирает данные по управлению из Azure или другого облака, или любая другая система управления, использующая Log Analytics для консолидации и анализа данных.

Все данные в репозитории Log Analytics хранятся как запись определенного типа.  Чтобы отправить данные в API сборщика данных HTTP, их необходимо отформатировать как несколько записей в формате JSON.  При отправке данных в репозитории для каждой записи в запрошенных полезных данных создается отдельная запись.


![Обзор сборщика данных HTTP](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Создание запроса
Чтобы использовать API сборщика данных HTTP, необходимо создать запрос POST, содержащий данные для отправки в нотацию объектов JavaScript (JSON).  В следующих трех таблицах перечислены обязательные атрибуты для каждого запроса. Более подробное описание каждого из атрибутов приводится далее в этой статье.

### <a name="request-uri"></a>URI запроса
| Атрибут | Свойство |
|:--- |:--- |
| Метод |ПУБЛИКАЦИЯ |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Тип содержимого |приложение/json |

### <a name="request-uri-parameters"></a>Параметры URI запроса
| Параметр | ОПИСАНИЕ |
|:--- |:--- |
| CustomerID |Уникальный идентификатор для рабочей области Log Analytics. |
| Ресурс |Имя ресурса API: /api/logs. |
| Версия API |Версия API для использования с этим запросом. В настоящее время это версия 2016-04-01. |

### <a name="request-headers"></a>Заголовки запросов
| Заголовок | ОПИСАНИЕ |
|:--- |:--- |
| Авторизация |Подпись авторизации. Далее в этой статье вы найдете сведения о том, как создать заголовок HMAC-SHA256. |
| Log-Type |Укажите тип записи для отправляемых данных. Сейчас для указания типа журнала можно использовать только буквы. Цифры и специальные символы не поддерживаются. Предельный размер этого параметра составляет 100 символов. |
| x-ms-date |Дата обработки запроса в формате RFC 1123. |
| time-generated-field |Имя поля данных, содержащее метку времени элемента данных. Если вы укажете здесь поле, его содержимое будет использоваться как значение параметра **TimeGenerated**. Если это поле не указано, по умолчанию для **TimeGenerated** будет использоваться время приема сообщения. Содержимое поля сообщения должно соответствовать формату ISO 8601: YYYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Авторизация
Любой запрос к API сборщика данных HTTP в Log Analytics должен включать заголовок авторизации. Чтобы проверить подлинность запроса, необходимо подписать запрос с помощью первичного или вторичного ключа для рабочей области, выполняющей запрос. Затем следует передать подпись как часть запроса.   

Вот формат заголовка авторизации:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* — это уникальный идентификатор для рабочей области Log Analytics. *Signature* — это [код проверки подлинности сообщения на основе хэша (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx), созданный из запроса и вычисленный с помощью [алгоритма SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Затем его можно закодировать с помощью кодировки Base64.

Используйте этот формат для кодирования строки подписи **SharedKey**:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Вот пример строки подписи:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

При наличии строки подписи закодируйте ее с помощью алгоритма HMAC-SHA256 в строке в кодировке UTF-8, а затем закодируйте результат в Base64. Используйте следующий формат:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Примеры в следующих разделах содержат образец кода, с помощью которого вы сможете создать заголовок авторизации.

## <a name="request-body"></a>Тело запроса
Текст сообщения должен иметь формат JSON. Он должен содержать одну или несколько записей с парами имени и значения свойств в следующем формате:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Вы можете сгруппировать в одном запросе несколько записей, используя следующий формат. Все записи должны принадлежать к одному типу.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Тип и свойства записи
При отправке данных через API сборщика данных HTTP в Log Analytics определяется тип пользовательской записи. В настоящее время нельзя записывать данные в записи существующих типов, созданные с помощью других типов данных и решений. Log Analytics считывает входящие данные, а затем создает свойства, которые соответствуют типам данных вводимых значений.

Каждый запрос к API Log Analytics должен содержать заголовок **Log-Type** с именем типа записей. Суффикс **_CL** автоматически добавляется к вводимому имени, чтобы пользовательский журнал отличался от журналов других типов. Например, если ввести имя **MyNewRecordType**, Log Analytics создаст запись с типом **MyNewRecordType_CL**. Это помогает избежать конфликтов между именами типов, создаваемыми пользователями, и готовыми именами существующих или будущих решений Microsoft.

Чтобы определить тип данных свойства, Log Analytics добавляет суффикс к имени свойства. Если свойство содержит значение NULL, свойство не включается в эту запись. В таблице ниже перечислены типы данных свойства и соответствующие суффиксы.

| Тип данных свойства | Суффикс |
|:--- |:--- |
| Строка |_s |
| Логическое |_b |
| Double |_d |
| Дата и время |_t |
| GUID |_g |

Тип данных, который Log Analytics использует для каждого свойства, зависит от того, существует ли тип записи для новой записи.

* Если тип записи не существует, Log Analytics создает новый тип. Log Analytics использует определение типа JSON для указания типа данных для каждого свойства новой записи.
* Если тип записи не существует, Log Analytics пытается создать новую запись на основе существующих свойств. Если тип данных для свойства в новой записи не соответствует существующему типу и не может быть преобразован в него или если запись включает свойство, которое не существует, Log Analytics создает новое свойство с соответствующим суффиксом.

Например, при отправке следующих данных создается запись с тремя свойствами: **number_d**, **boolean_b** и **string_s**:

![Пример записи 1](media/log-analytics-data-collector-api/record-01.png)

Если отправить следующую запись со всеми значениями в строковом формате, свойства не изменятся. Эти значения можно преобразовать в существующие типы данных:

![Пример записи 2](media/log-analytics-data-collector-api/record-02.png)

Но если отправить следующую запись, Log Analytics создаст новые свойства **boolean_d** и **string_d**. Преобразовать эти значения нельзя:

![Пример записи 3](media/log-analytics-data-collector-api/record-03.png)

Если затем будет отправлена следующая запись, прежде чем будет создан тип записи, Log Analytics создаст запись с тремя свойствами: **number_s**, **boolean_s** и **string_s**. В этой записи каждое начальное значение форматируется как строка:

![Пример записи 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Ограничения данных
Существуют ограничения на данные, публикуемые в API сбора данных Log Analytics.

* Не более 30 МБ на публикацию для API сбора данных Log Analytics. Это ограничение размера для одной публикации. Если данные одной публикации превышают 30 МБ, необходимо разделить данные на меньшие фрагменты и отправить их параллельно.
* Не более 32 КБ для значений полей. Если значение поля превышает 32 КБ, данные будут усечены.
* Рекомендуемое максимальное количество полей для данного типа — 50. Это ограничение введено для удобства поиска и использования.  

## <a name="return-codes"></a>Коды возврата
Код состояния HTTP 200 означает, что запрос получен для обработки. Такой результат означает, что операция завершена успешно.

В этой таблице представлен полный набор кодов состояний, которые может возвращать служба:

| Код | Status | Код ошибки | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| 200 |ОК | |Запрос был успешно принят. |
| 400 |Недопустимый запрос |InactiveCustomer |Рабочая область закрыта. |
| 400 |Недопустимый запрос |InvalidApiVersion |Указанная версия API не распознана службой. |
| 400 |Недопустимый запрос |InvalidCustomerId |Указан недопустимый идентификатор рабочей области. |
| 400 |Недопустимый запрос |InvalidDataFormat |Отправлены недопустимые данные JSON. Текст ответа может содержать дополнительные сведения о том, как устранить ошибку. |
| 400 |Недопустимый запрос |InvalidLogType |Указанный тип журнала содержит специальные символы или цифры. |
| 400 |Недопустимый запрос |MissingApiVersion |Версия API не указана. |
| 400 |Недопустимый запрос |MissingContentType |Тип содержимого не указан. |
| 400 |Недопустимый запрос |MissingLogType |Обязательное значение типа журнала не указано. |
| 400 |Недопустимый запрос |UnsupportedContentType |Для типа содержимого не было задано значение **application/json**. |
| 403 |Запрещено |InvalidAuthorization |Службе не удалось проверить подлинность запроса. Проверьте правильность идентификатора и ключа подключения рабочей области. |
| 404 |Не найдено | | Указан неправильный URL-адрес либо запрос слишком большой. |
| 429 |Слишком много запросов | | В службу поступает слишком большой объем данных из вашей учетной записи. Повторите запрос позже. |
| 500 |Внутренняя ошибка сервера |UnspecifiedError |Служба обнаружила внутреннюю ошибку. Повторите запрос. |
| 503 |Служба недоступна |ServiceUnavailable |Служба сейчас недоступна для получения запросов. Повторите запрос. |

## <a name="query-data"></a>Запрос данных
Для запроса данных, отправленных посредством API сборщика данных HTTP в Log Analytics, найдите записи с **типом**, равным указанному вами значению **LogType**, к которому добавлен суффикс **_CL**. Например, если вы использовали значение **MyCustomLog**, будут возвращены все записи, содержащие текст **Type=MyCustomLog_CL**.

>[!NOTE]
> Если ваша рабочая область переведена на [язык запросов Log Analytics](log-analytics-log-search-upgrade.md), приведенный выше запрос будет изменен следующим образом.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Примеры запросов
В следующих разделах вы найдете примеры отправки данных в API сборщика данных HTTP в Log Analytics с использованием разных языков программирования.

Для каждого примера выполните следующие действия, чтобы задать переменные в заголовке авторизации:

1. На портале Azure перейдите в свою рабочую область Log Analytics.
2. Выберите **Расширенные параметры**, а затем — **Подключенные источники**.
2. Справа от **идентификатора рабочей области** щелкните значок копирования и вставьте идентификатор как значение переменной **CustomerID**.
3. Справа от **первичного ключа** щелкните значок копирования и вставьте идентификатор как значение переменной **SharedKey**.

Также можно изменить переменные для типа журнала и данных JSON.

### <a name="powershell-sample"></a>Пример для PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Пример на языке C#
```
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Пример на языке Python
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Дополнительная информация
- Чтобы получить данные из репозитория Log Analytics, используйте [API поиска по журналам](log-analytics-log-search-api.md).
