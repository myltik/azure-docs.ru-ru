---
title: "Библиотеки Azure для .NET Framework и .NET Core | Документация Майкрософт"
description: "Список всех библиотек Azure и пакеты NuGet для .NET Framework и .NET Core."
keywords: "Azure .NET, пакеты SDK, справочник по API Azure .NET, библиотека классов Azure .NET, Azure NuGet, Azure Core"
author: camsoper
manager: douge
ms.author: casoper
ms.date: 04/06/2016
ms.topic: managed-reference
ms.prod: azure
ms.technology: azure
ms.devlang: dotnet
ms.assetid: 
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: fd38e1c9b0ebb89d930274ab92b63f4fb0219ca0
ms.lasthandoff: 04/13/2017

---

# <a name="azure-libraries-for-net"></a>Библиотеки Azure для .NET

Используйте службы Azure в своих приложениях и управляйте ими с помощью библиотек в пакетах SDK .NET для Azure.

## <a name="packages"></a>Пакеты

> [!TIP]
> [Библиотеки Fluent](https://azure.microsoft.com/blog/simpler-azure-management-libraries-for-net/) (имена файлов заканчиваются на **\*.Fluent**) улучшают процесс разработки, предоставляя объектно ориентированные API высокого уровня, оптимизированные для удобства чтения и записи. Они позволяют четко определить необходимые, необязательные или неизменяемые компоненты. Эти библиотеки можно запустить параллельно с не-Fluent библиотеками, поэтому вы можете использовать пакеты Fluent, если их синтаксис для вас предпочтительнее. [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent) — это накопительный пакет, в котором содержатся все библиотеки управления Fluent.

служба | Взаимодействие со службой | Управление ресурсами службы
--------|---------------------------|-------------------------
[Службы Analysis Services](/azure/analysis-services/) | | [Microsoft.Azure.Management.Analysis](https://www.nuget.org/packages/Microsoft.Azure.Management.Analysis)
[ApiManagement](/azure/api-management/) | | [Microsoft.Azure.Management.ApiManagement](https://www.nuget.org/packages/Microsoft.Azure.Management.ApiManagement)
[Авторизация](/rest/api/authorization) | | [Microsoft.Azure.Management.Authorization](https://www.nuget.org/packages/Microsoft.Azure.Management.Authorization)
[Автоматизация](/azure/automation/) | | [Microsoft.Azure.Management.Automation](https://www.nuget.org/packages/Microsoft.Azure.Management.Automation)
[Azure Active Directory](/azure/active-directory) | [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) | 
[Архивация](/azure/backup/) | | [Microsoft.Azure.Management.RecoveryServices.Backup](https://www.nuget.org/packages/Microsoft.Azure.Management.RecoveryServices.Backup)
[Пакетная служба](/azure/batch/) | [Azure.Batch](https://www.nuget.org/packages/Azure.Batch) | [Microsoft.Azure.Management.Batch.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch.Fluent)<br/>[Microsoft.Azure.Management.Batch](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch)
[Выставление счетов](/azure/billing/) | | [Microsoft.Azure.Management.Billing](https://www.nuget.org/packages/Microsoft.Azure.Management.Billing)
[CDN](/azure/cdn/) | | [Microsoft.Azure.Management.Cdn.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Cdn.Fluent)<br/>[Microsoft.Azure.Management.Cdn](https://www.nuget.org/packages/Microsoft.Azure.Management.Cdn)
[Cognitive Services](/azure/cognitive-services/) | | [Microsoft.Azure.Management.CognitiveServices](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices)
[Приложения для коммерции](/azure/billing/billing-usage-rate-card-overview) | | [Microsoft.Azure.Commerce.UsageAggregates](https://www.nuget.org/packages/Microsoft.Azure.Commerce.UsageAggregates)
[Реестр контейнеров](/azure/container-registry) | | [Microsoft.Azure.Management.ContainerRegistry](https://www.nuget.org/packages/Microsoft.Azure.Management.ContainerRegistry)
[Customer Insights](/dynamics365/customer-insights) | | [Microsoft.Azure.Management.CustomerInsights](https://www.nuget.org/packages/Microsoft.Azure.Management.CustomerInsights)
[Фабрика данных](/azure/data-factory/) | | [Microsoft.Azure.Management.DataFactories](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)
[Аналитика озера данных](/azure/data-lake-analytics/) | [Microsoft.Azure.Management.DataLake.Analytics](http://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics) | [Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)
[Data Lake Store](/azure/data-lake-store/) | [Microsoft.Azure.Management.DataLake.Store](http://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store) | [Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)<br/>[Microsoft.Azure.Management.DataLake.StoreUploader](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.StoreUploader)
[DevTest Labs](/azure/devtest-lab/) | | [Microsoft.Azure.Management.DevTestLabs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs)
[DNS](/azure/dns/) | | [Microsoft.Azure.Management.Dns.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Dns.Fluent)<br/>[Microsoft.Azure.Management.Dns](https://www.nuget.org/packages/Microsoft.Azure.Management.Dns)
[DocumentDB](/azure/documentdb/) | [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) | 
[Концентраторы событий](/azure/event-hubs/) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs)<br/>[Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor)
[Graph](/rest/api/graphrbac) | | [Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Graph.RBAC)
[HD Insight](/azure/hdinsight/) | [Microsoft.Azure.Management.HDInsight.Job](http://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight.Job) | [Microsoft.Azure.Management.HDInsight](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight)
[Центр IoT](/azure/iot-hub) | [Microsoft.Azure.Devices](https://www.nuget.org/packages/Microsoft.Azure.Devices)&nbsp;<b>&#42;</b><br/>[Microsoft.Azure.Devices.Client](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)&nbsp;<b>&#42;</b>
[хранилище ключей;](/azure/key-vault/) | [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) | [Microsoft.Azure.Management.KeyVault.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault.Fluent)<br/>[Microsoft.Azure.Management.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault)
[Служба Log Analytics](/azure/log-analytics/) | | [Microsoft.Azure.Management.OperationalInsights](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights)
[Logic Apps](/azure/logic-apps/) | | [Microsoft.Azure.Management.Logic](https://www.nuget.org/packages/Microsoft.Azure.Management.Logic)
[MachineLearning](/azure/machine-learning/) | | [Microsoft.Azure.Management.MachineLearning](https://www.nuget.org/packages/Microsoft.Azure.Management.MachineLearning)
[Службы мультимедиа](/azure/media-services/) | [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)
[Мониторинг](/azure/monitoring-and-diagnostics/) | | [Microsoft.Azure.Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights)
[Центры уведомлений](/azure/notification-hubs/) | [Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)&nbsp;<b>&#42;</b><br/>[WindowsAzure.Messaging.Managed](https://www.nuget.org/packages/WindowsAzure.Messaging.Managed)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.Management.NotificationHubs)
[PowerBI Embedded](/azure/power-bi-embedded/) | | [Microsoft.Azure.Management.PowerBIEmbedded](https://www.nuget.org/packages/Microsoft.Azure.Management.PowerBIEmbedded)
[Службы восстановления](/azure/site-recovery/) | | [Microsoft.Azure.Management.RecoveryServices](https://www.nuget.org/packages/Microsoft.Azure.Management.RecoveryServices)
[Кэш Redis](/azure/redis-cache/) | [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) | [Microsoft.Azure.Management.Redis.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Redis.Fluent)<br/>[Microsoft.Azure.Management.Redis](https://www.nuget.org/packages/Microsoft.Azure.Management.Redis)
[Диспетчер ресурсов](/azure/azure-resource-manager/) | | [Microsoft.Azure.Management.ResourceManager.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager.Fluent)<br/>[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)
[Планировщик](/azure/scheduler/) | | [Microsoft.Azure.Management.Scheduler](https://www.nuget.org/packages/Microsoft.Azure.Management.Scheduler)
[Поиск](/azure/search/) | [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.Search](https://www.nuget.org/packages/Microsoft.Azure.Management.Search)
[Служебная шина](/azure/service-bus/) | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus)
[Ретрансляция служебной шины](/azure/service-bus-relay/) | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay)
[Service Fabric](/azure/service-fabric/) | [Microsoft.ServiceFabric](https://www.nuget.org/profiles/servicefabric)&nbsp;<b>&#42;</b> | 
[База данных SQL](/azure/sql-database/) | [System.Data.SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | [Microsoft.Azure.Management.Sql.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql.Fluent)<br/>[Microsoft.Azure.Management.Sql](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
[Хранилище](/azure/storage/) | [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage) | [Microsoft.Azure.Management.Storage.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage.Fluent)<br/>[Microsoft.Azure.Management.Storage](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)<br/>[Microsoft.Azure.Storage.DataMovement](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement)
[Stream Analytics](/azure/stream-analytics/) | | [Microsoft.Azure.Management.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.Management.StreamAnalytics)
[Диспетчер трафика](/azure/traffic-manager/) | | [Microsoft.Azure.Management.TrafficManager.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.TrafficManager.Fluent)<br/>[Microsoft.Azure.Management.TrafficManager](https://www.nuget.org/packages/Microsoft.Azure.Management.TrafficManager)
[Виртуальные машины](/azure/virtual-machines/) | | [Microsoft.Azure.Management.Compute.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent)<br/>[Microsoft.Azure.Management.Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute)
[Виртуальная сеть](/azure/virtual-network/) | | [Microsoft.Azure.Management.Network.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Network.Fluent)<br/>[Microsoft.Azure.Management.Network](https://www.nuget.org/packages/Microsoft.Azure.Management.Network)
[Веб-приложения](/azure/app-service-web) | | [Microsoft.Azure.Management.AppService.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.AppService.Fluent)<br/>[Microsoft.Azure.Management.Websites](https://www.nuget.org/packages/Microsoft.Azure.Management.Websites)

\* *– Означает, что в настоящее время пакет несовместим с .NET Core.*

## <a name="installation"></a>Установка

### <a name="visual-studio"></a>Visual Studio

Если вы используете Visual Studio, вы можете импортировать пакет в проект с помощью **консоли диспетчера пакетов NuGet**.

1. Откройте решение Visual Studio и запустите консоль. Для этого зайдите в меню **Сервис**, щелкните **Диспетчер пакетов NuGet**, затем выберите **Консоль диспетчера пакетов**.  

    ![Консоль диспетчера пакетов](media/index/package-manager.png)

2. В окне консоли воспользуйтесь командлетом **Install-Package**, чтобы скачать и установить пакет NuGet.  Например, чтобы включить последнюю версию [библиотеки Azure Resource Management](http://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager) для .NET используйте следующий командлет:

    ```powershell
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre 
    ``` 
    Если необходимо использовать определенную версию, добавьте номер версии следующим образом:

    ```powershell
    Install-Package Microsoft.Azure.Management.ResourceMananger -Version 1.4.0-preview
    ``` 

### <a name="other-editors"></a>Другие редакторы

Если вы используете .NET Core с Visual Studio Code (или любой другой редактор), добавьте пакет как элемент **PackageReference**, внеся соответствующие изменения в файл CSPROJ.  В этом примере указана конкретная версия **Microsoft.Azure.Management.ResourceManager**, что является рекомендуемым решением, однако [плавающие версии](/nuget/consume-packages/package-references-in-project-files#floating-versions) NuGet также поддерживаются.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager" Version="1.4.0-preview" />
  </ItemGroup>

</Project>
```

> [!TIP]
> В предыдущих версиях .NET Core вместо csproj-файлов использовались файлы project.json.  Дополнительные сведения о сопоставлении project.json- и csproj-файлов [см. в этом документе](/dotnet/articles/core/tools/project-json-to-csproj).

