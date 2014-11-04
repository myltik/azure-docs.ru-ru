<properties title="Начало работы с хранилищем Azure" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

### <span id="whathappened">Что произошло с моим проектом?</span>

##### Добавленные ссылки

В проект Visual Studio добавлен пакет NuGet хранилища Azure.
Этот пакет добавляет следующие ссылки .NET:

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

Также добавлен пакет NuGet **Microsoft.Framework.ConfigurationModel.Json**.

##### Добавлена строка подключения к хранилищу Azure

В файле config.json проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. в разделе [ASP.NET vNext][ASP.NET vNext].

  [Приступая к работе]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Что произошло?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [ASP.NET vNext]: http://www.asp.net/vnext
