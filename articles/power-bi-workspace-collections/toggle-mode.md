---
title: Переключение между режимами просмотра и правки отчетов в коллекциях рабочих областей Power BI | Документация Майкрософт
description: Узнайте, как переключаться между режимами просмотра и правки отчетов в коллекциях рабочих областей Power BI.
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
ms.openlocfilehash: 23a8c4f0dd626a623df56de9546258a23d549d1a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409957"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Переключение между режимами просмотра и правки отчетов в коллекциях рабочих областей Power BI

Узнайте, как переключаться между режимами просмотра и правки отчетов в коллекциях рабочих областей Power BI.

> [!IMPORTANT]
> Использовать службу "Коллекции рабочих областей Power BI" не рекомендуется. Она будет доступна до июня 2018 года или до даты, указанной в контракте. Мы советуем организовать перенос приложения в Power BI Embedded, чтобы избежать прерываний в его работе. Сведения о том, как перенести данные из коллекций рабочих областей Power BI в Power BI Embedded, см. в [этой статье](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Создание маркера доступа

Необходимо создать маркер доступа, который дает возможность просматривать и изменять отчет. Чтобы изменить и сохранить отчет, необходимо разрешение маркера **Report.ReadWrite**. Дополнительные сведения см. в статье об [аутентификации и авторизации в коллекциях рабочих областей Power BI](app-token-flow.md).

> [!NOTE]
> Это позволяет изменить существующий отчет и сохранить изменения. Если вам также требуется функция поддержки команды **Сохранить как**, необходимо предоставить дополнительные разрешения. Дополнительные сведения см. в разделе [Области](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Конфигурация внедрения

Необходимо предоставить разрешения и параметр viewMode, чтобы кнопка "Сохранить" отображалась в режиме правки. Дополнительные сведения см. в статье [Embed Configuration Details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Сведения о конфигурации внедрения).

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

[Приступая к работе с примером Microsoft Power BI Embedded](get-started-sample.md)  
[Внедрение отчета в Power BI Embedded](embed-report.md)  
[Аутентификация и авторизация в коллекциях рабочих областей Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Репозиторий PowerBI-CSharp на сайте GitHub](https://github.com/Microsoft/PowerBI-CSharp)  
[Репозиторий PowerBI-Node на сайте GitHub](https://github.com/Microsoft/PowerBI-Node)  

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)
