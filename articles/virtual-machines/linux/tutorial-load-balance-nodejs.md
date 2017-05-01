---
title: "Руководство по созданию высокодоступного приложения, выполняемого на виртуальных машинах Azure | Документация Майкрософт"
description: "Сведения о создании высокодоступного безопасного приложения, выполняемого на трех виртуальных машинах Linux с использованием балансировщика нагрузки в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 65ff0e03af6b43478f645274adfeb46d28418894
ms.lasthandoff: 04/07/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-linux-virtual-machines-in-azure"></a>Создание в Azure высокодоступного приложения с балансировкой нагрузки, выполняемого на виртуальных машинах Linux
В этом руководстве описывается создание высокодоступного приложения, устойчивого к сбоям обслуживания. Приложение использует балансировщик нагрузки, группу доступности и три виртуальные машины Linux. В этом руководстве описывается создание приложения Node.js. Тем не менее с его помощью вы можете развернуть другую платформу приложения, используя те же высокодоступные компоненты и рекомендации.

## <a name="step-1---azure-prerequisites"></a>Шаг 1. Предварительные требования Azure
Для работы с этим руководством откройте окно терминала и убедитесь, что установлена последняя версия [Azure CLI 2.0](/cli/azure/install-azure-cli). Если вы еще не вошли в подписку Azure, войдите, выполнив команду [az login](/cli/azure/#login), и следуйте инструкциям на экране.

```azurecli
az login
```

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Прежде чем создавать другие ресурсы Azure, необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>Шаг 2. Создание группы доступности
Виртуальные машины можно создать в логических доменах сбоя и обновления. Каждый логический домен представляет собой часть оборудования в основном центре обработки данных Azure. При создании двух и более виртуальных машин вычислительные ресурсы и ресурсы хранения распределяются по этим доменам. Это позволяет поддерживать доступность приложения в случае обслуживания компонента оборудования. Группы доступности позволяют определить эти логические домены сбоя и обновления.

Создайте группу доступности с помощью команды [az vm availability-set create](/cli/azure/vm/availability-set#create). В следующем примере создается группа доступности с именем `myAvailabilitySet`.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>Шаг 3. Создание балансировщика нагрузки
Приложение Azure Load Balancer распределяет трафик между набором определенных виртуальных машин с помощью правил балансировщика нагрузки. Проба работоспособности позволяет отслеживать данный порт на каждой виртуальной машине и передавать трафик только в рабочую виртуальную машину.

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса
Для доступа к приложению через Интернет назначьте общедоступный IP-адрес для балансировщика нагрузки. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/public-ip#create). В следующем примере создается общедоступный IP-адрес с именем `myPublicIP`.

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки
Создайте балансировщик нагрузки с помощью команды [az network lb create](/cli/azure/network/lb#create). В следующем примере с помощью адреса `myPublicIP` создается балансировщик нагрузки с именем `myLoadBalancer`.

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Создание пробы работоспособности
Чтобы балансировщик нагрузки мог следить за состоянием приложения, необходимо настроить пробу работоспособности. Проба работоспособности динамически добавляет или удаляет виртуальные машины из балансировщика нагрузки на основе их ответа на проверки работоспособности. По умолчанию виртуальная машина удаляется из числа машин, на которые балансировщик распределяет нагрузку, после двух последовательных сбоев с интервалом в 15 секунд. Пробу работоспособности можно создать на основе протокола или конкретной страницы проверки работоспособности приложения. В следующем примере создается проба TCP. Этот пример можно расширить и добавить, например, `--path healthcheck.js`. Чтобы обеспечить работоспособность узла, для балансировщика нагрузки необходимо создать `healthcheck.js` и вернуть ответ **HTTP 200 OK**.

Создайте пробу работоспособности с помощью команды [az network lb probe create](/cli/azure/network/lb/probe#create). В следующем примере создается проба работоспособности с именем `myHealthProbe`, которая отслеживает каждую виртуальную машину.

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки
Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами.

Создайте правило балансировщика нагрузки с помощью команды [az network lb rule create](/cli/azure/network/lb/rule#create). В следующем примере создается правило с именем `myLoadBalancerRule`, используется проба работоспособности `myHealthProbe` и распределяется трафик через порт `80`.

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>Шаг 4. Настройка сети
Каждая виртуальная машина содержит один или несколько виртуальных сетевых адаптеров (NIC), подключенных к виртуальной сети. Эта виртуальная сеть защищена благодаря фильтрации трафика в соответствии с определенными правилами доступа.

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Чтобы установить сетевое подключение к виртуальным машинам, создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/vnet#create). В следующем примере создается виртуальная сеть `myVnet` и подсеть `mySubnet`.

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>Настройка безопасности сети
Группы безопасности сети используют правила для управления входящим и исходящим потоком трафика на виртуальных машинах. Эти правила определяет пользователь, например можно настроить правило, разрешающее прохождение трафика через порт 80. В то время как балансировщик нагрузки распределяет трафик между виртуальными машинами, правила группы безопасности сети гарантируют прохождение только разрешенного трафика.

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create). В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Чтобы разрешить получение веб-трафика в приложении, создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#create). В следующем примере создается правило группы безопасности сети с именем `myNetworkSecurityGroupRule`.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>Создание виртуальных сетевых адаптеров 
При работе балансировщиков нагрузки используется сетевой адаптер, связанный с виртуальной, а не с фактической виртуальной машиной. Виртуальный сетевой адаптер подключается к балансировщику нагрузки, а затем к виртуальной машине.

Создайте виртуальный сетевой адаптер с помощью команды [az network nic create](/cli/azure/network/nic#create). В следующем примере создаются три виртуальных сетевых адаптера (один виртуальный сетевой адаптер создается для каждой виртуальной машины, используемой приложением).

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>Шаг 5. Создание приложения
**cloud-init** — это широко используемый подход к настройке виртуальной машины. Вы можете использовать **cloud-init** для установки пакетов и записи файлов. Так как **cloud-init** выполняется во время первоначального развертывания, дополнительные действия для запуска приложения не требуются. Как только виртуальная машина будет развернута и приложение запустится, балансировщик нагрузки начнет распределение трафика. Дополнительные сведения об использовании **cloud-init** см. в статье [Настройка виртуальной машины Linux во время создания с помощью cloud-init](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Следующая конфигурация **cloud-init** позволяет установить пакеты **nodejs** и **npm**, а также установить и настроить **nginx** в качестве веб-прокси для приложения. Она также создает простое приложение Node.js Hello World, а затем инициализирует и запускает его с помощью **экспресс-запуска**. Если вы хотите использовать другую платформу приложений, настройте пакеты и развернутое приложение соответствующим образом.

Создайте файл с именем `cloud-init.txt` и вставьте следующую конфигурацию:

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
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-6---create-virtual-machines"></a>Шаг 6. Создание виртуальных машин
Теперь, когда все базовые компоненты готовы, можно создать высокодоступные виртуальные машины для запуска приложения.

### <a name="create-vms"></a>Создание виртуальных машин
Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создаются три виртуальные машины и ключи SSH, если они не существуют.

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Создание и настройка трех виртуальных машин занимает несколько минут. Проба работоспособности балансировщика нагрузки автоматически определяет, когда приложение работает на каждой виртуальной машине. Как только приложение будет запущено, правило балансировщика нагрузки начнет распределение трафика.

### <a name="test-your-app"></a>Тестирование приложения
Получите общедоступный IP-адрес балансировщика нагрузки с помощью команды [az network public-ip show](/cli/azure/network/public-ip#show). Следующий пример позволяет получить IP-адрес для созданного ранее `myPublicIP`.

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Введите общедоступный IP-адрес в веб-браузер. Отобразится приложение, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик.

![Запуск приложения Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Принудительно обновите веб-браузер, чтобы увидеть, как балансировщик нагрузки распределяет трафик между тремя виртуальными машинами, на которых выполняется приложение.


## <a name="step-7---management-tasks"></a>Шаг 7. Задачи управления
На виртуальных машинах, выполняющих приложение, может потребоваться выполнить обслуживание, например установить обновления операционной системы. Для обработки большего объема трафика в приложении необходимо добавить дополнительные виртуальные машины. В этом разделе показано, как удалить или добавить виртуальную машину из балансировщика нагрузки.

### <a name="remove-a-vm-from-the-load-balancer"></a>Удаление виртуальной машины из балансировщика нагрузки
Удалите виртуальную машину из внутреннего пула адресов с помощью команды [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove). В следующем примере удаляется виртуальный сетевой адаптер для **myVM2** из `myLoadBalancer`.

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Принудительно обновите веб-браузер, чтобы увидеть, как балансировщик нагрузки распределяет трафик между оставшимися двумя виртуальными машинами, выполняющими приложение. Теперь можно выполнить обслуживание на виртуальной машине, например установить обновления операционной системы или перезагрузить виртуальную машину.

### <a name="add-a-vm-to-the-load-balancer"></a>Добавление виртуальной машины в балансировщик нагрузки
После выполнения обслуживания виртуальной машины, или если необходимо расширить емкость, добавьте виртуальную машину во внутренний пул адресов с помощью команды [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add). В следующем примере добавляется виртуальный сетевой адаптер для **myVM2** в `myLoadBalancer`.

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве описывается создание инфраструктуры высокодоступных приложений с использованием отдельных ресурсов Azure. Дополнительно можно использовать масштабируемые наборы виртуальных машин для автоматического изменения числа виртуальных машин, на которых выполняется ваше приложение. Перейдите к следующему руководству, [Создание высокодоступного приложения в Linux с использованием масштабируемых наборов виртуальных машин](tutorial-convert-to-vmss.md).

Дополнительные сведения о некоторых высокодоступных функциях, представленных в этом руководстве, см. в следующих статьях:

- [Управление доступностью виртуальных машин Linux](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Обзор Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Управление потоком сетевого трафика с помощью групп безопасности сети](../../virtual-network/virtual-networks-nsg.md)
- [Примеры Azure CLI для виртуальных машин Linux](../windows/cli-samples.md)
