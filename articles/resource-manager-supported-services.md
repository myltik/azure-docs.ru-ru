<properties
   pageTitle="Поддерживаемые службы и регионы диспетчера ресурсов | Microsoft Azure"
   description="Описывает поставщиков ресурсов, которые поддерживают диспетчер ресурсов, и регионы, допускающие размещение ресурсов."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2015"
   ms.author="tomfitz"/>

# Поддержка диспетчера ресурсов для служб, регионов и версий API

Диспетчер ресурсов Azure предлагает новый способ развертывания служб, составляющих ваши приложения, и управления ими. Большинство служб (хотя и не все) поддерживает диспетчер ресурсов, а некоторые службы поддерживают диспетчер ресурсов лишь частично. Корпорация Майкрософт обеспечит поддержку диспетчера ресурсов для каждой службы, имеющей важное значение для будущих решений, однако перед обеспечением согласованной поддержки необходимо знать текущее состояние для каждой службы. В этом разделе представлен список поддерживаемых поставщиков ресурсов для диспетчера ресурсов Azure.

При развертывании ресурсов вам также необходимо знать, в каких регионах поддерживаются эти ресурсы и какие версии API доступны для ресурсов. В разделе [Поддерживаемые регионы](#supported-regions) показано, как определить, какие именно регионы будут работать для вашей подписки и ресурсов. В разделе [Поддерживаемые версии API](#supported-api-versions) показано, как определить, какие версии API вы сможете использовать.

В следующих таблицах указано, какие службы поддерживают развертывание и управление с помощью диспетчера ресурсов, а какие не поддерживают. Столбец под названием **Перемещение ресурсов** содержит сведения о том, можно ли переместить ресурсы этого типа в новую группу ресурсов и новую подписку. Столбец под названием **Портал предварительной версии** указывает, можно ли создать службу с помощью портала предварительной версии.


## Среда выполнения приложений

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Виртуальные машины | Да | Да, много вариантов | Нет | [Создание виртуальной машины](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Пакетная служба | Да | [Да (только классическая версия)](https://portal.azure.com/#create/Microsoft.BatchAccount) | | [Пакетная служба (REST)](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Службы жизненного цикла Dynamics | Да | Нет | | | |
| Виртуальные машины (классические) | Ограничена | Да | Частично (см. ниже) | — | — |
| Удаленное приложение | Нет | — | — | — | — |
| Service Fabric | Нет | — | — | — | — |

Пункт "Виртуальные машины (классические)" относится к ресурсам, которые были развернуты с помощью классической модели развертывания, а не через модель развертывания диспетчера ресурсов. Как правило эти ресурсы не поддерживают операции диспетчера ресурсов, однако некоторые операции стали доступны. Дополнительные сведения об этих моделях развертывания вы найдете в статье [Общие сведения о развертывании с помощью диспетчера ресурсов и классическом развертывании](resource-manager-deployment-model.md).

Ресурсы типа "Виртуальные машины (классические)" можно переместить в новую группу ресурсов, но не в новую подписку.

## Интернет и мобильные устройства

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Управление API | Да | Нет | Да | [Создание API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| Приложения API | Да | [Да](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Веб-приложения | Да | [Да](https://portal.azure.com/#create/Microsoft.WebSite) | Да, с ограничениями (см. ниже) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Центры уведомлений | Да | [Да](https://portal.azure.com/#create/Microsoft.NotificationHub) | | [Создание центра уведомлений](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Приложения логики | Да | [Да](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | | | |
| Взаимодействие через мобильные устройства | Да | Нет | Да | | |

При работе с веб-приложениями невозможно переместить только план службы приложения. Варианты перемещения веб-приложений:

- Переместите все ресурсы из одной группы ресурсов в другую, если в целевой группе нет ресурсов Microsoft.Web.
- Переместите веб-приложения в другую группу ресурсов, но сохраните план службы приложений в исходной группе.


## Хранилище данных

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Да | [Да](https://portal.azure.com/#create/Microsoft.DocumentDB) | Да | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Хранилище | Да | [Да](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | | [Создание хранилища](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| Кэш Redis | Да | [Да](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | Да | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| База данных SQL | Да | [Да](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.7-preview) | Да | [Создание базы данных](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Поиск | Да | [Да](https://portal.azure.com/#create/Microsoft.Search) | Да | [Поиск REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| Хранилище данных SQL | Да | [Да](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | Нет | Нет | — | — | — |
| Управляемая служба кэша | Нет | Нет | — | — | — |


## Аналитика

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Концентратор событий | Да | Нет | | [Создание концентратора событий](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Анализ потока | Да | [Да](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | Да | [Да](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | | | |
| Фабрика данных | Да | [Да](https://portal.azure.com/#create/Microsoft.DataFactory) | Да | [Создание фабрики данных](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Машинное обучение | Нет | Нет | - | - | - |
| Каталог данных | Нет | Нет | - | - | - |

## Сеть

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Шлюз приложений | Да | | | | |
| DNS | Да | | | [Создание зоны DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Подсистема балансировки нагрузки | Да | | | [Создание балансировщика нагрузки](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Виртуальные сети | Да | [Да](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | Нет | [Создание виртуальной сети](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Диспетчер трафика | Да | Нет | | [Создание профиля диспетчера трафика](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| ExpressRoute | Да | Нет | Нет | [REST для ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## Мультимедиа и CDN

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Служба мультимедиа | Нет | Нет | | | |
| CDN | Нет | Нет | | | |

## Гибридная интеграция

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Службы BizTalk | Да | Нет | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Service Bus | Да | Нет | | [REST служебной шины](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| Служба архивации | Нет | Нет | - | - | - |
| Site Recovery | Нет | Нет | - | - | - |

## Управление удостоверениями и доступом 

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | Нет | Нет | — | — | — |
| Azure Actice Directory B2C | Нет | Нет | — | — | — |
| Многофакторная проверка подлинности | Нет | Нет | — | — | — |

## Службы разработки 

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Да | [Да](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Карты Bing | Да | [Да](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Учетная запись Visual Studio | Да | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## управления 

| служба | Доступность диспетчера ресурсов | Портал предварительной версии | Перемещение ресурсов | Интерфейс REST API | Схема |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Автоматизация | Да | [Да](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | | | |
| хранилище ключей; | Да | Нет | Да | [REST хранилища ключей](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Планировщик | Да | Нет | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights; | Да | Нет | Да | | |
| Центры IoT | Да | [Да](https://portal.azure.com/#create/Microsoft.IotHub) | | | |


## Поддерживаемые регионы

При развертывании ресурсов обычно требуется указать для них регион. Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, препятствующие использованию определенных регионов, поддерживающих ресурс. Эти ограничения могут быть связаны с аспектами налогообложения в стране вашего проживания или с политикой, установленной администратором вашей подписки, где разрешен лишь ограниченный перечень регионов.

Перед развертыванием ресурсов проверьте поддерживаемые регионы для типа ресурса, выполнив одну из следующих команд.

### Интерфейс REST API

Выяснить, какие регионы доступны в вашей подписке для конкретного типа ресурсов, можно с помощью операции [Вывод списка всех поставщиков ресурсов](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

В следующем примере показано, как получить поддерживаемые регионы для веб-сайтов с помощью Azure PowerShell 1.0 Preview. Дополнительные сведения о выпуске предварительной версии 1.0 см. в публикации [Предварительная версия Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Результат должен быть аналогичен приведенному ниже:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

В Azure PowerShell 0.9.8 используйте другую команду.

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Инфраструктура CLI Azure

Следующий пример возвращает все поддерживаемые расположения для каждого типа ресурсов.

    azure location list

Вы можете отфильтровать результаты, например, с помощью средства **jq**. Дополнительные сведения о jq и других средствах вы найдете в разделе [Полезные средства для работы с Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Возвращаемые данные:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Поддерживаемые версии API

При развертывании шаблона нужно указать версию API, используемую для создания каждого ресурса. Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в диспетчере ресурсов новых возможностей будет выпущена новая версия API REST. Следовательно, версия API, которая указывается в шаблоне, влияет на то, какие свойства можно указать в шаблоне. В общем случае при создании шаблонов следует выбирать последнюю версию API. Для существующих шаблонов можно решить, хотите ли вы продолжать использовать более раннюю версию API или хотите обновить шаблон до последней версии, чтобы воспользоваться новыми возможностями.

### Интерфейс REST API

Выяснить, какие версии API доступны для разных типов ресурсов, можно с помощью операции [Вывод списка всех поставщиков ресурсов](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

В следующем примере показано, как получить доступные версии API для конкретного типа ресурса с помощью предварительной версии Azure PowerShell 1.0.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
Результат должен быть аналогичен приведенному ниже:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Для Azure PowerShell 0.9.8 используйте такую команду:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Инфраструктура CLI Azure

Сохранить информацию для поставщика ресурсов (включая доступные версии API) в файл можно с помощью следующей команды.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Затем откройте этот файл и найдите в нем элемент **apiVersions**.

## Дальнейшие действия

- Сведения о создании шаблонов диспетчера ресурсов см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
- Сведения о развертывании ресурсов см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](./azure-portal/resource-group-template-deploy.md).

<!----HONumber=Nov15_HO1-->