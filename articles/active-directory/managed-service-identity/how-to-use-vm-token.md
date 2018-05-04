---
title: Использование управляемого удостоверения службы виртуальной машины Azure для получения маркеров доступа
description: Пошаговые инструкции и примеры использования MSI виртуальной машины Azure для получения маркера доступа OAuth.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 541055eeae5e2c0eaff2fb88d8e83fdc43ba08b0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Использование управляемого удостоверения службы (MSI) виртуальной машины Azure для получения маркера 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Эта статья содержит различные примеры кода и скриптов для получения маркера, а также инструкции по обработке ситуаций с просроченным маркером и устранению ошибок HTTP. Рекомендуем использовать управляемое удостоверение службы с конечной точкой IMDS, так как конечная точка расширения виртуальной машины будет объявлена устаревшей.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Если вы планируете использовать примеры Azure PowerShell в этой статье, убедитесь, что установлена последняя версия [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Во всех примерах кода или скриптов в этой статье предполагается, что клиент выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной машине см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](qs-configure-portal-windows-vm.md) или в одном из вариантов статей (с использованием PowerShell, CLI, шаблона или пакета SDK для Azure). 

> [!IMPORTANT]
> - Периметр безопасности для управляемого удостоверения — это ресурс. Все коды и скрипты, выполняемые на виртуальной машине с поддержкой MSI, могут запрашивать и получать маркеры. 

## <a name="overview"></a>Обзор

Клиентское приложение может запрашивать [маркер доступа к приложению](../develop/active-directory-dev-glossary.md#access-token) MSI для доступа к данному ресурсу. Маркер создан на основе [субъекта-службы MSI](overview.md#how-does-it-work). Таким образом клиенту не нужно регистрироваться для получения маркера доступа для собственного субъекта-службы. Маркер подходит для использования в качестве маркера носителя при [выполнении вызовов между службами, требующих учетных данных клиента](../develop/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Получение маркера с использованием HTTP](#get-a-token-using-http) | Сведения о протоколе для конечной точки маркера MSI |
| [Получение маркера с использованием C#](#get-a-token-using-c) | Пример использования конечной точки MSI REST от клиента C# |
| [Получение маркера с использованием Go](#get-a-token-using-go) | Пример использования конечной точки MSI REST от клиента Go |
| [Получение маркера с использованием Azure PowerShell](#get-a-token-using-azure-powershell) | Пример использования конечной точки MSI REST от клиента Azure PowerShell |
| [Получение маркера с использованием CURL](#get-a-token-using-curl) | Пример использования конечной точки MSI REST от клиента Bash/CURL |
| [Обработка срока действия маркера](#handling-token-expiration) | Рекомендации по обработке срока действия маркера доступа |
| [Обработка ошибок](#error-handling) | Рекомендации по обработке ошибок HTTP, возвращаемых из конечной точки маркера MSI |
| [Идентификаторы ресурсов для служб Azure](#resource-ids-for-azure-services) | Сведения о том, где можно получить идентификаторы ресурсов для поддерживаемых служб Azure |

## <a name="get-a-token-using-http"></a>Получение маркера с использованием HTTP 

Основной интерфейс для получения маркера доступа создан на основе REST, что делает его доступным для любого клиентского приложения, выполняющегося на виртуальной машине,которая может выполнять вызовы HTTP REST. Это похоже на модель программирования Azure AD, за исключением того, что клиент использует конечную точку на виртуальной машине (а не конечную точку Azure AD).

Пример запроса с использованием конечной точки службы метаданных экземпляров (IMDS) MSI *(рекомендуется)*:

```
GET http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1 Metadata: true
```

| Элемент | ОПИСАНИЕ |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Конечная точка MSI для службы метаданных экземпляров. |
| `api-version`  | Параметр строки запроса, указывающий версию API для конечной точки IMDS. Используйте версию API `2018-02-01` или выше. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Он также отображается в утверждении (аудитории) `aud` выданного маркера. В этом примере запрашивается маркер для доступа к Azure Resource Manager, который имеет универсальный код ресурса (URI) идентификатора приложения https://management.azure.com/. |
| `Metadata` | Поле заголовка HTTP-запроса, требуемое MSI с целью устранения рисков от атак с подделкой запроса со стороны сервера (SSRF). Должно быть присвоено значение true (все в нижнем регистре).

Пример запроса с использованием конечной точки расширения MSI для виртуальной машины (*эта конечная точка будет объявлена устаревшей*):

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Элемент | ОПИСАНИЕ |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://localhost:50342/oauth2/token` | Конечная точка MSI, в которой порт 50342 является портом по умолчанию, который можно настроить. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Он также отображается в утверждении (аудитории) `aud` выданного маркера. В этом примере запрашивается маркер для доступа к Azure Resource Manager, который имеет универсальный код ресурса (URI) идентификатора приложения https://management.azure.com/. |
| `Metadata` | Поле заголовка HTTP-запроса, требуемое MSI с целью устранения рисков от атак с подделкой запроса со стороны сервера (SSRF). Должно быть присвоено значение true (все в нижнем регистре).


Пример ответа:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Элемент | ОПИСАНИЕ |
| ------- | ----------- |
| `access_token` | Запрашиваемый маркер доступа. При вызове защищенного REST API маркер внедряется в поле `Authorization` заголовка запроса в качестве маркера носителя, позволяя API выполнить проверку подлинности вызывающего объекта. | 
| `refresh_token` | Не используется MSI. |
| `expires_in` | Количество секунд, в течение которых маркер доступа является действительным, прежде чем его срок действия истечет (с момента выдачи). Время выдачи можно найти в утверждении `iat` маркера. |
| `expires_on` | Период времени после истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `exp` маркера). |
| `not_before` | Период времени, когда маркер доступа вступает в силу и может быть принят. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `nbf` маркера). |
| `resource` | Ресурс, для которого был запрошен маркер доступа, соответствует параметру строки запроса `resource`. |
| `token_type` | Тип маркера, который является маркером доступа носителя, что значит, что ресурс может предоставлять доступ носителю этого маркера. |

## <a name="get-a-token-using-c"></a>Получение маркера с использованием C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Получение маркера с использованием Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Получение маркера с использованием Azure PowerShell

В следующем примере демонстрируется использование конечной точки MSI REST от клиента PowerShell для выполнения таких задач:

1. Получение маркера доступа.
2. Использование маркера доступа для вызова REST API Azure Resource Manager и получения информации о виртуальной машине. Не забудьте указать свой идентификатор подписки, имя группы ресурсов и имя виртуальной машины для `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>` и `<VM-NAME>` соответственно.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Пример анализа маркера доступа, полученного из ответа:
```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Получение маркера с использованием CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Пример анализа маркера доступа, полученного из ответа:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="token-expiration"></a>Срок действия маркера 

Если маркер кэшируется в коде, необходимо подготовиться к обработке сценариев, в которых ресурс указывает, что срок действия маркера истек. 

Примечание. Так как подсистема IMDS MSI кэширует маркеры, сетевые вызовы Azure AD выполняется, только если:
- произошел промах кэша из-за отсутствия маркера в кэше;
- истек срок действия маркера.

## <a name="error-handling"></a>Обработка ошибок

Конечная точка MSI сообщает об ошибках в поле кода состояния заголовка ответного сообщения HTTP в виде ошибок 4xx или 5xx:

| Код состояния | Причина ошибки | Способ устранения |
| ----------- | ------------ | ------------- |
| 429 — слишком много запросов |  Достигнут предел регулирования IMDS. | Повторите попытку с экспоненциальным увеличением задержки. См. инструкции ниже. |
| Ошибка 4xx в запросе. | Один или несколько параметров запроса неверные. | Не выполняйте повторную попытку.  Для получения дополнительной информации просмотрите сведения об ошибке.  Ошибки 4xx — это ошибки времени разработки.|
| Временная ошибка 5xx службы. | Подсистема MSI или Azure Active Directory вернули временную ошибку. | По прошествии 1 секунды можно безопасно повторить попытку.  Если повторные попытки будут выполняться слишком быстро или слишком часто, IMDS и (или) Azure AD могут вернуть ошибку ограничения частоты (429).|

Если возникает ошибка, в соответствующем тексте ответа HTTP содержится код JSON с подробностями об ошибке:

| Элемент | ОПИСАНИЕ |
| ------- | ----------- |
| error   | Идентификатор ошибки. |
| error_description | Подробное описание ошибки. **Описания ошибки могут в любое время измениться. Не записывайте код, который создает ветвь на основе значений в описании ошибки.**|

### <a name="http-response-reference"></a>Ссылка ответа HTTP

В этом разделе описываются возможные сообщения об ошибках. Состояние "200 ОК" — это успешный ответ, а маркер доступа содержится в коде JSON текста ответа в элементе access_token.

| Код состояния | Ошибка | Описание ошибки | Решение |
| ----------- | ----- | ----------------- | -------- |
| 400 — недопустимый запрос | invalid_resource | AADSTS50001: приложение с именем *\<URI\>* не найдено в клиенте с именем *\<TENANT-ID\>*. Это может произойти, если приложение установил не администратор клиента или если пользователь в клиенте не предоставил к нему разрешение. Возможно, вы отправили запрос на проверку подлинности не тому клиенту. | (только для Linux) |
| 400 — недопустимый запрос | bad_request_102 | Необходимый заголовок метаданных не указан | Поле заголовка запроса `Metadata` отсутствует или имеет неправильный формат. Должно быть указано значение `true` в нижнем регистре. В качестве примера см. "Пример запроса" в [предыдущем разделе для REST](#rest).|
| 401 — недостаточно прав | unknown_source | Неизвестный *\<URI\>* источника | Убедитесь, что универсальный код ресурса (URI) запроса HTTP GET имеет правильный формат. Часть `scheme:host/resource-path` должна быть указана как `http://localhost:50342/oauth2/token`. В качестве примера см. "Пример запроса" в [предыдущем разделе для REST](#rest).|
|           | invalid_request | В запросе отсутствует требуемый параметр, он включает недопустимое значение параметра, параметр указан несколько раз или как-то искажен. |  |
|           | unauthorized_client | Клиент не авторизован для запроса маркера доступа с помощью этого метода. | Ошибка возникла из-за запроса, который не использовал локальное замыкание на себя для вызова расширения, или из-за того, что на виртуальной машине неправильно настроен MSI. Если вам нужна помощь при конфигурации виртуальной машины, см. статью [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](qs-configure-portal-windows-vm.md). |
|           | access_denied | Владелец ресурса или сервер авторизации отклонил запрос. |  |
|           | unsupported_response_type | Сервер авторизации не поддерживает получение маркера доступа с помощью этого метода. |  |
|           | invalid_scope | Запрошенная область недопустима, неизвестна или неверна. |  |
| 500 — внутренняя ошибка сервера | неизвестно | Не удалось извлечь маркер из Active Directory. Дополнительные сведения см. в журналах в *\<путь к файлу\>*. | Убедитесь, что MSI включен на виртуальной машине. Если вам нужна помощь при конфигурации виртуальной машины, см. статью [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](qs-configure-portal-windows-vm.md).<br><br>Кроме того, убедитесь, что универсальный код ресурса (URI) запроса HTTP GET отформатирован правильно, в частности универсальный код ресурса (URI), указанный в строке запроса. В качестве примера см. "Пример запроса" в [предыдущем разделе для REST](#rest) или раздел [Службы Azure, поддерживающие аутентификацию Azure AD](overview.md#azure-services-that-support-azure-ad-authentication) для получения списка служб и соответствующих идентификаторов ресурсов.

## <a name="throttling-guidance"></a>Руководство по регулированию 

Ограничения регулирования применяются к числу вызовов к конечной точке IMDS MSI. Если превышен порог регулирования, конечная точка IMDS MSI ограничивает каждый последующий запрос, пока действует регулирование. В течение этого периода конечная точка IMDS MSI возвращает код состояния HTTP 429 ("Too many requests" (Слишком много запросов)), и запросы не выполняются. 

Для повторных попыток рекомендуем следующую стратегию: 

| **Стратегия повторов** | **Параметры** | **Значения** | **Принцип работы** |
| --- | --- | --- | --- |
|ExponentialBackoff |Число повторных попыток<br />Минимальная задержка<br />Максимальная задержка<br />Разностная задержка<br />Первый быстрый повтор |5<br />0 с<br />60 с<br />2 с<br />false |Попытка 1 — задержка 0 с<br />Попытка 2 — задержка 2 с<br />Попытка 3 — задержка 6 с<br />Попытка 4 — задержка 14 с<br />Попытка 5 — задержка 30 с |

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure

Список ресурсов, которые поддерживают Azure AD и были протестированы с MSI и соответствующими идентификаторами ресурсов, см. в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](overview.md#azure-services-that-support-azure-ad-authentication).


## <a name="related-content"></a>Связанная информация

- О том, как включить MSI для виртуальной машины Azure, можно узнать в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](qs-configure-portal-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








