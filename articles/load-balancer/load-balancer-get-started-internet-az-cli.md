---
title: "Создание общедоступного балансировщика нагрузки уровня \"Стандартный\" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью Azure CLI | Документы Майкрософт"
description: "Сведения о создании общедоступного балансировщика нагрузки уровня \"Стандартный\" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью Azure CLI"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 180cf891611f148a1b9acd112fbcddab8eb54de3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Создание общедоступного балансировщика нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным сервером с помощью Azure CLI

В этой статье приведены действия по созданию общедоступной [подсистемы балансировки нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным в пределах зоны интерфейсным сервером с помощью общедоступного стандартного IP-адреса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Регистрация для использования зон доступности, подсистемы балансировки нагрузки уровня "Стандартный" и общедоступного стандартного IP-адреса (предварительная версия)

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.17 или более поздней версии.  Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
[Номер SKU подсистемы балансировки нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) сейчас находится в предварительной версии. В период действия предварительной версии эта функция может не отличаться таким же уровнем доступности и надежности, как функции, предоставляемые в общедоступной версии. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Общедоступный номер SKU подсистемы балансировки нагрузки уровня "Базовый"](load-balancer-overview.md) можно использовать для производственных служб. 

> [!NOTE]
> Зоны доступности используются в режиме предварительной версии в сценариях разработки и тестирования. Поддержка реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Перед выбором зоны или избыточного в пределах зоны параметра интерфейсного общедоступного IP-адреса для подсистемы балансировки нагрузки необходимо выполнить действия, описанные в статье [Общие сведения о зонах доступности в Azure (предварительная версия)](https://docs.microsoft.com/azure/availability-zones/az-overview).

Обязательно установите последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и войдите в учетную запись Azure с помощью команды [az login](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#az_login).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Создание общедоступного стандартного IP-адреса

Создайте общедоступный стандартный IP-адрес с помощью следующей команды:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки

Создайте общедоступный балансировщик нагрузки уровня "Стандартный" с созданным на предыдущем шаге общедоступным стандартным IP-адресом с помощью следующей команды:

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Создание зонда балансировщика нагрузки на порту 80

Создайте зонд работоспособности балансировщика нагрузки с помощью следующей команды:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Создание правила балансировщика нагрузки для порта 80

Создайте правило балансировщика нагрузки с помощью следующей команды:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Дополнительная информация
- Узнайте о [создании общедоступного IP-адреса в зоне доступности](../virtual-network/create-public-ip-availability-zone-cli.md).



