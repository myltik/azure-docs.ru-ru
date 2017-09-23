---
title: "Внедрение отчета в Azure Power BI Embedded | Документация Майкрософт"
description: "Узнайте, как внедрить отчет Azure Power BI Embedded в приложение."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3d865af2418c9c557c861a379766a125d75cebf1
ms.contentlocale: ru-ru
ms.lasthandoff: 03/14/2017

---
# <a name="embed-a-report-in-power-bi-embedded"></a>Внедрение отчета в Power BI Embedded

Узнайте, как внедрить отчет Azure Power BI Embedded в приложение.

В этой статье мы рассмотрим, как внедрить в приложение отчет. Предполагается, что у вас уже есть отчет, который находится в рабочей области в коллекции рабочих областей. Если вы еще не выполнили этот шаг, то см. статью [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

Можно воспользоваться пакетом SDK для .NET (C#) или Node.js, параллельно с JavaScript, чтобы быстро интегрировать в свое приложение службу Power BI Embedded. 

## <a name="using-the-access-keys-to-use-rest-apis"></a>Использование ключей доступа для интерфейсов REST API

Чтобы вызвать REST API, можно передать ключ доступа, который получается на портале Azure для заданной коллекции рабочих областей. Дополнительные сведения см. в статье [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

## <a name="get-a-report-id"></a>Получение идентификатора отчета

Каждый маркер доступа основан на отчете. Необходимо получить данный идентификатор для отчета, который требуется внедрить. Это можно сделать на основе вызовов к интерфейсу REST API [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) (Получить отчеты). В результате будут возвращены идентификатор отчета и URL-адрес внедрения. Это можно сделать с помощью пакета SDK Power BI для .NET или прямого вызова REST API.

### <a name="using-the-power-bi-net-sdk"></a>Использование пакета SDK Power BI для .NET

При использовании пакета SDK для .NET необходимо создать учетные данные маркера, основанные на ключе доступа, который можно получить на портале Azure. Для этого необходимо установить [пакет NuGet для API Power BI](https://www.nuget.org/profiles/powerbi).

**Установка пакета NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Код C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Прямой вызов REST API

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Создание маркера доступа

Power BI Embedded использует маркеры внедрения, которые являются подписанными веб-маркерами JSON (JSON Web Token) с поддержкой HMAC. Маркеры подписываются с помощью ключа доступа из коллекции рабочих областей Azure Power BI Embedded. По умолчанию маркеры внедрения используются для предоставления доступа к отчету с правами только для чтения, чтобы внедрить его в приложение. Маркеры внедрения выдаются для конкретного отчета и должны быть связаны с URL-адресом внедрения.

Маркеры доступа должны создаваться на сервере, так как ключи доступа используются для подписания или шифрования маркеров. Дополнительные сведения о создании маркера доступа см. в статье [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md). Вы также можете ознакомиться с методом [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Здесь приведен пример с использованием пакета SDK для Power BI (.NET).

Будет использоваться ранее полученный идентификатор отчета. После создания маркера внедрения воспользуйтесь ключом доступа для создания маркера, который вы сможете использовать в javascript. Для *класса PowerBIToken* необходимо установить [пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Установка пакета NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Код C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Добавление областей разрешений в маркеры внедрения

При использовании маркеров внедрения может потребоваться ограничить использование ресурсов, к которым предоставляется доступ. Для этого можно создать маркер с заданной областью разрешений. Дополнительные сведения см. в разделе [Области](power-bi-embedded-app-token-flow.md#scopes).

## <a name="embed-using-javascript"></a>Внедрение с помощью JavaScript

Когда у вас уже есть маркер доступа и идентификатор отчета, можно внедрить отчет с помощью JavaScript. Для этого необходимо установить [пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Параметр embedUrl будет иметь значение https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Чтобы протестировать функциональные возможности, можно использовать [пример внедрения отчета JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/). В нем также приводятся примеры кода для различных операций, которые доступны.

**Установка пакета NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Код JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Выбор размера внедряемых элементов

Отчет будет автоматически внедрен, исходя из размера его контейнера. Чтобы переопределить заданный по умолчанию размер внедряемых элементов, просто добавьте атрибут класса CSS или встроенные стили для ширины и высоты.

## <a name="see-also"></a>См. также

[Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)  
[Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Пакет NuGet для API Power BI](https://www.nuget.org/profiles/powerbi)
[Пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Репозиторий PowerBI-CSharp на сайте GitHub](https://github.com/Microsoft/PowerBI-CSharp)  
[Репозиторий PowerBI-Node на сайте GitHub](https://github.com/Microsoft/PowerBI-Node)  
У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)

