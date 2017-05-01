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
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: a740813b013e75a098836290ad8e9ebe76d33b37
ms.lasthandoff: 04/18/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Поставщики диспетчера ресурсов, регионы, версии API и схемы
В этом разделе представлен список поддерживаемых поставщиков ресурсов для Azure Resource Manager.

При развертывании ресурсов вам также необходимо знать, в каких регионах поддерживаются эти ресурсы и какие версии API доступны для ресурсов. В разделе [Поддерживаемые регионы](#supported-regions) показано, как определить, какие именно регионы работают для вашей подписки и ваших ресурсов. В разделе [Поддерживаемые версии API](#supported-api-versions) показано, как определить, какие версии API вы сможете использовать.

Чтобы узнать, какие службы поддерживаются на портале Azure и классическом портале, см. в [таблице доступности портала Azure](https://azure.microsoft.com/features/azure-portal/availability/). Перечень служб, поддерживающих перемещение ресурсов, см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

В следующих таблицах указано, какие службы Майкрософт поддерживают развертывание и управление с помощью Resource Manager, а какие не поддерживают. Также существует множество сторонних поставщиков ресурсов, поддерживающих Resource Manager. В разделе [Поставщики и типы ресурсов](#resource-providers-and-types) описано, как просмотреть все поставщики ресурсов.

## <a name="compute"></a>Среда выполнения приложений
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| пакетная служба; |Да |[Пакетная служба (REST)](/rest/api/batchservice) |[Ресурсы пакетной службы](/azure/templates/microsoft.batch/batchaccounts) |
| Реестр контейнеров |Да |[REST реестра контейнеров ](/rest/api/containerregistry) |[Ресурсы реестра контейнеров](/azure/templates/microsoft.containerregistry/registries) |
| Служба контейнеров |Да |[REST службы контейнеров](/rest/api/compute/containerservices) |[Ресурсы службы контейнеров](/azure/templates/microsoft.containerservice/containerservices) |
| Службы жизненного цикла Dynamics |Да | | |
| Наборы масштабирования |Да |[REST набора масштабирования](/rest/api/virtualmachinescalesets/) |[Ресурсы масштабируемого набора](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |Да |[Service Fabric Rest](/rest/api/servicefabric) | [Ресурсы Service Fabric](/azure/templates/microsoft.servicefabric/clusters) |
| Виртуальные машины |Да |[Интерфейс REST виртуальной машины](/rest/api/compute/virtualmachines) |[Ресурсы виртуальной машины](/azure/templates/microsoft.compute/virtualmachines) |
| виртуальные машины (классические); |Ограничено |- |- |
| Remote App |Нет |- |- |
| Облачные службы (классические) |Ограничено (см. ниже) |- |- |

Пункт "Виртуальные машины (классические)" относится к ресурсам, которые были развернуты с помощью классической модели развертывания, а не через модель развертывания диспетчера ресурсов. Как правило эти ресурсы не поддерживают операции диспетчера ресурсов, однако некоторые операции стали доступны. Дополнительные сведения об этих моделях развертывания вы найдете в статье [Общие сведения о развертывании с помощью диспетчера ресурсов и классическом развертывании](resource-manager-deployment-model.md). 

Облачные службы (классические) можно использовать с другими классическими ресурсами. Однако классические ресурсы не используют все функции Resource Manager, и для будущих решений это не оптимальный вариант. Вместо этого рассмотрите возможность изменения инфраструктуры приложений для использования ресурсов из пространств имен Microsoft.Compute, Microsoft.Storage и Microsoft.Network.

## <a name="networking"></a>Сеть
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| Шлюз приложений |Да |[REST шлюза приложений](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Ресурсы шлюза приложений](/azure/templates/microsoft.network/applicationgateways) |
| DNS |Да |[Интерфейс REST службы DNS](/rest/api/dns) |[Ресурсы DNS](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |Да |[REST для ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [Ресурсы ExpressRoute](/azure/templates/microsoft.network/expressroutecircuits) |
| Подсистема балансировки нагрузки |Да |[Интерфейс REST балансировщика нагрузки](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Ресурсы подсистемы балансировки нагрузки](/azure/templates/microsoft.network/loadbalancers) |
| Диспетчер трафика |Да |[Интерфейс REST диспетчера трафика](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Ресурсы диспетчера трафика](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| Виртуальные сети |Да |[Интерфейс REST виртуальной сети](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Ресурсы виртуальной сети](/azure/templates/microsoft.network/virtualnetworks) |
| Сетевой шлюз |Да |[Сетевой шлюз REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [Ресурсы подключения](/azure/templates/microsoft.network/connections) <br /> [Ресурсы локального сетевого шлюза](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Ресурсы виртуального сетевого шлюза](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>Хранилище
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- | --- |
| Импорт и экспорт | Да | [REST импорта и экспорта](/rest/api/storageimportexport/) | [Ресурсы импорта и экспорта](/azure/templates/microsoft.importexport/jobs) |
| Хранилище |Да |[REST для службы хранилища](/rest/api/storagerp) |[Ресурсы службы хранилища](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |Да | | |

## <a name="databases"></a>Базы данных
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- | --- |
| DocumentDB |Да |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[Ресурсы DocumentDB](/azure/templates/microsoft.documentdb/databaseaccounts) |
| кэш Redis; |Да | [REST для кэша Redis](/rest/api/redis) |[Ресурсы Redis](/azure/templates/microsoft.cache/redis) |
| База данных SQL |Да |[Интерфейс REST базы данных SQL](/rest/api/sql) |[Ресурсы базы данных SQL](/azure/templates/microsoft.sql/servers) |
| Хранилище данных SQL |Да | | |

## <a name="web--mobile"></a>Интернет и мобильные устройства
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| Приложения API |Да | [REST для службы приложений](/rest/api/appservice) |[Веб-ресурсы](/azure/templates/microsoft.web/sites) |
| Управление API |Да |[Интерфейс REST управления API](/rest/api/apimanagement) |[Ресурсы API управления](/azure/templates/microsoft.apimanagement/service) |
| Регистрация сертификата | Да | [REST регистрации сертификата](/rest/api/appservice/appservicecertificateorders) | [Ресурсы регистрации сертификата](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |Да | | | |
| Регистрация домена | Да | [Регистрация домена](/rest/api/appservice/domains) | [Ресурсы регистрации домена](/azure/templates/microsoft.domainregistration/domains)  |
| Приложение-функция |Да | [REST приложения-функции](/rest/api/appservice) | [Веб-ресурсы](/azure/templates/microsoft.web/sites) |
| Приложения логики |Да |[REST Logic Apps](/rest/api/logic) |[Ресурсы приложения логики](/azure/templates/microsoft.logic/workflows) |
| Мобильные приложения |Да | [REST для службы приложений](/rest/api/appservice) | [Веб-ресурсы](/azure/templates/microsoft.web/sites) |
| Взаимодействие через мобильные устройства |Да |[REST служб мобильного взаимодействия](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| Поиск |Да |[Поиск REST](/rest/api/searchservice) | [Ресурсы Поиска](/azure/templates/microsoft.search/searchservices) |
| Веб-приложения |Да | [REST веб-приложений](/rest/api/appservice/webapps) | [Веб-ресурсы](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>Аналитика
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона | 
| --- | --- | --- | --- |
| службы Analysis Services | Да | [REST службы Analysis Service](/rest/api/analysisservices) | [Ресурсы служб Analysis Services](/azure/templates/microsoft.analysisservices/servers) |
| Cognitive Services |Да | [REST для Cognitive Services](/rest/api/cognitiveservices) |[Ресурсы служб Cognitive Services](/azure/templates/microsoft.cognitiveservices/accounts) |
| Каталог данных |Да |[REST каталога данных](/rest/api/datacatalog) |[Схема каталога данных](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Фабрика данных |Да |[Интерфейс REST фабрики данных](/rest/api/datafactory) | |
| Аналитика озера данных |Да | [REST для Data Lake](/rest/api/datalakeanalytics) |[Ресурсы Data Lake Analytics](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Хранилище озера данных |Да |[REST Data Lake Store](/rest/api/datalakestore) |[Ресурсы Data Lake Store](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |Да |[Интерфейс REST HDInsights](/rest/api/hdinsight) | |
| Машинное обучение |Да |[REST машинного обучения](/rest/api/machinelearning) |[Ресурсы машинного обучения](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Анализ потока |Да |[Steam Analytics REST](/rest/api/streamanalytics) | |
| Power BI |Да |[REST Power BI Embedded](/rest/api/powerbiembedded) |[Ресурсы Power BI](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>Интернет вещей
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| Концентратор событий |Да |[Интерфейс REST концентратора событий](/rest/api/eventhub) |[Ресурсы концентратора событий](/azure/templates/microsoft.eventhub/namespaces) |
| Центры IoT |Да |[REST для центра IoT](/rest/api/iothub) |[Ресурсы Центра Интернета вещей](/azure/templates/microsoft.devices/iothubs) |
| Центры уведомлений |Да |[Интерфейс REST концентратора уведомлений](/rest/api/notificationhubs) |[Ресурсы центра уведомлений](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>Мультимедиа и CDN
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| CDN |Да |[CDN REST](/rest/api/cdn) |[Ресурсы CDN](/azure/templates/microsoft.cdn/profiles) |
| Служба мультимедиа |Да |[REST служб мультимедиа](/rest/api/media) |[Ресурсы мультимедиа](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>Интеграция Enterprise
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| Службы BizTalk |Да | |[Схема BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Передача | Да |  | [Ресурсы ретранслятора](/azure/templates/microsoft.relay/namespaces) |
| Служебная шина |Да |[REST служебной шины](/rest/api/servicebus) |[Ресурсы служебной шины](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>Управление удостоверениями и доступом
Azure Active Directory работает с Resource Manager, чтобы активировать для вашей подписки управление доступом на основе ролей. Дополнительные сведения об управлении доступом на основе ролей и Active Directory см. в [этой статье](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Службы разработки
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| Монитор |Да |[REST монитора](/rest/api/monitor) |[Ресурсы аналитики](/azure/templates/microsoft.insights/alertrules) |
| Карты Bing |Да | | |
| DevTest Labs |Да | [REST DevTest Labs](/rest/api/dtl) |[Ресурсы DevTest Labs](/azure/templates/microsoft.devtestlab/labs) |
| Учетная запись Visual Studio |Да | |[Схема Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>Управление и безопасность
| служба | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- |
| Помощник | Да | [Помощник по REST](/rest/api/advisor/) | - |
| Автоматизация |Да |[REST автоматизации](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Automation resources](/azure/templates/microsoft.automation/automationaccounts) (Ресурсы службы автоматизации) |
| Выставление счетов | Да | [Выставление счетов для REST](/rest/api/billing/) | - |
| хранилище ключей; |Да |[REST хранилища ключей](/rest/api/keyvault) |[Ресурсы Key Vault](/azure/templates/microsoft.keyvault/vaults) |
| Operational Insights; |Да | | |
| Служба восстановления |Да |[REST служб восстановления](/rest/api/recoveryservices) |[Ресурсы служб восстановления](/azure/templates/microsoft.recoveryservices/vaults) |
| Планировщик |Да |[Интерфейс REST планировщика](/rest/api/scheduler) |[Ресурсы планировщика](/azure/templates/microsoft.scheduler/jobcollections) |
| Безопасность |Да |[REST безопасности](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| Управление сервером | Да | [REST управления сервером](/rest/api/servermanagement/) | [Ресурсы управления сервером](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>Диспетчер ресурсов
| Функция | Доступность диспетчера ресурсов | Интерфейс REST API | Формат шаблона |
| --- | --- | --- | --- | --- |
| Авторизация |Да |[REST авторизации](/rest/api/authorization) |[Ресурсы авторизации](/azure/templates/microsoft.authorization/locks) |
| Ресурсы |Да |[REST ресурсов](/rest/api/resources) |[Ресурсы развертывания](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>Поставщики и типы ресурсов
При развертывании ресурсов часто бывает необходимо получить сведения о типах и поставщиках ресурсов. Эту информацию можно получить с помощью REST API, Azure PowerShell или интерфейса командной строки Azure.

Чтобы работать с поставщиком ресурсов, его необходимо регистрировать с использованием вашей учетной записи. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. В приведенных в разделе примерах показано, как получить состояние регистрации поставщика ресурсов и зарегистрировать поставщик ресурсов.

### <a name="portal"></a>Microsoft Azure
Можно легко просмотреть список поддерживаемых поставщиков ресурсов, выбрав **Поставщики ресурсов** в колонке подписки. Чтобы зарегистрировать в подписке поставщик ресурсов, щелкните ссылку **Зарегистрировать**.
   
![список поставщиков ресурсов](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
Чтобы получить список всех доступных поставщиков ресурсов, включая их типы, расположения, версии API и состояние регистрации, используйте операцию [List all resource providers](https://docs.microsoft.com/rest/api/resources/providers#Providers_List). Для регистрации поставщика ресурсов см. раздел [Register a subscription with a resource provider](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register) (Регистрация поставщика ресурсов в подписке).

### <a name="powershell"></a>PowerShell
Следующий пример демонстрирует получение всех доступных поставщиков ресурсов.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


В следующем примере показано, как получить типы ресурсов для определенного поставщика ресурсов.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

Для регистрации поставщика ресурсов укажите пространство имен:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Интерфейс командной строки Azure
Следующий пример демонстрирует получение всех доступных поставщиков ресурсов.

```azurecli
az provider list
```

Просмотреть информацию для определенного поставщика ресурсов можно с помощью следующей команды:

```azurecli
az provider show --namespace Microsoft.Web
```

Для регистрации поставщика ресурсов укажите пространство имен:

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure
В следующем примере показано, как получить поддерживаемые расположения для веб-сайтов.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>Поддерживаемые версии API
При развертывании шаблона нужно указать версию API, используемую для создания каждого ресурса. Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API. Следовательно, версия API, которая указывается в шаблоне, влияет на то, какие свойства можно указать в шаблоне. Как правило, при создании шаблонов следует выбирать последнюю версию API. Для существующих шаблонов можно решить, хотите ли вы продолжать использовать более раннюю версию API или хотите обновить шаблон до последней версии, чтобы воспользоваться новыми возможностями.

### <a name="portal"></a>Microsoft Azure
Поддерживаемые версии API определяются таким же образом, как и поддерживаемые регионы (описано выше).

### <a name="rest-api"></a>Интерфейс REST API
Выяснить, какие версии API доступны для разных типов ресурсов, вы можете с помощью операции [вывода списка всех поставщиков ресурсов](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
В следующем примере показано, как получить доступные версии API для конкретного типа ресурса.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

Выходные данные должны быть следующего вида.

```powershell
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
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Получить доступные версии API для поставщика ресурсов можно с помощью следующей команды:

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Сведения о развертывании ресурсов см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).


