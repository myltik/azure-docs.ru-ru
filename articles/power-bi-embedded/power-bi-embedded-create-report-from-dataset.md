---
title: "Создание отчета из набора данных в Azure Power BI Embedded | Документация Майкрософт"
description: "Отчеты Power BI Embedded теперь можно создавать из набора данных в собственном приложении."
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
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 457f53aa76059dbb2faed6b264102f1f59b9918a
ms.lasthandoff: 03/14/2017

---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-embedded"></a>Создание отчета из набора данных в Power BI Embedded

Отчеты Power BI Embedded теперь можно создавать из набора данных в собственном приложении. 

Метод проверки подлинности аналогичен методу при внедрении запроса. Он основан на маркерах доступа, относящихся к конкретному набору данных. Маркеры, используемые для PowerBI.com, выдаются Azure Active Directory (AAD), а маркеры Power BI Embedded — самой службой.

При создании внедренного отчета выдаваемые маркеры предназначены для конкретного набора данных. Маркеры должны быть связаны с URL-адресом внедрения в том же элементе, чтобы каждый имел свой уникальный маркер. Чтобы создать внедренный отчет, в маркере доступа необходимо предоставить области *Dataset.Read и Workspace.Report.Create*.

## <a name="create-access-token-needed-to-create-new-report"></a>Создание маркера доступа, необходимого для создания отчета

Power BI Embedded использует маркеры внедрения, которые являются подписанными веб-маркерами JSON (JSON Web Token) с поддержкой HMAC. Маркеры подписываются с помощью ключа доступа из коллекции рабочих областей Azure Power BI Embedded. По умолчанию маркеры внедрения используются для предоставления доступа к отчету с правами только для чтения, чтобы внедрить его в приложение. Маркеры внедрения выдаются для конкретного отчета и должны быть связаны с URL-адресом внедрения.

Маркеры доступа должны создаваться на сервере, так как ключи доступа используются для подписания или шифрования маркеров. Дополнительные сведения о создании маркера доступа см. в статье [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md). Вы также можете ознакомиться с методом [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Здесь приведен пример с использованием пакета SDK для Power BI (.NET).

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

Чтобы создать отчет, необходимо предоставить конфигурацию создания. В нее входят маркер доступа, embedURL и datasetID, на основе которых требуется создать отчет. Для этого необходимо установить [пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Параметр embedUrl будет иметь значение https://embedded.powerbi.com/appTokenReportEmbed.

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

Вызов *powerbi.createReport()* приведет к тому, что пустой холст в режиме правки отобразится в элементе *div*.

![](media/power-bi-embedded-create-report-from-dataset/pbi-embedded-create-new-report.png)

## <a name="save-new-reports"></a>Сохранение новых отчетов

Отчет фактически не будет создан, пока вы не вызовите операцию **Сохранить как**. Это можно сделать, используя меню "Файл" или JavaScript.

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

![](media/power-bi-embedded-create-report-from-dataset/pbi-embedded-save-new-report.png)

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

[Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)  
[Сохранение отчетов](power-bi-embedded-save-reports.md)  
[Внедрение отчета в Power BI Embedded](power-bi-embedded-embed-report.md)  
[Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Пакет NuGet для Power BI (Core)](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Пакет NuGet для Power BI (JavaScript)](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)
