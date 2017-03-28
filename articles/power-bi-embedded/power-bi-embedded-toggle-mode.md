---
title: "Переключение между режимами просмотра и правки отчетов в Azure Power BI Embedded | Документация Майкрософт"
description: "Узнайте, как переключаться между режимами просмотра и правки отчетов в Power BI Embedded."
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
ms.openlocfilehash: f73bf05b41523a5833cc9366fb84cb7021b4b7a9
ms.lasthandoff: 03/14/2017

---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-embedded"></a>Переключение между режимами просмотра и правки отчетов в Power BI Embedded

Узнайте, как переключаться между режимами просмотра и правки отчетов в Power BI Embedded.

## <a name="creating-an-access-token"></a>Создание маркера доступа

Необходимо будет создать маркер доступа, который дает возможность как просматривать, так и изменять отчет. Чтобы изменить и сохранить отчет, необходимо разрешение маркера **Report.ReadWrite**. Дополнительные сведения см. в статье [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md).

> [!NOTE]
> Это позволит вам изменить существующий отчет и сохранить изменения. Если вам также требуется функция поддержки команды **Сохранить как**, то необходимо будет предоставить дополнительные разрешения. Дополнительные сведения см. в разделе [Области](power-bi-embedded-app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Конфигурация внедрения

Необходимо будет предоставить разрешения и параметр viewMode, чтобы кнопка "Сохранить" отображалась в режиме правки. Дополнительные сведения см. в статье [Embed Configuration Details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Сведения о конфигурации внедрения).

Например, в JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Таким образом указывается внедрение отчета в режиме просмотра (параметру **viewMode** задается значение **models.ViewMode.View**).

## <a name="view-mode"></a>Режим просмотра

Если вы находитесь в режиме правки, то переключиться в режим просмотра можно с помощью следующего кода JavaScript:

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Режим правки

Если вы находитесь в режиме просмотра, то переключиться в режим правки можно с помощью следующего кода JavaScript:

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>См. также

[Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)  
[Внедрение отчета в Power BI Embedded](power-bi-embedded-embed-report.md)  
[Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Репозиторий PowerBI-CSharp на сайте GitHub](https://github.com/Microsoft/PowerBI-CSharp)  
[Репозиторий PowerBI-Node на сайте GitHub](https://github.com/Microsoft/PowerBI-Node)  
У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)

