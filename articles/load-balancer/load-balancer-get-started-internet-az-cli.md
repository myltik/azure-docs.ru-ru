---
title: Создание общедоступного балансировщика нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью Azure CLI | Документы Майкрософт
description: Сведения о создании общедоступного балансировщика нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: f3f479de8bc3975f4da07a7761ffc99f976db20e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Создание общедоступного балансировщика нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным сервером с помощью Azure CLI

В этой статье приведены действия по созданию общедоступной [подсистемы балансировки нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным в пределах зоны интерфейсным сервером с помощью общедоступного стандартного IP-адреса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы планируете установить CLI и использовать его локально, обязательно установите последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и войдите в учетную запись Azure с помощью команды [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Создание общедоступного стандартного IP-адреса

Создайте общедоступный стандартный IP-адрес с помощью следующей команды:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки

Создайте общедоступный балансировщик нагрузки уровня "Стандартный" с созданным на предыдущем шаге общедоступным стандартным IP-адресом с помощью следующей команды:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Создание зонда балансировщика нагрузки на порту 80

Создайте зонд работоспособности балансировщика нагрузки с помощью следующей команды:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Создание правила балансировщика нагрузки для порта 80

Создайте правило балансировщика нагрузки с помощью следующей команды:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).



