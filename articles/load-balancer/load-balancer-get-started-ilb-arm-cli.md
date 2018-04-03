---
title: Создание внутренней подсистемы балансировки нагрузки уровня "Базовый" с помощью Azure CLI 2.0 | Документация Майкрософт
description: Сведения о создании внутренней подсистемы балансировки нагрузки с помощью Azure CLI 2.0.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: kumud
ms.openlocfilehash: d90a4e74b6ad3bb95e91ad3a5327c887a87784bd
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Создание внутренней подсистемы балансировки нагрузки с помощью Azure CLI 2.0 для распределения нагрузки между виртуальными машинами

В этой статье показано, как создать внутреннюю подсистему балансировки нагрузки для распределения нагрузки между виртуальными машинами. Чтобы протестировать подсистему балансировки нагрузки, мы развернем две виртуальные машины с установленным сервером Ubuntu для распределения между ними нагрузки веб-приложения.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroupILB* в расположении *eastus*.

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

С помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) создайте виртуальную сеть с именем *myVnet*, содержащую подсеть *mySubnet*, в группе ресурсов *myResourceGroup*.

```azurecli-interactive
  az network vnet create \
    --name myVnet
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:
  - интерфейсную конфигурацию IP-адресов, которая принимает входной трафик подсистемы балансировки нагрузки;
  - внутренний пул IP-адресов, на который внешний пул отправляет трафик с балансировкой нагрузки;
  - зонд работоспособности, определяющий работоспособность серверных экземпляров виртуальной машины;
  - правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer"></a>Создание подсистемы балансировки нагрузки

С помощью команды [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) создайте общедоступную подсистему балансировки нагрузки уровня "Базовый" **myLoadBalancer** с интерфейсной конфигурацией IP-адресов **myFrontEnd** и внутренним пулом **myBackEndPool**, связанным с частным IP-адресом **10.0.0.7.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>Создание зонда работоспособности

Зонд работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут принимать трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте зонд работоспособности с помощью команды [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create), чтобы отслеживать работоспособность виртуальных машин. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул IP-адресов для приема трафика, а также порты источника и назначения. С помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) создайте правило подсистемы балансировки нагрузки с именем *myLoadBalancerRuleWeb* для прослушивания порта 80, используемого внешним пулом *myFrontEndPool*, и отправки трафика с балансировкой нагрузки внутреннему пулу адресов *myBackEndPool*, который также использует порт 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Создание серверов для внутреннего пула IP-адресов

Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

###  <a name="create-a-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети для определения входящих подключений к виртуальной сети.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupILB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети, чтобы разрешить входящие подключения через порт 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupILB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 300
```
### <a name="create-nics"></a>Создание сетевых адаптеров

Создайте два сетевых интерфейса с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create) и привяжите их к частному IP-адресу и группе безопасности сети. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом примере описано создание двух виртуальных машин для использования в качестве внутренних серверов для подсистемы балансировки нагрузки. Чтобы проверить, успешно ли создана подсистема балансировки нагрузки, установите NGINX на виртуальных машинах.

### <a name="create-an-availability-set"></a>Создание группы доступности

Создайте группу доступности с помощью команды [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create).

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Создание двух виртуальных машин

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
 
Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#az_vm_create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
Развертывание виртуальных машин может занять несколько минут.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Создание виртуальной машины для проверки работы подсистемы балансировки нагрузки

Чтобы протестировать подсистему балансировки нагрузки, создайте виртуальную машину *myVMTest* и привяжите ее к *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Тестирование внутренней подсистемы балансировки нагрузки

Чтобы протестировать подсистему балансировки нагрузки, сначала получите ее частный IP-адрес. Затем войдите в виртуальную машину myVMTest и введите частный IP-адрес в адресную строку веб-браузера.

Для получения частного IP-адреса подсистемы балансировки нагрузки используйте команду [az network lb show](/cli/azure/network/public-ip##az-network-lb-show). Скопируйте частный IP-адрес и вставьте его в адресную строку веб-браузера виртуальной машины *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer
    --resource-group myResourceGroupILB
``` 
![Проверка балансировщика нагрузки](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, подсистему балансировки нагрузки и все связанные с ними ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Дополнительная информация
В этой статье вы создали внутреннюю подсистему балансировки нагрузки уровня "Базовый", подключили к ней виртуальные машины, настроили правило трафика подсистемы балансировки нагрузки, зонд работоспособности, а также проверили работу подсистемы балансировки нагрузки. Дополнительные сведения о подсистемах балансировки нагрузки и связанных с ними ресурсах см. в статьях с инструкциями.
