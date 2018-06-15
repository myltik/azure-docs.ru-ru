---
title: Создание отчета из набора данных в коллекциях рабочих областей Power BI | Документация Майкрософт
description: Отчеты коллекций рабочих областей Power BI теперь можно создавать из набора данных в собственном приложении.
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
ms.openlocfilehash: 5c6a52edd708b6077820f2004e83ac7dee945610
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409522"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Создание отчета из набора данных в коллекциях рабочих областей Power BI

Отчеты коллекций рабочих областей Power BI теперь можно создавать из набора данных в собственном приложении.

> [!IMPORTANT]
> Использовать службу "Коллекции рабочих областей Power BI" не рекомендуется. Она будет доступна до июня 2018 года или до даты, указанной в договоре. Мы советуем организовать перенос приложения в Power BI Embedded, чтобы избежать прерываний в его работе. Сведения о том, как перенести данные из коллекций рабочих областей Power BI в Power BI Embedded, см. в [этой статье](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Метод проверки подлинности аналогичен методу внедрения отчета. Он основан на маркерах доступа, относящихся к конкретному набору данных. Маркеры, используемые для PowerBI.com, выдаются службой Azure Active Directory (AAD). Маркеры коллекции рабочих областей Power BI выдает ваше собственное приложение.

При создании внедренного отчета выдаваемые маркеры предназначаются для конкретного набора данных. Маркеры должны быть связаны с URL-адресом внедрения в том же элементе, чтобы каждый имел свой уникальный маркер. Чтобы создать внедренный отчет, в маркере доступа необходимо предоставить области *Dataset.Read и Workspace.Report.Create*.

## <a name="create-access-token-needed-to-create-new-report"></a>Создание маркера доступа, необходимого для создания отчета

В коллекциях рабочих областей Power BI используется маркер внедрения, который представляет собой JSON Web Token, подписанный HMAC. Маркеры подписываются с помощью ключа доступа из коллекции рабочих областей Power BI. По умолчанию маркеры внедрения используются для предоставления доступа к отчету с правами только для чтения, чтобы внедрить его в приложение. Маркеры внедрения выдаются для конкретного отчета и должны быть связаны с URL-адресом внедрения.

Маркеры доступа должны создаваться на сервере, так как ключи доступа используются для подписания или шифрования маркеров. Дополнительные сведения о создании маркера доступа см. в разделе [Аутентификация и авторизация в коллекциях рабочих областей Power BI](app-token-flow.md). Вы также можете ознакомиться с методом [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Здесь приведен пример с использованием пакета SDK для Power BI (.NET).

В этом примере у нас есть идентификатор набора данных, на основе которого требуется создать отчет. Необходимо также добавить области *Dataset.Read и Workspace.Report.Create*.

Для *класса PowerBIToken* необходимо установить [пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Установка пакета NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Код C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Создание пустого отчета

Чтобы создать отчет, необходимо предоставить конфигурацию создания. В нее входят маркер доступа, embedURL и datasetID, на основе которых требуется создать отчет. Для этого необходимо установить [пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). embedUrl станет https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Чтобы протестировать функциональные возможности, можно использовать [пример внедрения отчета JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/). В нем также приводятся примеры кода для различных операций, которые доступны.

**Установка пакета NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Код JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Вызов *powerbi.createReport()* приводит к тому, что пустой холст в режиме правки отобразится в элементе *div*.

![Новый пустой отчет](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Сохранение новых отчетов

Отчет не будет создан, пока вы не вызовите операцию **Сохранить как**. Это можно сделать, используя меню "Файл" или JavaScript.

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Отчет создается только после вызова операции **Сохранить как**. После сохранения на холсте по-прежнему будет отображаться набор данных в режиме правки, а не отчет. Необходимо перезагрузить новый отчет так же, как любой другой отчет.

![Меню "Файл" > "Сохранить как"](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Загрузка нового отчета

Для взаимодействия с новым отчетом необходимо внедрить его таким же образом, как приложение внедряет обычный отчет. Таким образом, специально для нового отчета должен быть выдан новый маркер, а затем вызван метод embed.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Автоматизация сохранения и загрузки нового отчета с помощью "сохраненного" события

Чтобы автоматизировать процесс "Сохранить как", а затем загрузить новый отчет, можно воспользоваться "сохраненным" событием. Это событие срабатывает, когда операция сохранения завершена и возвращает объект JSON, содержащий новый идентификатор reportId, имя отчета, старый идентификатор reportId (если он был), а также сведения об операции ("Сохранить как" или "Сохранить").

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Для автоматизации процесса дождитесь передачи "сохраненного" события, возьмите из него новый идентификатор reportId, создайте маркер и внедрите новый отчет с этим маркером.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>См. также

[Приступая к работе с примером Microsoft Power BI Embedded](get-started-sample.md)  
[Сохранение отчетов](save-reports.md)  
[Внедрение отчета в Power BI Embedded](embed-report.md)  
[Аутентификация и авторизация в коллекциях рабочих областей Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)