---
title: Создание и изменение канала ExpressRoute Azure с помощью CLI | Документация Майкрософт
description: В этой статье описывается создание, подготовка, проверка, обновление, удаление и отзыв канала ExpressRoute с помощью CLI.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23479613"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Создание и изменение канала ExpressRoute с помощью CLI


В этой статье описывается, как создать канал ExpressRoute Azure с помощью интерфейса командной строки (CLI), а также показано, как проверить состояние, обновить или удалить и отозвать канал. Если вы хотите использовать другой метод для работы с каналами ExpressRoute, выберите подходящую статью из списка ниже:

> [!div class="op_single_selector"]
> * [Портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [интерфейс командной строки Azure](howto-circuit-cli.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (классическая модель)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Перед началом работы

* Перед началом работы установите последнюю версию команд интерфейса командной строки (версию 2.0 или более позднюю). Сведения об установке команд интерфейса командной строки см. в статьях [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Установка Azure CLI 2.0) и [Get started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Приступая к работе с Azure CLI 2.0).
* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## <a name="create"></a>Создание и подготовка канала ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Войдите в учетную запись Azure и выберите подписку.

Чтобы начать настройку, войдите в свою учетную запись Azure. Для подключения используйте следующие примеры:

```azurecli
az login
```

Просмотрите подписки учетной записи.

```azurecli
az account list
```

Выберите подписку, для которой требуется создать канал ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Получение списка поддерживаемых поставщиков, расположений и значений пропускной способности

Перед созданием канала ExpressRoute потребуется список поддерживаемых поставщиков услуг подключения, расположений и вариантов пропускной способности. Команда CLI az network express-route list-service-providers возвращает сведения, которые вы будете использовать на следующих этапах:

```azurecli
az network express-route list-service-providers
```

Ответ будет выглядеть примерно так:

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Проверьте, указан ли в ответе ваш поставщик услуг подключения. Запишите следующие сведения, которые потребуются при создании канала:

* ИМЯ
* PeeringLocations
* BandwidthsOffered

Теперь все готово к созданию канала ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Создание канала ExpressRoute

> [!IMPORTANT]
> Выставление счетов за использование ExpressRoute начинается после получения ключа службы. Выполните эту операцию, когда поставщик услуг подключения будет готов предоставить канал.
> 
> 

Перед созданием канала ExpressRoute необходимо создать группу ресурсов (если вы этого еще не сделали) Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другой поставщик и другие параметры, подставьте в запрос соответствующие данные. 

Убедитесь, что указаны правильный уровень SKU и семейство SKU:

* Уровень SKU определяет, какая надстройка включена — ExpressRoute Standard или ExpressRoute Premium. Укажите Standard, чтобы получить SKU "Стандартный", или Premium, чтобы получить надстройку "Премиум".
* Семейство SKU определяет тип выставления счетов. Выберите Metereddata для тарифного плана с оплатой за трафик или Unlimiteddata для безлимитного тарифного плана. Тип выставления счетов можно изменить с Metereddata на Unlimiteddata, но не наоборот.


Выставление счетов за использование ExpressRoute начинается после получения ключа службы. Ниже приведен пример запроса нового ключа службы:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Ответ будет содержать ключ службы.

### <a name="4-list-all-expressroute-circuits"></a>4. Получение списка всех каналов ExpressRoute

Чтобы получить список всех созданных вами каналов ExpressRoute, выполните команду az network express-route list. Вы можете получить эти сведения в любое время с помощью этой команды. Чтобы получить список всех каналов, сделайте вызов без параметров.

```azurecli
az network express-route list
```

Ваш ключ службы внесен в поле *ServiceKey* ответа.

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Подробное описание всех параметров можно получить, выполнив команду с параметром -h.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Отправка ключа службы поставщику услуг подключения для подготовки

Параметр ServiceProviderProvisioningState предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Параметр Status предоставляет состояние на стороне Майкрософт. Дополнительные сведения см. в разделе [Состояния подготовки канала ExpressRoute](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет имеет следующее состояние:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Когда поставщик услуг подключения находится в процессе его включения, канал переходит в следующее состояние:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Периодическая проверка состояния и статуса ключа канала

Проверка состояния и состояния ключа канала позволит вам узнать, когда поставщик активирует ваш канал. После настройки канала значение параметра ServiceProviderProvisioningState изменится на Provisioned, как показано в примере ниже:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Ответ будет выглядеть примерно так:

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Создание конфигурации маршрутизации

Пошаговые инструкции по созданию и изменению пиринга каналов см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](howto-routing-cli.md).

> [!IMPORTANT]
> Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если ваш поставщик услуг подключения предлагает услуги третьего уровня (обычно это IPVPN, например MPLS), то он возьмет на себя настройку маршрутизации и управление ею.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Связывание виртуальной сети с каналом ExpressRoute

Теперь свяжите виртуальную сеть с каналом ExpressRoute. Дополнительные сведения см. в статье [Connect a virtual network to an ExpressRoute circuit using CLI](howto-linkvnet-cli.md) (Подключение виртуальной сети к каналу ExpressRoute с помощью CLI).

## <a name="modify"></a>Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение. Можно вносить следующие изменения без простоя:

* Включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute.
* Увеличивать пропускную способность канала ExpressRoute при условии, что в порту имеется доступная емкость. Однако снижение уровня пропускной способности канала не поддерживается. 
* Перейти с тарифного плана с оплатой за трафик на безлимитный тарифный план. Однако переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается.
* Параметр *Allow Classic Operations*(Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Включение надстройки ExpressRoute "Премиум"

Вы можете включить надстройку ExpressRoute "Премиум" для имеющегося канала ExpressRoute с помощью следующей команды:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Теперь для канала включены функции надстройки ExpressRoute "Премиум". С момента успешного выполнения команды мы начнем выставлять счета по тарифам надстройки уровня "Премиум".

### <a name="to-disable-the-expressroute-premium-add-on"></a>Отключение надстройки ExpressRoute "Премиум"

> [!IMPORTANT]
> Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для канала "Стандартный".
> 
> 

Перед отключением надстройки ExpressRoute "Премиум" ознакомьтесь со следующими требованиями:

* Перед переходом с надстройки "Премиум" на "Стандартный", убедитесь, что к каналу привязано меньше 10 виртуальных сетей. Если у вас их больше 10, запрос на обновление завершится ошибкой и мы будем выставлять вам счета по тарифам ценовой категории "Премиум".
* Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если вы не отсоедините все ваши виртуальные сети, запрос обновления завершится ошибкой и мы будем выставлять вам счета по тарифам ценовой категории "Премиум".
* Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если таблица маршрутов содержит больше 4000 маршрутов, сеанс BGP будет сброшен. Его можно будет снова включить только после того, как количество объявленных префиксов станет меньше 4000.

Вы можете отключить надстройку ExpressRoute "Премиум" для имеющегося канала с помощью следующей команды:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Обновление пропускной способности канала ExpressRoute

Параметры пропускной способности, поддерживаемые для вашего поставщика, приведены в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала.

> [!IMPORTANT]
> Может потребоваться заново создать канал ExpressRoute, если существующий порт не обеспечивает достаточную емкость. Канал невозможно обновить, если в его расположении нет доступной дополнительной емкости.
>
> Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.
>

Выберите необходимый размер и используйте следующую команду для изменения размера канала:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Размер канала изменяется со стороны Майкрософт. Затем свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Когда вы сообщите поставщику услуг об изменении размера канала, мы будем выставлять счета за обновленную пропускную способность.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Перевод SKU с измеряемых на неограниченные данные

Изменить SKU канала ExpressRoute можно, используя следующий пример:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Управление доступом к классической среде и среде диспетчера ресурсов

Инструкции см. в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Отзыв и удаление канала ExpressRoute

Чтобы отменить подготовку и удалить канал ExpressRoute, ознакомьтесь со следующими требованиями:

* Связь между ExpressRoute и всеми виртуальными сетями необходимо разорвать. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Канал можно удалить, если поставщик услуг отозвал его. При отозванном канале состояние подготовки поставщика услуг получает значение **Не подготовлено**. Это приостанавливает выставление счетов для канала.

Для удаления канала ExpressRoute выполните следующую команду:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

После создания канала обязательно выполните задачи, описанные в следующих статьях:

* [Создание и изменение маршрутизации для канала ExpressRoute](howto-routing-cli.md)
* [Связывание виртуальной сети с каналом ExpressRoute](howto-linkvnet-cli.md)