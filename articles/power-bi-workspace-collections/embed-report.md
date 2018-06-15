---
title: Внедрение отчета в коллекции рабочих областей Power BI | Документация Майкрософт
description: Узнайте, как внедрить отчет коллекций рабочих областей Power BI в приложение.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: b6fa46b1cf3a251d6116e7de6ef41a9e6d265c29
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31410358"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Внедрение отчета в коллекции рабочих областей Power BI

Узнайте, как внедрить отчет коллекций рабочих областей Power BI в приложение.

> [!IMPORTANT]
> Использовать службу "Коллекции рабочих областей Power BI" не рекомендуется. Она будет доступна до июня 2018 года или до даты, указанной в договоре. Мы советуем организовать перенос приложения в Power BI Embedded, чтобы избежать прерываний в его работе. Сведения о том, как перенести данные из коллекций рабочих областей Power BI в Power BI Embedded, см. в [этой статье](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

В этой статье мы рассмотрим, как внедрить в приложение отчет. Предполагается, что у вас уже есть отчет, который находится в рабочей области в коллекции рабочих областей. Если вы еще не выполнили этот шаг, прочитайте статью [Начало работы с коллекциями рабочих областей Power BI](get-started.md).

Можно воспользоваться пакетом SDK для .NET (C#) или Node.js, параллельно с JavaScript, чтобы быстро интегрировать в свое приложение службу коллекций рабочих областей Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Использование ключей доступа для интерфейсов REST API

Чтобы вызвать REST API, можно передать ключ доступа, который получается на портале Azure для заданной коллекции рабочих областей. Дополнительные сведения см. в статье [Начало работы с коллекциями рабочих областей Power BI](get-started.md).

## <a name="get-a-report-id"></a>Получение идентификатора отчета

Каждый маркер доступа основан на отчете. Необходимо получить данный идентификатор для отчета, который требуется внедрить. Это можно сделать на основе вызовов к интерфейсу REST API [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) (Получить отчеты). В результате будут возвращены идентификатор отчета и URL-адрес внедрения. Это можно сделать с помощью пакета SDK Power BI для .NET или прямого вызова REST API.

### <a name="using-the-power-bi-net-sdk"></a>Использование пакета SDK Power BI для .NET

При использовании пакета SDK для .NET необходимо создать учетные данные маркера, основанные на ключе доступа, полученного на портале Azure. Для этого необходимо установить [пакет NuGet для API Power BI](https://www.nuget.org/profiles/powerbi).

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

В коллекциях рабочих областей Power BI используются маркеры внедрения, которые представляют собой маркеры JSON Web Token, подписанные HMAC. Маркеры подписываются с помощью ключа доступа из коллекции рабочих областей Power BI. По умолчанию маркеры внедрения используются для предоставления доступа к отчету с правами только для чтения, чтобы внедрить его в приложение. Маркеры внедрения выдаются для конкретного отчета и должны быть связаны с URL-адресом внедрения.

Маркеры доступа должны создаваться на сервере, так как ключи доступа используются для подписания или шифрования маркеров. Дополнительные сведения о создании маркера доступа см. в разделе [Аутентификация и авторизация в коллекциях рабочих областей Power BI](app-token-flow.md). Вы также можете ознакомиться с методом [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Здесь приведен пример с использованием пакета SDK для Power BI (.NET).

Будет использоваться ранее полученный идентификатор отчета. После создания маркера внедрения воспользуйтесь ключом доступа для создания маркера, который вы сможете использовать в JavaScript. Для *класса PowerBIToken* необходимо установить [пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

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

При использовании маркеров внедрения может потребоваться ограничить использование ресурсов, к которым предоставляется доступ. Для этого можно создать маркер с заданной областью разрешений. Дополнительные сведения см. в разделе [Области](app-token-flow.md#scopes).

## <a name="embed-using-javascript"></a>Внедрение с помощью JavaScript

Когда у вас уже есть маркер доступа и идентификатор отчета, можно внедрить отчет с помощью JavaScript. Для этого необходимо установить [пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). embedUrl станет https://embedded.powerbi.com/appTokenReportEmbed.

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

Отчет будет автоматически внедрен, исходя из размера его контейнера. Чтобы переопределить заданный по умолчанию размер внедряемого элемента, просто добавьте атрибут класса CSS или встроенные стили для ширины и высоты.

## <a name="see-also"></a>См. также

[Приступая к работе с примером Microsoft Power BI Embedded](get-started-sample.md)  
[Аутентификация и авторизация в коллекциях рабочих областей Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Пакет NuGet для API Power BI](https://www.nuget.org/profiles/powerbi)
[Пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Репозиторий PowerBI-CSharp на сайте GitHub](https://github.com/Microsoft/PowerBI-CSharp)  
[Репозиторий PowerBI-Node на сайте GitHub](https://github.com/Microsoft/PowerBI-Node)  

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)
