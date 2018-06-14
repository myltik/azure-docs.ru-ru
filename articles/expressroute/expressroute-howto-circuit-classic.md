---
title: 'Изменение канала ExpressRoute с помощью PowerShell: классическая модель Azure | Документация Майкрософт'
description: В этой статье описывается, как выполнить проверку состояния, обновление, удаление или отмену подготовки канала классической модели развертывания ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "24102840"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Изменение канала ExpressRoute с помощью PowerShell (классическая модель)

> [!div class="op_single_selector"]
> * [Портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [интерфейс командной строки Azure](howto-circuit-cli.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (классическая модель)](expressroute-howto-circuit-classic.md)
>

В этой статье описывается, как проверить состояние, обновить или удалить и отозвать канал ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Перед началом работы

Установите последние версии модулей PowerShell для управления службами Azure (SM). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview).

## <a name="get-the-status-of-a-circuit"></a>Получение состояния канала

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureCircuit` . Если этот командлет вызывается без параметров, выводится список всех каналов.

```powershell
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
```

Для получения сведений об определенном канале ExpressRoute передайте ключ службы как параметр вызова.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Подробное описание всех параметров можно получить, выполнив следующий пример:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Изменение канала

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие задачи без простоя:

* включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
* увеличивать пропускную способность канала ExpressRoute при условии, что в порту имеется доступная емкость. Снижение уровня пропускной способности канала не поддерживается. 
* Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается.
* Параметр *Allow Classic Operations*(Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### <a name="enable-the-expressroute-premium-add-on"></a>Включение надстройки ExpressRoute уровня "Премиум"

Вы можете включить надстройку ExpressRoute "Премиум" для существующего канала ExpressRoute с помощью следующего командлета PowerShell.

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Теперь для вашего канала включена надстройка ExpressRoute Premium. После успешного выполнения команды включается тарификация за использование надстройки уровня "Премиум".

### <a name="disable-the-expressroute-premium-add-on"></a>Отключение надстройки ExpressRoute уровня "Премиум"

> [!IMPORTANT]
> Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для канала "Стандартный".
> 
> 

#### <a name="considerations"></a>Рекомендации

* Прежде чем менять уровень надстройки с "Премиум" на "Стандартный", убедитесь, что с каналом связаны меньше 10 виртуальных сетей. В противном случае запрос на обновление завершится ошибкой, а плата будет начисляться по ценам для уровня "Премиум".
* Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. В противном случае запрос на обновление завершится ошибкой, а плата будет начисляться по ценам для уровня "Премиум".
* Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если в ней больше 4000 маршрутов, сеанс BGP будет сброшен. Его можно будет снова активировать только после того, как количество объявленных префиксов станет меньше 4000.

#### <a name="to-disable-the-premium-add-on"></a>Отключение надстройки уровня "Премиум"

Вы можете выключить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего командлета PowerShell.

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Обновление пропускной способности канала ExpressRoute

Варианты пропускной способности, поддерживаемые для вашего поставщика, приведены в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md) . Можно выбрать любой размер, превышающий размер существующего канала, при условии, что он не превышает возможности физического порта (на котором создается канал).

> [!IMPORTANT]
> Может потребоваться заново создать канал ExpressRoute, если существующий порт не обеспечивает достаточную емкость. Канал невозможно обновить, если в его расположении нет доступной дополнительной емкости.
>
> Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Изменение размера канала

Решив, какой размер вам необходим, вы можете использовать следующую команду для изменения размера канала.

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Когда размер вашего канала будет увеличен на стороне Майкрософт, вам нужно связаться с поставщиком услуг подключения и попросить его обновить конфигурации в соответствии с этим изменением. С этого момента включится тарификация для обновленного параметра пропускной способности.

Если при увеличении пропускной способности канала вы видите приведенную ошибку, это означает, что в физическом порту, на котором создан ваш существующий канал, не осталось достаточно пропускной способности. Необходимо удалить этот канал и создать новый канал нужного размера.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Отзыв и удаление канала

### <a name="considerations"></a>Рекомендации

* Для успешного выполнения этой операции необходимо разорвать связи между каналом ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Если поставщик услуг отзовет канал (состояние подготовки поставщика услуг изменится на **Не подготовлено**), вы можете удалить канал. Это приостанавливает выставление счетов для канала.

#### <a name="delete-a-circuit"></a>Удаление канала

Для удаления канала ExpressRoute выполните следующую команду:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```