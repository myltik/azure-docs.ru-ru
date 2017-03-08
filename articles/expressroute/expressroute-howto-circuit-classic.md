---
title: "Создание и изменение канала ExpressRoute с помощью PowerShell и классического портала Azure | Документация Майкрософт"
description: "В этой статье описана процедура создания и подготовки канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить и отозвать канал."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: fe0bff84a5316628d9e465da0d4e62162f1ea4f2
ms.openlocfilehash: cb67631dbbfb53a0de9b07bc3918bd70751ec41b
ms.lasthandoff: 02/10/2017


---
# <a name="create-and-modify-an-expressroute-circuit"></a>Создание и изменение канала ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-circuit-arm.md)
> * [Классическая модель: PowerShell](expressroute-howto-circuit-classic.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

В этой статье пошагово описано, как создать канал Azure ExpressRoute, используя командлеты PowerShell и классическую модель развертывания. В этой статье описывается, как проверить состояние, обновить или удалить и отозвать канал ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Перед началом работы
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Шаг 1. Ознакомьтесь со статьями о предварительных условиях и процедурах
Не забудьте изучить [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.  

### <a name="step-2-install-the-latest-versions-of-the-azure-powershell-modules"></a>Шаг 2. Установите последние версии модулей Azure PowerShell
Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Шаг 3. Войдите в учетную запись Azure и выберите подписку
1. В командной строке Windows PowerShell с повышенными привилегиями выполните следующий командлет.
   
        Add-AzureAccount
2. В появившемся окне входа выполните вход в свою учетную запись.
3. Получите список своих подписок.
   
        Get-AzureSubscription
4. Выберите подписку, которую нужно использовать.
   
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Создание и предоставление канала ExpressRoute
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Шаг 1. Импорт модулей PowerShell для ExpressRoute
 Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell (если вы еще не сделали это). Импортируйте модули из расположения, в которое они были установлены на локальном компьютере. В зависимости от метода установки модулей их расположение может отличаться от показанного в следующем примере. При необходимости измените пример.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Шаг 2. Получение списка поддерживаемых поставщиков, расположений и значений пропускной способности
Перед созданием канала ExpressRoute потребуется список поддерживаемых поставщиков услуг подключения, расположений и вариантов пропускной способности.

Командлет PowerShell `Get-AzureDedicatedCircuitServiceProvider` возвращает эти сведения, которые будут использоваться в последующих шагах.

    Get-AzureDedicatedCircuitServiceProvider

Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующие данные, так как они потребуются позже, при создании канала.

* Имя
* PeeringLocations
* BandwidthsOffered

Теперь все готово к созданию канала ExpressRoute.         

### <a name="step-3-create-an-expressroute-circuit"></a>Шаг 3. Создание канала ExpressRoute
В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другой поставщик и другие параметры, подставьте в запрос соответствующие данные.

> [!IMPORTANT]
> Выставление счетов за использование ExpressRoute начинается после того, как клиент получает служебный ключ. Обязательно выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.
> 
> 

Пример запроса нового ключа службы:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Если же вы хотите создать канал ExpressRoute с надстройкой "Премиум", воспользуйтесь приведенным ниже примером. Дополнительные сведения о надстройке Premium см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Шаг 4. Вывод списка всех каналов ExpressRoute
Чтобы получить список всех созданных вами каналов ExpressRoute, можно выполнить команду `Get-AzureDedicatedCircuit`.

    Get-AzureDedicatedCircuit

Ответ будет выглядеть примерно так:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureDedicatedCircuit` . Если этот командлет вызывается без параметров, выводится список всех каналов. Ваш ключ службы будет приведен в поле *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Подробное описание всех параметров можно получить, выполнив следующую команду:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Шаг 5. Отправка ключа службы поставщику услуг подключения для подготовки
Параметр *ServiceProviderProvisioningState* предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Параметр *Status* предоставляет состояние на стороне инфраструктуры Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в статье [Рабочие процессы](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет иметь следующее состояние:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Когда поставщик услуг подключения находится в процессе его включения, канал переходит в следующее состояние.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Шаг 6. Периодическая проверка состояния и статуса ключа канала
Это позволяет узнать, когда поставщик включил ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Provisioned*, как показано в следующем примере.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Шаг 7. Создание конфигурации маршрутизации
Пошаговые инструкции см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-classic.md).

> [!IMPORTANT]
> Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно это IPVPN, например MPLS), то ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Шаг 8. Связывание виртуальной сети с каналом ExpressRoute
Теперь свяжите виртуальную сеть с каналом ExpressRoute. Пошаговые инструкции см. в статье [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md). Инструкции по созданию виртуальной сети для ExpressRoute с помощью классической модели развертывания см. в статье [Настройка виртуальной сети для ExpressRoute на классическом портале](expressroute-howto-vnet-portal-classic.md).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Получение состояния канала ExpressRoute
Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureCircuit` . Если этот командлет вызывается без параметров, выводится список всех каналов.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Для получения сведений об определенном канале ExpressRoute передайте ключ службы как параметр вызова.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Подробное описание всех параметров можно получить, выполнив следующий пример:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Изменение канала ExpressRoute
Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие действия без простоя:

* включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
* увеличивать пропускную способность канала ExpressRoute. Обратите внимание, что снижение пропускной способности канала не поддерживается.
* Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Обратите внимание, что переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается.
* Параметр *Allow Classic Operations*(Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Включение надстройки ExpressRoute "Премиум"
Вы можете включить надстройку ExpressRoute "Премиум" для существующего канала ExpressRoute с помощью следующего командлета PowerShell.

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Теперь для вашего канала включена надстройка ExpressRoute Premium. Обратите внимание, что с момента успешного выполнения команды мы начнем тарифицировать надстройку Premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Отключение надстройки ExpressRoute "Премиум"
> [!IMPORTANT]
> Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для канала "Стандартный".
> 
> 

#### <a name="considerations"></a>Рекомендации

* Прежде чем переходить с надстройки Premium на Standard, убедитесь, что к каналу привязано меньше 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам "Премиум".
* Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам "Премиум".
* Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если в ней больше 4000 маршрутов, сеанс BGP будет удален. Его можно будет снова включить только после того, как количество объявленных префиксов станет меньше 4000.

#### <a name="disable-the-premium-add-on"></a>Отключение надстройки premium
Вы можете выключить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего командлета PowerShell.

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Обновление пропускной способности канала ExpressRoute
Варианты пропускной способности, поддерживаемые для вашего поставщика, приведены в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md) . Можно выбрать любой размер, превышающий размер существующего канала, при условии, что он не превышает возможности физического порта (на котором создается канал).

> [!IMPORTANT]
> Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Изменение размера канала

Решив, какой размер вам необходим, вы можете использовать следующую команду для изменения размера канала.

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Размер вашего канала будет изменен со стороны Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Обратите внимание, что с этого момента мы начнем тарифицировать обновленный параметр пропускной способности.

Если при увеличении пропускной способности канала вы видите приведенную ошибку, это означает, что в физическом порту, на котором создан ваш существующий канал, не осталось достаточно пропускной способности. Необходимо удалить этот канал и создать новый канал нужного размера. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Отзыв и удаление канала ExpressRoute

### <a name="considerations"></a>Рекомендации

* Для успешного выполнения этой операции необходимо разорвать связи между каналом ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Если поставщик услуг отзовет канал (состояние подготовки поставщика услуг изменится на **Не подготовлено**), то вы можете удалить канал. В результате будет приостановлено выставление счетов за этот канал.

#### <a name="delete-a-circuit"></a>Удаление канала

Для удаления канала ExpressRoute выполните следующую команду:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Дальнейшие действия
После создания канала обязательно выполните действия, описанные в следующих статьях:

* [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-classic.md)
* [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md)


