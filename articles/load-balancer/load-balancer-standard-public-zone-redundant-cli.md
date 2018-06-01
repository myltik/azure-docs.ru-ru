---
title: Балансировка нагрузки избыточных в пределах зоны виртуальных машин с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать общедоступный Load Balancer уровня "Стандартный" с избыточным зональным внешним интерфейсом с помощью Azure CLI
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
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: e469311609909e3453015702fca7d015a4e72398
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34273972"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Балансировка нагрузки виртуальных машин по всем зонам доступности с помощью Azure CLI

В этой статье приведены пошаговые инструкции по созданию общедоступного [Load Balancer уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным в пределах зоны внешним интерфейсом, чтобы достичь избыточности в пределах зоны без зависимости от нескольких записей DNS. Один интерфейсный IP-адрес по умолчанию является избыточным в пределах зоны.  Используйте избыточный в пределах зоны внешний интерфейс для распределения нагрузки, с помощью одного IP-адреса можно связаться с любой виртуальной машиной в рамках региона во всех зонах доступности. Зоны доступности позволяют защитить приложения и данные от маловероятных сбоев и потери всего центра обработки данных.

Дополнительные сведения о том, как работают зоны доступности с Load Balancer уровня "Стандартный", см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.17 или более поздней версии.  Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroupSLB* в расположении *westeurope*.

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Создание избыточного в пределах зоны общедоступного стандартного IP-адреса
Для доступа к приложению через Интернет требуется общедоступный IP-адрес для балансировщика нагрузки. Внешний интерфейс с избыточностью в пределах зоны обслуживается всеми зонами доступности в регионе одновременно. Создайте избыточный в пределах зоны общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). При создании общедоступного стандартного IP-адреса он по умолчанию является избыточным в пределах зоны.

В следующем примере создается избыточный в пределах зоны общедоступный IP-адрес *myPublicIP* в группе ресурсов *myResourceGroupLoadBalancer*.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Создание Azure Load Balancer уровня "Стандартный"
В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:
- интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
- внутренний пул IP-адресов, на который внешний пул отправляет трафик с балансировкой нагрузки;
- зонд работоспособности, определяющий работоспособность серверных экземпляров виртуальной машины;
- правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer"></a>Создание подсистемы балансировки нагрузки
Создайте подсистему балансировки нагрузки уровня "Стандартный" с помощью команды [az network lb create](/cli/azure/network/lb#az_network_lb_create). В следующем примере создается подсистема балансировки нагрузки *myLoadBalancer*, а адрес *myPublicIP* назначается внешней IP-конфигурации.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Создание проверки работоспособности на порте 80

Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте проверку работоспособности с помощью команды az network lb probe create, чтобы отслеживать работоспособность виртуальных машин. Чтобы создать пробу работоспособности TCP, используйте команду [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). В следующем примере создается проба TCP *myHealthProbe*.

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Создание правила подсистемы балансировки нагрузки для порта 80
Правило подсистемы балансировки нагрузки определяет интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул IP-адресов для приема трафика, а также порты источника и назначения. С помощью команды [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) создайте правило подсистемы балансировки нагрузки с именем *myLoadBalancerRuleWeb* для прослушивания порта 80, используемого внешним пулом *myFrontEndPool*, и отправки трафика с балансировкой нагрузки внутреннему пулу адресов *myBackEndPool*, который также использует порт 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Настройка виртуальной сети
Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть

С помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) в группе ресурсов myResourceGroup создайте виртуальную сеть с именем *myVnet*, содержащую подсеть *mySubnet*.


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с именем *myNetworkSecurityGroup* для определения входящих подключений к виртуальной сети с помощью команды [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Создайте правило группы безопасности сети с именем *myNetworkSecurityGroupRule* для порта 80 с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>Создание сетевых адаптеров
Создайте три виртуальных сетевых адаптера с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create) и привяжите их к общедоступному IP-адресу и группе безопасности сети. В следующем примере создаются шесть виртуальных сетевых адаптеров. (по одной виртуальной сетевой карте для каждой виртуальной машины, используемой приложением). Вы можете в любое время создать дополнительные виртуальные сетевые карты и виртуальные машины и добавить их в балансировщик нагрузки:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Создание внутренних серверов
В этом примере описано, как создать три виртуальные машины, расположенные в зонах 1, 2 и 3, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки. Чтобы проверить, успешно ли создана подсистема балансировки нагрузки, установите NGINX на виртуальных машинах.

### <a name="create-cloud-init-config"></a>Создание конфигурации cloud-init

Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также для запуска простого приложения Node.js "Hello World" на виртуальной машине Linux. В текущей оболочке создайте файл cloud-init.txt и вставьте в него следующую конфигурацию. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>Создание зональных виртуальных машин
Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#az_vm_create) в зонах 1, 2 и 3. В следующем примере создается виртуальная машина в каждой зоне и ключи SSH, если они не существуют.

Создайте виртуальную машину в каждой зоне (в зонах 1, 2 и 3) в расположении *westeurope*.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

Получите общедоступный IP-адрес подсистемы балансировки нагрузки, используя команду [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
После этого можно ввести общедоступный IP-адрес в веб-браузер. Прежде чем подсистема балансировки нагрузки начнет распределять трафик между виртуальными машинами, виртуальные машины должны быть подготовлены. Эта подготовка может занять несколько минут. Отображается приложение, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик, как показано в следующем примере:

![Запуск приложения Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Чтобы увидеть, как подсистема балансировки нагрузки распределяет трафик между виртуальными машинами во всех трех зонах, на которых выполняется приложение, остановите работу виртуальной машины в определенной зоны и обновите браузер.

## <a name="next-steps"></a>Дополнительная информация
- Узнайте больше об [Azure Load Balancer уровня "Стандартный"](./load-balancer-standard-overview.md).



