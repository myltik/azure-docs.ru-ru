---
title: "Поддерживаемые службы Resource Manager | Документация Майкрософт"
description: "Описывает поставщиков ресурсов, которые поддерживают диспетчер ресурсов, их схемы и доступные версии API, а также регионы, допускающие размещение ресурсов."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: dabe7d9796ab24a257ea904bc5d978cb71d7e149
ms.openlocfilehash: af96c2b6063f7200cdeb6b51e5c729aa4643127f


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Поставщики диспетчера ресурсов, регионы, версии API и схемы
Диспетчер ресурсов Azure предлагает новый способ развертывания служб, составляющих ваши приложения, и управления ими. Большинство служб (хотя и не все) поддерживает диспетчер ресурсов, а некоторые службы поддерживают диспетчер ресурсов лишь частично. В этом разделе представлен список поддерживаемых поставщиков ресурсов для диспетчера ресурсов Azure.

При развертывании ресурсов вам также необходимо знать, в каких регионах поддерживаются эти ресурсы и какие версии API доступны для ресурсов. В разделе [Поддерживаемые регионы](#supported-regions) показано, как определить, какие именно регионы работают для вашей подписки и ваших ресурсов. В разделе [Поддерживаемые версии API](#supported-api-versions) показано, как определить, какие версии API вы сможете использовать.

Чтобы узнать, какие службы поддерживаются на портале Azure и классическом портале, см. в [таблице доступности портала Azure](https://azure.microsoft.com/features/azure-portal/availability/). Перечень служб, поддерживающих перемещение ресурсов, см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

В следующих таблицах указано, какие службы Майкрософт поддерживают развертывание и управление с помощью Resource Manager, а какие не поддерживают. Ссылка в столбце **Шаблоны быстрого запуска** отправляет запрос к репозиторию шаблонов быстрого запуска Azure для указанного поставщика ресурсов. Шаблоны быстрого запуска добавляются и обновляются на регулярной основе. Наличие ссылки для определенной службы не обязательно означает, что запрос возвращает шаблоны из репозитория. Также существует множество сторонних поставщиков ресурсов, поддерживающих Resource Manager. В разделе [Поставщики и типы ресурсов](#resource-providers-and-types) описано, как просмотреть все поставщики ресурсов.

## <a name="compute"></a>Среда выполнения приложений
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Пакетная служба |Да |[Пакетная служба (REST)](/rest/api/batchservice) |[Схема пакета](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Контейнер |Да |[REST службы контейнеров](/rest/api/containerregistry) |[Схема контейнера](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Службы жизненного цикла Dynamics |Да | | | |
| Наборы масштабирования |Да |[REST набора масштабирования](/rest/api/compute/virtualmachinescalesets) |[Схема масштабируемого набора](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |Да |[Service Fabric Rest](/rest/api/servicefabric) | [Схема Service Fabric](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Виртуальные машины |Да |[Интерфейс REST виртуальной машины](/rest/api/compute/virtualmachines) |[Схема виртуальной машины](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| виртуальные машины (классические); |Ограничено |- |- |- |
| Remote App |Нет |- |- |- |
| Облачные службы (классические) |Ограничено (см. ниже) |- |- |- |

Пункт "Виртуальные машины (классические)" относится к ресурсам, которые были развернуты с помощью классической модели развертывания, а не через модель развертывания диспетчера ресурсов. Как правило эти ресурсы не поддерживают операции диспетчера ресурсов, однако некоторые операции стали доступны. Дополнительные сведения об этих моделях развертывания вы найдете в статье [Общие сведения о развертывании с помощью диспетчера ресурсов и классическом развертывании](resource-manager-deployment-model.md). 

Облачные службы (классические) можно использовать с другими классическими ресурсами. Однако классические ресурсы не используют все функции Resource Manager, и для будущих решений это не оптимальный вариант. Вместо этого рассмотрите возможность изменения инфраструктуры приложений для использования ресурсов из пространств имен Microsoft.Compute, Microsoft.Storage и Microsoft.Network.

## <a name="networking"></a>Сеть
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Шлюз приложений |Да |[REST шлюза приложений](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |Да |[Интерфейс REST службы DNS](/rest/api/dns) |[Схема DNS](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |Да |[REST для ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Подсистема балансировки нагрузки |Да |[Интерфейс REST балансировщика нагрузки](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Схема подсистемы балансировки нагрузки](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Диспетчер трафика |Да |[Интерфейс REST диспетчера трафика](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Схема диспетчера трафика](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Виртуальные сети |Да |[Интерфейс REST виртуальной сети](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[Схема виртуальной сети](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN-шлюз |Да |[Сетевой шлюз REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[подключения](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Хранилище
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- | --- |
| Хранилище |Да |[Интерфейс REST службы хранилища](/rest/api/storagerp) |[Учетная запись хранения](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |Да | | | |

## <a name="databases"></a>Базы данных
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- | --- |
| DocumentDB |Да |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[Схема DocumentDB](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Кэш Redis |Да | [REST для кэша Redis](/rest/api/redis) |[Схема Redis](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| База данных SQL |Да |[Интерфейс REST базы данных SQL](/rest/api/sql) |[Схема базы данных SQL](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Хранилище данных SQL |Да | | | |

## <a name="web--mobile"></a>Интернет и мобильные устройства
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Приложения API |Да | [REST для службы приложений](/rest/api/appservice) |[Схема приложений API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Приложения API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Управление API |Да |[Интерфейс REST управления API](/rest/api/apimanagement) |[Схема управления API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |Да | | | |
| Приложение-функция |Да | [REST приложения-функции](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Приложения логики |Да |[REST Logic Apps](/rest/api/logic) |[Схема Logic Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Мобильные приложения |Да | [REST для службы приложений](/rest/api/appservice) |[Схема мобильных приложений](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Взаимодействие через мобильные устройства |Да |[REST служб мобильного взаимодействия](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Поиск |Да |[Поиск REST](/rest/api/searchservice) | [Схема поиска](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Веб-приложения |Да | [REST веб-приложений](/rest/api/appservice/webapps) |[Схема веб-приложений](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>Аналитика
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| службы Analysis Services | Да | [REST службы Analysis Service](/rest/api/analysisservices) | [Схема служб Analysis Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| Cognitive Services |Да | [REST для Cognitive Services](/rest/api/cognitiveservices) |[Схема Cognitive Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| Каталог данных |Да |[REST каталога данных](/rest/api/datacatalog) |[Схема каталога данных](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Фабрика данных |Да |[Интерфейс REST фабрики данных](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Аналитика озера данных |Да | [REST для Data Lake](/rest/api/datalakeanalytics) |[Схема Data Lake](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Хранилище озера данных |Да |[REST Data Lake Store](/rest/api/datalakestore) |[Схема Data Lake](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |Да |[Интерфейс REST HDInsights](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Машинное обучение |Да |[REST машинного обучения](/rest/api/machinelearning) |[Схема машинного обучения](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Stream Analytics |Да |[Steam Analytics REST](/rest/api/streamanalytics) | | |
| Power BI |Да |[REST Power BI Embedded](/rest/api/powerbiembedded) |[Схема Power BI](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>Интернет вещей
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Концентратор событий |Да |[Интерфейс REST концентратора событий](/rest/api/eventhub) |[Схема концентратора событий](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Центры IoT |Да |[REST для центра IoT](/rest/api/iothub) |[Схема Центра Интернета вещей](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Центры уведомлений |Да |[Интерфейс REST концентратора уведомлений](/rest/api/notificationhubs) |[Схема центра уведомлений](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Мультимедиа и CDN
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| CDN |Да |[CDN REST](/rest/api/cdn) |[Схема CDN](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Служба мультимедиа |Да |[REST служб мультимедиа](/rest/api/media) |[Схема службы мультимедиа](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) | |

## <a name="hybrid-integration"></a>Гибридная интеграция
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Службы BizTalk |Да | |[Схема BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Служба восстановления |Да |[REST служб восстановления](/rest/api/recoveryservices) |[Схема служб восстановления](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Service Bus |Да |[REST служебной шины](/rest/api/servicebus) |[Схема служебной шины](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Управление удостоверениями и доступом
Azure Active Directory работает с Resource Manager, чтобы активировать для вашей подписки управление доступом на основе ролей. Дополнительные сведения об управлении доступом на основе ролей и Active Directory см. в [этой статье](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Службы разработки
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Монитор |Да |[REST монитора](/rest/api/monitor) |[Схема Insights](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Карты Bing |Да | | | |
| DevTest Labs |Да | [REST для DevTest Labs](/rest/api/dtl) |[Схема DevTest Lab](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Учетная запись Visual Studio |Да | |[Схема Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>Управление и безопасность
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- |
| Автоматизация |Да |[REST автоматизации](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Схема службы автоматизации](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| хранилище ключей; |Да |[REST хранилища ключей](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[Секретный код хранилища ключей](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights; |Да | | | |
| Планировщик |Да |[Интерфейс REST планировщика](/rest/api/scheduler) |[Схема планировщика](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Безопасность (предварительная версия) |Да |[REST безопасности](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>Диспетчер ресурсов
| Функция | Доступность диспетчера ресурсов | Интерфейс REST API | Схема | Шаблоны быстрого запуска |
| --- | --- | --- | --- | --- | --- |
| Авторизация |Да |[REST авторизации](/rest/api/authorization) |[Блокировки ресурсов](resource-manager-template-lock.md)<br />[Назначения ролей](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ресурсы |Да |[REST ресурсов](/rest/api/resources) |[Ссылки на ресурсы](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>Поставщики и типы ресурсов
При развертывании ресурсов часто бывает необходимо получить сведения о типах и поставщиках ресурсов. Эту информацию можно получить с помощью REST API, Azure PowerShell или интерфейса командной строки Azure.

Чтобы работать с поставщиком ресурсов, его необходимо регистрировать с использованием вашей учетной записи. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. В приведенных ниже примерах показано, как получить состояние регистрации поставщика ресурсов и зарегистрировать поставщик ресурсов, если это необходимо.

### <a name="portal"></a>Microsoft Azure
Можно легко просмотреть список поддерживаемых поставщиков ресурсов, выбрав **Поставщики ресурсов** в колонке подписки. Чтобы зарегистрировать в подписке поставщик ресурсов, щелкните ссылку **Зарегистрировать**.
   
![список поставщиков ресурсов](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
Чтобы получить список всех доступных поставщиков ресурсов, включая их типы, расположения, версии API и состояние регистрации, используйте операцию [List all resource providers](https://docs.microsoft.com/rest/api/resources/providers#Providers_List). Для регистрации поставщика ресурсов см. раздел [Register a subscription with a resource provider](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register) (Регистрация поставщика ресурсов в подписке).

### <a name="powershell"></a>PowerShell
Следующий пример демонстрирует получение всех доступных поставщиков ресурсов.

    Get-AzureRmResourceProvider -ListAvailable


В следующем примере показано, как получить типы ресурсов для определенного поставщика ресурсов.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes

Выходные данные должны быть следующего вида.

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...

Для регистрации поставщика ресурсов укажите пространство имен:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Интерфейс командной строки Azure
Следующий пример демонстрирует получение всех доступных поставщиков ресурсов.

    azure provider list

Выходные данные должны быть следующего вида.

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Сохранить информацию для определенного поставщика ресурсов в файл можно с помощью следующей команды.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Для регистрации поставщика ресурсов укажите пространство имен:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Поддерживаемые регионы
При развертывании ресурсов обычно требуется указать для них регион. Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс. Эти ограничения могут быть связаны с аспектами налогообложения в стране вашего проживания или с политикой, установленной администратором вашей подписки, где разрешен лишь ограниченный перечень регионов. 

Полный список всех поддерживаемых регионов для всех служб Azure см. в разделе [Службы по региону](https://azure.microsoft.com/regions/#services). Однако этот список может включать в себя регионы, которые не поддерживаются вашей подпиской. Определить регионы для конкретного типа ресурса, которые поддерживает ваша подписка, можно с помощью портала, REST API, PowerShell или интерфейса командной строки Azure.

### <a name="portal"></a>Microsoft Azure
Чтобы просмотреть поддерживаемые регионы для типа ресурса, выполните следующие действия:

1. Выберите **Больше служб** > **Обозреватель ресурсов**.
   
    ![обозреватель ресурсов](./media/resource-manager-supported-services/select-resource-explorer.png)
2. Откройте узел **Поставщики**.
   
    ![выбор поставщиков](./media/resource-manager-supported-services/select-providers.png)
3. Выберите поставщик ресурсов и просмотрите поддерживаемые регионы и версии API.
   
    ![просмотр поставщика](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>Интерфейс REST API
Выяснить, какие регионы доступны в вашей подписке для конкретного типа ресурсов, можно с помощью операции [вывода списка всех поставщиков ресурсов](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
В следующем примере показано, как получить поддерживаемые регионы для веб-сайтов.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Выходные данные должны быть следующего вида.

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

### <a name="azure-cli"></a>Интерфейс командной строки Azure
Следующий пример возвращает все поддерживаемые расположения для каждого типа ресурсов.

    azure location list

Вы также можете отфильтровать полученные расположения с помощью такой служебной программы JSON, как [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Возвращаемые данные:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Поддерживаемые версии API
При развертывании шаблона нужно указать версию API, используемую для создания каждого ресурса. Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API. Следовательно, версия API, которая указывается в шаблоне, влияет на то, какие свойства можно указать в шаблоне. Как правило, при создании шаблонов следует выбирать последнюю версию API. Для существующих шаблонов можно решить, хотите ли вы продолжать использовать более раннюю версию API или хотите обновить шаблон до последней версии, чтобы воспользоваться новыми возможностями.

### <a name="portal"></a>Microsoft Azure
Поддерживаемые версии API определяются таким же образом, как и поддерживаемые регионы (описано выше).

### <a name="rest-api"></a>Интерфейс REST API
Выяснить, какие версии API доступны для разных типов ресурсов, вы можете с помощью операции [вывода списка всех поставщиков ресурсов](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
В следующем примере показано, как получить доступные версии API для конкретного типа ресурса.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

Выходные данные должны быть следующего вида.

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

### <a name="azure-cli"></a>Интерфейс командной строки Azure
Сохранить информацию для поставщика ресурсов (включая доступные версии API) в файл можно с помощью следующей команды.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Затем откройте этот файл и найдите в нем элемент **apiVersions** .

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Сведения о развертывании ресурсов см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).




<!--HONumber=Dec16_HO2-->


