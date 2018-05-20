---
title: Безопасный доступ к учетной записи Azure Cosmos DB с использованием конечной точки службы виртуальной сети Azure | Документация Майкрософт
description: В этом документе описываются шаги, необходимые для настройки конечной точки службы виртуальной сети Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: b07a159e69a11656555a8550b807cce0b2c9ef6c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Безопасный доступ к учетной записи Azure Cosmos DB с использованием конечной точки службы виртуальной сети Azure

Учетные записи Azure CosmosDB можно настроить для предоставления доступа только из определенной подсети виртуальной сети Azure. Включив [конечную точку службы](../virtual-network/virtual-network-service-endpoints-overview.md) Azure CosmosDB из виртуальной сети и ее подсети, трафик обеспечивает оптимальный и безопасный маршрут к Azure Cosmos DB.  

Azure Cosmos DB — это многомодельная глобально распределенная служба баз данных. Данные в учетной записи Azure Cosmos DB можно реплицировать в нескольких регионах. Когда Azure Cosmos DB настроена с конечной точкой службы виртуальной сети, каждая виртуальная сеть обеспечивает доступ из IP-адресов, принадлежащих к определенной подсети. На следующем рисунке показана иллюстрация Azure Cosmos DB, которая имеет включенную конечную точку службы виртуальной сети:

![Архитектура конечной точки службы виртуальной сети](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

После того как учетная запись Azure Cosmos DB настроена с конечной точкой службы виртуальной сети, к ней можно получить доступ только из указанной подсети. Удаляются все общедоступные подключения и Интернет. Подробные сведения о конечных точках службы см. в статье [Обзор конечных точек службы виртуальной сети](../virtual-network/virtual-network-service-endpoints-overview.md).

> [!NOTE]
> В настоящее время конечные точки службы виртуальной сети могут быть настроены для учетных записей API Mongo или SQL API для Azure Cosmos DB. В скором времени будет доступна возможность настройки конечных точек службы для других API и национальных облаков, таких как Azure для Германии или Azure для государственных организаций. Если имеется существующий брандмауэр IP, настроенный для вашей учетной записи Azure Cosmos DB, обратите внимание на его конфигурацию, удалите брандмауэр IP и затем настройте список управления доступом (ACL) конечной точки службы. После настройки конечной точки службы можно повторно включить брандмауэр IP, если это необходимо.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Настройка конечной точки службы с помощью портала Azure
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для существующей виртуальной сети Azure и подсети

1. В колонке **Все ресурсы** найдите виртуальную сеть, которой необходимо настроить конечную точку службы для Azure Cosmos DB. Перейдите к колонке **Конечная точка службы** и убедитесь, что подсеть виртуальной сети включена для конечной точки службы "Azure.CosmosDB".  

   ![Подтверждение включения конечной точки службы](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

3. Перед включением конечной точки службы виртуальной сети скопируйте информацию брандмауэра IP, связанную с вашей учетной записью Azure Cosmos DB, для использования в будущем. После настройки конечной точки службы можно снова включить брандмауэр IP.  

4. Из меню параметров выберите **Брандмауэры и виртуальные сети** и затем из раздела "Разрешить доступ" выберите пункт **Выбранные сети**.  

3. Чтобы предоставить доступ к подсети существующей виртуальной сети, в разделе "Виртуальные сети" выберите **Добавление существующей виртуальной сети Azure**.  

4. Выберите **Подписка**, из которой требуется добавить виртуальную сеть Azure. Выберите **виртуальную сеть** и **подсети** Azure, которым необходимо предоставить доступ к учетной записи Azure Cosmos DB. Затем выберите **Включить**, чтобы включить выбранные сети с конечными точками службы для "Microsoft.AzureCosmosDB". После завершения работы мастера выберите **Добавить**.  

   ![Выбор виртуальной сети и подсети](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Если конечная точка службы для Azure Cosmos DB не была ранее настроена для выбранных виртуальных сетей и подсетей Azure, ее можно настроить в рамках этой операции. Включение доступа займет до 15 минут. 

   ![Успешная настройка виртуальной сети и подсети](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Теперь ваша учетная запись Azure Cosmos DB допустит только трафик из этой выбранной подсети. Если вы ранее включали брандмауэр IP, перезапустите его, используя более раннюю информацию.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для новой виртуальной сети и подсети Azure

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

2. Перед включением конечной точки службы виртуальной сети скопируйте информацию брандмауэра IP, связанную с вашей учетной записью Azure Cosmos DB, для использования в будущем. После настройки конечной точки службы можно снова включить брандмауэр IP.  

3. Из меню параметров выберите **Брандмауэры и виртуальные сети** и затем из раздела "Разрешить доступ" выберите пункт **Выбранные сети**.  

4. Чтобы предоставить доступ к новой виртуальной сети Azure, в разделе "Виртуальные сети" выберите **Добавить новую виртуальную сеть**.  

5. Укажите сведения, необходимые для создания новой виртуальной сети, а затем выберите "Создать". Подсеть будет создана с конечной точкой службы для включенной "Microsoft.AzureCosmosDB".

   ![Выбор виртуальной сети и подсети для новой виртуальной сети](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Разрешение доступа с портала Azure

После включения конечных точек службы виртуальной сети Azure для вашей учетной записи базы данных Azure Cosmos DB доступ с портала или из других служб Azure отключен по умолчанию. Доступ к учетной записи базы данных Azure Cosmos DB с компьютеров за пределами настроенной подсети блокируется, включая доступ с портала.

![Разрешение доступа с портала](./media/vnet-service-endpoint/allow-access-from-portal.png)

Если ваша учетная запись Azure Cosmos DB используется другими службами Azure, такими как поиск Azure или доступная из Stream Analytics или Power BI, можно разрешить доступ, включив параметр **Разрешение доступа к службам Azure**.

Чтобы иметь доступ к метрикам Azure Cosmos DB с портала, необходимо включить параметр **Разрешение доступа к порталу Azure**. Дополнительные сведения об этих параметрах см. в разделах [Подключения из портала Azure](firewall-support.md#connections-from-the-azure-portal) и [Подключения из службы PaaS Azure](firewall-support.md#connections-from-other-azure-paas-services). После выбора доступа выберите **Сохранить** для сохранения настроек.

## <a name="remove-a-virtual-network-or-subnet"></a>Удаление виртуальной сети или подсети 

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, для которой назначены конечные точки службы.  

2. Выберите **Брандмауэры и виртуальные сети** из меню параметров.  

3. Чтобы удалить правило подсети или виртуальной сети, выберите "..." рядом с виртуальной сетью или подсетью и нажмите **Удалить**.

   ![Удаление виртуальной сети](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Щелкните **Сохранить**, чтобы применить изменения.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Настройка конечной точки службы с помощью Azure PowerShell 

Для настройки конечной точки службы для учетной записи Azure Cosmos DB с помощью Azure PowerShell выполните следующие действия:  

1. Установите последнюю версию [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Перед включением конечной точки службы для учетной записи убедитесь, что вы полностью записали настройки брандмауэра IP и полностью удалили его.

2. Перед включением конечной точки службы виртуальной сети скопируйте информацию брандмауэра IP, связанную с вашей учетной записью Azure Cosmos DB, для использования в будущем. После настройки конечной точки службы можно снова включить брандмауэр IP.  

3. Включите конечную точку службы для существующей подсети виртуальной сети.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. Подготовьтесь к операции включения ACL в учетной записи CosmosDB, убедившись в наличии конечной точки службы виртуальной сети и подсети для Azure Cosmos DB.

   ```powershell
   $subnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName  | Get-AzureRmVirtualNetworkSubnetConfig -Name $sname
   $vnProp = Get-AzureRmVirtualNetwork `-Name $vnName  -ResourceGroupName $rgName
   ```

5. Получите свойства учетной записи Azure Cosmos DB, выполнив следующий командлет:  

   ```powershell
   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Инициализируйте переменные для использования позже. Настройте все переменные из существующего определения учетной записи. Если у вас есть несколько расположений, необходимо добавить их как часть массива. На этом этапе также настройте конечную точку службы виртуальной сети, установив переменной "accountVNETFilterEnabled" значение "True". Это значение позже будет назначено параметру "isVirtualNetworkFilterEnabled".  

   ```powershell
   $locations = @(@{})
   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $subnetName  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Обновите свойства учетной записи Azure Cosmos DB с новой конфигурацией, выполнив следующие командлеты: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource ``
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Выполните следующую команду, чтобы убедиться, что ваша учетная запись Azure Cosmos DB обновлена с конечной точкой службы виртуальной сети, настроенной на предыдущем этапе:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Добавление конечной точки службы виртуальной сети для учетной записи Azure Cosmos DB с включенным брандмауэром IP

1. Сначала отключите доступ брандмауэра IP к учетной записи Azure Cosmos DB.  

2. Подключите конечную точку виртуальной сети к учетной записи сети Azure Cosmos DB с помощью одного из методов, описанных в предыдущих разделах.  

3. Повторно включите доступ к брандмауэру IP. 

## <a name="test-the-access"></a>Проверка доступа

Чтобы проверить, настроены ли конечные точки службы для Azure Cosmos DB должным образом, выполните следующие действия:

* Настройте две подсети в виртуальной сети как интерфейсную и серверную части, включите конечную точку службы Cosmos DB для подсети серверной части.  

* Включите доступ к учетной записи Cosmos DB для подсети серверной части.  

* Создайте две виртуальные машины: одну в подсети серверной части и другую в подсети интерфейсной части.  

* Попробуйте получить доступ к учетной записи Azure Cosmos DB с обеих виртуальных машин. Необходимо иметь возможность подключаться с виртуальной машины, созданной в подсети серверной части, но не с созданной в интерфейсной подсети. При попытке подключения из подсети интерфейсной части запрос завершится с ошибкой 404, которая подтверждает, что доступ к базе данных Azure Cosmos обеспечивается с помощью конечной точки службы виртуальной сети. Машина в подсети серверной части будет нормально работать.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Что происходит при получении доступа к учетной записи Azure Cosmos DB с включенным списком контроля доступа (ACL) к виртуальной сети?  

Ошибка HTTP 404.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Разрешен ли доступ для подсетей виртуальной сети, созданных в различных регионах, к учетной записи Azure Cosmos DB в другом регионе? Например, если учетная запись Azure Cosmos DB находится в западной части США или в восточной части США, а виртуальная сеть находится в нескольких регионах, может ли виртуальная сеть получить доступ к базе данных Azure Cosmos DB?  

Да, виртуальные сети, созданные в разных регионах, могут получить доступ к новым возможностям. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Может ли учетная запись Azure Cosmos DB иметь как конечную точку службы виртуальной сети, так и брандмауэр?  

Да, конечная точка службы виртуальной сети и брандмауэр могут сосуществовать. В общем случае следует убедиться, что перед настройкой конечной точки службы виртуальной сети доступ к порталу всегда включен, чтобы иметь возможность просматривать метрики, связанные с контейнером.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Могу ли я "разрешить доступ к другим службам Azure из заданного региона Azure", когда доступ к конечным точкам службы включен для базы данных Azure Cosmos?  

Это необходимо, только если требуется, чтобы к учетной записи Azure Cosmos DB могли получать доступ другие службы Azure, такие как фабрика данных Azure, поиск Azure или любые службы, развернутые в данном регионе Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Сколько конечных точек службы виртуальной сети разрешено для Azure Cosmos DB?  

Для учетной записи Azure Cosmos DB допускаются 64 конечных точки службы виртуальной сети.

### <a name="what-is-the-relationship-of-service-endpoint-with-respect-to-network-security-group-nsg-rules"></a>Какова взаимосвязь конечной точки службы с правилами группы безопасности сети (NSG)?  

Правило NSG от Azure Cosmos позволяет ограничивать доступ только к диапазону IP-адресов Azure Cosmos DB.
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Какова связь между брандмауэром IP и возможностями конечной точки службы виртуальной сети?  

Эти две функции дополняют друг друга, чтобы обеспечить изоляцию активов Azure Cosmos DB и обеспечить их безопасность. Использование брандмауэра IP гарантирует, что статические IP-адреса могут получать доступ к учетной записи Azure Cosmos DB.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Может ли IP-адрес локального устройства, подключенный через шлюз виртуальной сети Azure (VPN) или шлюз Экспресс-маршрута, получить доступ к учетной записи Azure Cosmos DB?  

IP-адрес или диапазон IP-адресов локального устройства должны быть добавлены в список статических IP-адресов для доступа к учетной записи Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Что происходит при удалении виртуальной сети, которая имеет настройки конечной точки службы для Azure Cosmos DB?  

При удалении виртуальной сети информация ACL, связанная с Azure Cosmos DB, удаляется и удаляет взаимодействие между виртуальной сетью и учетной записью Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Что произойдет, если учетная запись Azure Cosmos DB с включенной конечной точкой службы виртуальной сети удалена?

Метаданные, связанные с определенной учетной записью Azure Cosmos DB, удаляются из подсети. И ответственность конечного пользователя заключается в удалении подсети и используемой виртуальной сети.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Могу ли я использовать одноранговую виртуальную сеть для создания конечной точки службы для Azure Cosmos DB?  

Нет, только прямая виртуальная сеть и ее подсети могут создавать конечные точки службы Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Что происходит с исходным IP-адресом ресурса, например виртуальной машины, в подсети с включенной конечной точкой службы Azure Cosmos DB?

При включении конечных точек службы виртуальной сети исходные IP-адреса ресурсов в подсети виртуальной сети будут переключаться с использования общедоступных адресов IPV4 на частные адреса виртуальной сети Azure для трафика к Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Может ли Azure Cosmos DB находится в виртуальной сети Azure, предоставленной клиентом?  

Azure Cosmos DB — это мультитенантная служба с общедоступным IP-адресом. При ограничении доступа к подсети виртуальной сети Azure с помощью функции конечной точки службы доступ ограничен для учетной записи Azure Cosmos DB через заданную виртуальную сеть и подсеть Azure.  Учетная запись Azure Cosmos DB не находится в этой виртуальной сети Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Что делать, если что-либо будет регистрироваться в Log Analytics или OMS, если она включена?  

Azure Cosmos DB будет отправлять журналы с IP-адресом (без последнего октета) со статусом 403 для запроса, заблокированного ACL.  

## <a name="next-steps"></a>Дополнительная информация
Чтобы настроить брандмауэр для Azure Cosmos DB, см. статью [Поддерживаемые брандмауэры](firewall-support.md).

