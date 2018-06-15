---
title: Создание масштабируемого набора виртуальных машин с помощью Azure CLI 2.0 | Документация Майкрософт
description: Узнайте, как быстро создать масштабируемый набор виртуальных машин с помощью Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 8794ea7d998293e7ea88ba780f67ef8a021f2298
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201110"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Создание масштабируемого набора виртуальных машин с помощью Azure CLI 2.0
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Вы можете вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. В этой статье по началу работы вы создадите масштабируемый набор виртуальных машин с помощью Azure CLI 2.0. Вы также можете создать масштабируемый набор с помощью [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) или [портала Azure](virtual-machine-scale-sets-create-portal.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Прежде чем создать масштабируемый набор, выполните команду [az group create](/cli/azure/group#az_group_create) для создания группы ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss#az_vmss_create). В следующем примере создаются масштабируемый набор *myScaleSet* и ключи SSH, если они еще не созданы.

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="install-nginx-webserver"></a>Установка веб-сервера NGINX
Для проверки масштабируемого набора используйте расширение пользовательского скрипта, чтобы скачать и запустить скрипт, который позволяет установить NGINX на экземплярах виртуальных машин. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Дополнительные сведения см. в статье [Расширение Custom Script в ОС Windows](../virtual-machines/linux/extensions-customscript.md).

Примените расширение пользовательского скрипта, которое устанавливает NGINX, как показано ниже.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Разрешение веб-трафика
Чтобы разрешить передачу трафика в веб-сервер, создайте правило подсистемы балансировки нагрузки с помощью команды [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). В следующем примере создается правило *myLoadBalancerRuleWeb*.

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Тестирование веб-сервера
Чтобы посмотреть, как работает веб-сервер, получите общедоступный IP-адрес подсистемы балансировки нагрузки с помощью команды [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Следующий пример получает IP-адрес для *myScaleSetLBPublicIP*, созданного ранее вместе с масштабируемым набором.

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Введите в браузере общедоступный IP-адрес подсистемы балансировки нагрузки. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Веб-страница NGINX по умолчанию](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ненужную группу ресурсов, масштабируемый набор и все связанные ресурсы с помощью команды [az group delete](/cli/azure/group#az_group_delete), как показано ниже.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Дополнительная информация
В этой статье по началу работы вы создали простой масштабируемый набор и использовали расширение пользовательского скрипта, чтобы установить базовый веб-сервер NGINX на экземплярах виртуальных машин. Чтобы улучшить масштабируемость и автоматизацию, расширьте возможности масштабируемого набора, следуя инструкциям в следующих статьях:

- [Развертывание приложения в масштабируемых наборах виртуальных машин](virtual-machine-scale-sets-deploy-app.md)
- Автоматическое масштабирование с помощью [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) или [портала Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Автоматические обновления ОС масштабируемого набора виртуальных машин Azure](virtual-machine-scale-sets-automatic-upgrade.md)