---
title: "Создание высокодоступного приложения в Azure с использованием масштабируемых наборов виртуальных машин | Документация Майкрософт"
description: "Создание и развертывание высокодоступного приложения на виртуальных машинах Linux с помощью масштабируемого набора виртуальных машин и Azure CLI."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>Создание высокодоступного приложения в Linux с использованием масштабируемых наборов виртуальных машин
В этом руководстве показано, как создать высокодоступное приложение в масштабируемом наборе виртуальных машин. Вы также узнаете, как автоматизировать настройку виртуальных машин в масштабируемом наборе. 


## <a name="step-1---create-a-resource-group"></a>Шаг 1. Создание группы ресурсов
Для работы с этим руководством нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli). Если вы еще не вошли в подписку Azure, войдите, выполнив команду [az login](/cli/azure/#login), и следуйте инструкциям на экране.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroupVMSS` в расположении `westus`:

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>Шаг 2. Определение приложения
Используйте конфигурацию **cloud-init** из руководства, описывающего создание высокодоступного приложения с балансировкой нагрузки. Дополнительные сведения об использовании **cloud-init** см. в статье [Настройка виртуальной машины Linux во время создания с помощью cloud-init](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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


## <a name="step-3---create-scale-set"></a>Шаг 3. Создание масштабируемого набора
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Масштабируемые наборы используют те же компоненты, которые вы изучили в руководстве по [созданию высокодоступного приложения в Azure](tutorial-load-balance-nodejs.md). К этим компонентам относятся группы доступности, домены сбоя и домены обновления и подсистемы балансировки нагрузки.

При использовании масштабируемого набора эти ресурсы создаются и управляются автоматически. Можно автоматически увеличивать или уменьшать число виртуальных машин в масштабируемом наборе в зависимости от определенных правил. Вы можете использовать [настраиваемый образ](capture-image.md) в качестве источника для виртуальной машины или настроить виртуальные машины во время развертывания с помощью **cloud-init**, как описывается в этом руководстве.

Создайте масштабируемый набор виртуальных машин командой [az vmss create](/cli/azure/vmss#create). Приведенный ниже пример создает масштабируемый набор `myScaleSet`.

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="step-4---configure-firewall"></a>Шаг 4. Настройка брандмауэра
Подсистема балансировки нагрузки была создана автоматически в составе масштабируемого набора виртуальных машин. Она распределяет трафик в наборе определенных виртуальных машин с помощью правил подсистемы балансировки нагрузки. Чтобы разрешить передачу трафика в веб-приложение, необходимо создать правило командой [az network lb probe create](/cli/azure/network/lb/probe#create). В следующем примере создается правило `myLoadBalancerRuleWeb`.

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>Шаг 5. Тестирование приложения
Получите общедоступный IP-адрес балансировщика нагрузки с помощью команды [az network public-ip show](/cli/azure/network/public-ip#show). Следующий пример получает IP-адрес для `myScaleSetLBPublicIP`, созданного ранее вместе с масштабируемым набором.

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Введите общедоступный IP-адрес в веб-браузер. Отобразится приложение, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик.

![Запуск приложения Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Принудительно обновите окно веб-браузера, чтобы увидеть, как подсистема балансировки нагрузки распределяет трафик между всеми виртуальными машинами в масштабируемом наборе, в котором выполняется приложение.


## <a name="step-6---management-tasks"></a>Шаг 6. Задачи управления
На протяжении жизненного цикла масштабируемого набора может возникнуть необходимость выполнить одну или несколько задач управления. Кроме того, можно создавать сценарии для автоматизации различных задач жизненного цикла. CLI Azure 2.0 позволяет быстро выполнять эти задачи. Ниже приведено несколько распространенных задач.

### <a name="increase-or-decrease-vm-instances"></a>Увеличение или уменьшение числа экземпляров виртуальной машины
Можно вручную увеличить или уменьшить число виртуальных машин в масштабируемом наборе, выполнив команду [az vmss scale](/cli/azure/vmss#scale). Приведенный ниже пример увеличивает число виртуальных машин в масштабируемом наборе до `5`.

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

Правила автомасштабирования позволяют определить, как изменяется число виртуальных машин в масштабируемом наборе в соответствии с текущими потребностями в таких ресурсах, как пропускная способность сети или вычислительная мощность ЦП. В настоящее время эти правила невозможно задать с помощью Azure CLI 2.0. Для настройки автомасштабирования используйте [портал Azure](https://portal.azure.com).

### <a name="get-connection-info"></a>Получение сведений о подключении
Чтобы получить сведения о подключении для виртуальных машин в масштабируемых наборах, выполните команду [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Эта команда выводит общедоступные IP-адреса и порты для каждой виртуальной машины, которая разрешает подключение по протоколу SSH.

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов
При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы определили веб-приложение с помощью **cloud-init** и настроили каждую виртуальную машину при развертывании. Сведения о записи виртуальной машины для использования в качестве исходного образа в масштабируемом наборе приведены в разделе [Как подготовить к работе и записать образ виртуальной машины Linux](capture-image.md).

Дополнительные сведения о некоторых функциях масштабируемых наборов виртуальных машин, представленных в этом руководстве, см. в следующих статьях:

- [Общие сведения о масштабируемых наборах виртуальных машин в Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Обзор Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Управление потоком сетевого трафика с помощью групп безопасности сети](../../virtual-network/virtual-networks-nsg.md)
