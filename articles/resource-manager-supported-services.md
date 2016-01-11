<properties
   pageTitle="Поддерживаемые службы, регионы, схемы и версии диспетчера ресурсов | Microsoft Azure"
   description="Описывает поставщиков ресурсов, которые поддерживают диспетчер ресурсов, их схемы и доступные версии API, а также регионы, допускающие размещение ресурсов."
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
   ms.date="12/22/2015"
   ms.author="tomfitz"/>

# Поставщики диспетчера ресурсов, регионы, версии API и схемы

Диспетчер ресурсов Azure предлагает новый способ развертывания служб, составляющих ваши приложения, и управления ими.
Большинство служб (хотя и не все) поддерживает диспетчер ресурсов, а некоторые службы поддерживают диспетчер ресурсов лишь частично. Корпорация Майкрософт обеспечит поддержку диспетчера ресурсов для каждой службы, имеющей важное значение для будущих решений, однако перед обеспечением согласованной поддержки необходимо знать текущее состояние для каждой службы. В этом разделе представлен список поддерживаемых поставщиков ресурсов для диспетчера ресурсов Azure.

При развертывании ресурсов вам также необходимо знать, в каких регионах поддерживаются эти ресурсы и какие версии API доступны для ресурсов. В разделе [Поддерживаемые регионы](#supported-regions) показано, как определить, какие именно регионы будут работать для вашей подписки и ресурсов. В разделе [Поддерживаемые версии API](#supported-api-versions) показано, как определить, какие версии API вы сможете использовать.

Чтобы узнать, какие службы поддерживаются на портале Azure и классическом портале, см. статью [Диаграмма доступности портала Azure](https://azure.microsoft.com/features/azure-portal/availability/).

В следующих таблицах указано, какие службы поддерживают развертывание и управление с помощью диспетчера ресурсов, а какие не поддерживают. Столбец под названием **Перемещение ресурсов** содержит сведения о том, можно ли переместить ресурсы этого типа в новую группу ресурсов и новую подписку. Ссылка в столбце **Шаблоны быстрого запуска** отправляет запрос к репозиторию шаблонов быстрого запуска Azure для указанного поставщика ресурсов. Шаблоны быстрого запуска добавляются и обновляются на регулярной основе. Наличие ссылки для определенной службы не обязательно означает, что запрос возвратит шаблоны из репозитория.


## Среда выполнения приложений

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| Виртуальные машины | Да | Нет | [Создание виртуальной машины](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Пакетная служба | Да | Да | [Пакетная служба (REST)](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Службы жизненного цикла Dynamics | Да | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (предварительная версия) | Да | | [Service Fabric Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Виртуальные машины (классические) | Ограничена | Частично (см. ниже) | - | - | | Удаленное приложение | Нет | - | - | - |

Пункт "Виртуальные машины (классические)" относится к ресурсам, которые были развернуты с помощью классической модели развертывания, а не через модель развертывания диспетчера ресурсов. Как правило эти ресурсы не поддерживают операции диспетчера ресурсов, однако некоторые операции стали доступны. Дополнительные сведения об этих моделях развертывания вы найдете в статье [Общие сведения о развертывании диспетчера ресурсов и классическом развертывании](resource-manager-deployment-model.md).

Ресурсы типа "Виртуальные машины (классические)" можно переместить в новую группу ресурсов, но не в новую подписку.

## Сеть

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Шлюз приложений | Да | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Да | | [Создание зоны DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Подсистема балансировки нагрузки | Да | | [Создание балансировщика нагрузки](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Виртуальные сети | Да | Нет | [Создание виртуальной сети](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Диспетчер трафика | Да | | [Создание профиля диспетчера трафика](https://msdn.microsoft.com/library/azure/mt163581.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| ExpressRoute | Да | Нет | [REST для ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

## Хранилище данных

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| DocumentDB | Да | Да | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Хранилище | Да | Нет | [Создание хранилища](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Учетная запись хранения](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Кэш Redis | Да | Да | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| База данных SQL | Да | Да | [Создание базы данных](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Поиск | Да | Да | [Поиск REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Хранилище данных SQL | Да | | | |
| StorSimple | Нет | - | - | - | | Управляемый кэш | Нет | - | - | - |

## Интернет и мобильные устройства

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Управление API | Да | Да | [Создание API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Приложения API | Да | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [Приложения API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Веб-приложения | Да | Да, с ограничениями (см. ниже) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| Центры уведомлений | Да | Да | [Создание центра уведомлений](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| Приложения логики | Да | Да | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Взаимодействие через мобильные устройства | Да | Да | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

При работе с веб-приложениями невозможно переместить только план службы приложения. Варианты перемещения веб-приложений:

- Переместите все ресурсы из одной группы ресурсов в другую, если в целевой группе нет ресурсов Microsoft.Web.
- Переместите веб-приложения в другую группу ресурсов, но сохраните план службы приложений в исходной группе.

## Аналитика

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Концентратор событий | Да | | [Создание концентратора событий](https://msdn.microsoft.com/library/azure/dn790676.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Анализ потока | Да | | [Steam Analytics REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | Да | Да | [Создать кластер](https://msdn.microsoft.com/library/azure/mt622302.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Фабрика данных | Да | Да | [Создание фабрики данных](https://msdn.microsoft.com/library/azure/dn906717.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Машинное обучение | Нет | - | - | - | | Каталог данных | Нет | - | - | - |

## Мультимедиа и CDN

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| CDN | Да | | [CDN REST](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Служба мультимедиа | Нет | | | |


## Гибридная интеграция

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Службы BizTalk | Да | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Service Bus | Да | | [REST служебной шины](https://msdn.microsoft.com/library/azure/hh780717.aspx) | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Служба архивации| Нет | - | - | - | | Site Recovery | Нет | - | - | - |

## Управление удостоверениями и доступом 

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Azure Active Directory | Нет | - | - | - | | Azure Actice Directory B2C | Нет | - | - | - | | Multi-Factor Authentication | Нет | - | - | - |

## Службы разработки 

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | Да | Нет | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Карты Bing | Да | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Лаборатории по разработке и тестированию (предварительная версия) | Да | | | [2015-05-21-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Учетная запись Visual Studio | Да | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Управление и безопасность

| служба | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Автоматизация | Да | Да | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| хранилище ключей; | Да | Да | [REST хранилища ключей](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Хранилище ключей](resource-manager-template-keyvault.md)<br />[Секретный код хранилища ключей](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Планировщик | Да | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Operational Insights; | Да | Да | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| Центры IoT | Да | | [Создать центр IoT](https://msdn.microsoft.com/library/azure/mt589013.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Безопасность (предварительная версия) | Да | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Диспетчер ресурсов

| Функция | Доступность диспетчера ресурсов | Перемещение ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| Авторизация | Да | Недоступно | [Блокировки управления](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Контроль доступа на основе ролей](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Блокировка ресурса](resource-manager-template-lock.md)<br />[Назначения ролей](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ресурсы | Да | Недоступно | [Связанные ресурсы](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Ссылки на ресурсы](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Поддерживаемые регионы

При развертывании ресурсов обычно требуется указать для них регион. Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, препятствующие использованию определенных регионов, поддерживающих ресурс. Эти ограничения могут быть связаны с аспектами налогообложения в стране вашего проживания или с политикой, установленной администратором вашей подписки, где разрешен лишь ограниченный перечень регионов.

Полный список всех поддерживаемых регионов для всех служб Azure см. в статье [Службы по региону](https://azure.microsoft.com/regions/#services). Но в этот список могут входить регионы, которые не поддерживаются вашей подпиской. Можно определить регионы для определенного типа ресурса, поддерживаемого подпиской, выполнив одну из следующих команд.

### Интерфейс REST API

Выяснить, какие регионы доступны в вашей подписке для конкретного типа ресурсов, вы можете с помощью операции [вывода списка всех поставщиков ресурсов](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

В следующем примере показано, как получить поддерживаемые регионы для веб-сайтов с помощью Azure PowerShell 1.0. Дополнительные сведения о выпуске 1.0 см. в записи блога [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

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

Вы можете отфильтровать результаты расположения (например, с помощью средства **jq**). Дополнительные сведения о таких средствах, как jq, см. в статье [Полезные средства для работы с Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

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

Выяснить, какие версии API доступны для разных типов ресурсов, вы можете с помощью операции [вывода списка всех поставщиков ресурсов](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

В следующем примере показано, как получить доступные версии API для конкретного типа ресурса с помощью Azure PowerShell 1.0.

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
- Сведения о развертывании ресурсов см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1223_2015-->