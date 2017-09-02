---
title: "Создание масштабируемого набора виртуальных машин для Linux в Azure | Документы Майкрософт"
description: "Создание и развертывание высокодоступного приложения на виртуальных машинах Linux с помощью масштабируемого набора виртуальных машин."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 2b8d519e11f70eda164bd8f6e131a3989f242ab0
ms.contentlocale: ru-ru
ms.lasthandoff: 08/12/2017

---

# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Создание масштабируемого набора виртуальных машин и развертывание высокодоступного приложения на базе Linux
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Можно вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. В рамках этого руководства вы развернете масштабируемый набор виртуальных машин в Azure. Вы узнаете, как выполнять такие задачи.

> [!div class="checklist"]
> * создать приложение для масштабирования с использованием файла конфигурации cloud-init;
> * создавать масштабируемый набор виртуальных машин;
> * увеличивать или уменьшать количество экземпляров в масштабируемом наборе;
> * просматривать сведения о подключении для экземпляров масштабируемого набора;
> * использовать диски данных в масштабируемом наборе.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="scale-set-overview"></a>Обзор масштабируемого набора
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Масштабируемые наборы используют те же компоненты, которые вы изучили в предыдущем руководстве [Создание высокодоступных виртуальных машин](tutorial-availability-sets.md). Виртуальные машины в масштабируемом наборе создаются в группе доступности и распределяются по логическим доменам сбоя и обновления.

Виртуальные машины создаются в масштабируемом наборе по мере необходимости. Можно определить правила автомасштабирования, чтобы управлять добавлением и удалением виртуальных машин в масштабируемом наборе. Эти правила могут активироваться на основе метрик, таких как использование ЦП, использование памяти или сетевой трафик.

При использовании образа платформы Azure масштабируемые наборы поддерживают до 1000 виртуальных машин. Для производственных рабочих нагрузок вам может потребоваться [создать пользовательский образ виртуальной машины](tutorial-custom-images.md). При использовании пользовательского образа в масштабируемом наборе можно создать до 100 виртуальных машин.


## <a name="create-an-app-to-scale"></a>Создание приложения для масштабирования
Для использования в рабочей среде может потребоваться [создать пользовательский образ виртуальной машины](tutorial-custom-images.md), включающий установленное и настроенное приложение. В этом учебнике виртуальные машины настраиваются при первом запуске для быстрого ознакомления с работой масштабируемого набора.

В предыдущем руководстве вы узнали, [Как настроить виртуальную машину Linux при первой загрузке](tutorial-automate-vm-deployment.md) с помощью cloud-init. Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также для запуска простого приложения Node.js "Hello World". 

В текущей оболочке создайте файл *cloud-init.txt* и вставьте в него следующую конфигурацию. Например, создайте файл в Cloud Shell, не на локальном компьютере. Введите `sensible-editor cloud-init.txt`, чтобы создать файл и просмотреть список доступных редакторов. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка:

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


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Прежде чем создать масштабируемый набор, выполните команду [az group create](/cli/azure/group#create) для создания группы ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroupScaleSet* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss#create). В следующем примере создается масштабируемый набор *myScaleSet*, используется пакет cloud-int для настройки виртуальной машины и создаются ключи SSH, если они не существуют.

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут. Некоторые фоновые задачи продолжают работу после возврата к командной строке в Azure CLI. Прежде чем вы получите доступ к приложению, может пройти несколько минут.


## <a name="allow-web-traffic"></a>Разрешение веб-трафика
Подсистема балансировки нагрузки была создана автоматически в составе масштабируемого набора виртуальных машин. Она распределяет трафик в наборе определенных виртуальных машин с помощью правил подсистемы балансировки нагрузки. Дополнительные сведения о принципах работы с балансировщиком нагрузки и его настройки см. в следующем руководстве [Балансировка нагрузки между виртуальными машинами в Azure](tutorial-load-balancer.md).

Чтобы разрешить передачу трафика в веб-приложение, нужно создать правило командой [az network lb rule create](/cli/azure/network/lb/rule#create). В следующем примере создается правило *myLoadBalancerRuleWeb*.

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Тестирование приложения
Чтобы просмотреть приложение Node.js в Интернете, получите общедоступный IP-адрес балансировщика нагрузки с помощью команды [az network public-ip show](/cli/azure/network/public-ip#show). Следующий пример получает IP-адрес для *myScaleSetLBPublicIP*, созданного ранее вместе с масштабируемым набором.

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Введите общедоступный IP-адрес в веб-браузер. Отобразится приложение, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик.

![Запуск приложения Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Чтобы познакомиться с работой масштабируемого набора, принудительно обновите окно веб-браузера, чтобы увидеть, как балансировщик нагрузки распределяет трафик между всеми виртуальными машинами, где выполняется приложение.


## <a name="management-tasks"></a>Задачи управления
На протяжении жизненного цикла масштабируемого набора может возникнуть необходимость выполнить одну или несколько задач управления. Кроме того, можно создавать сценарии для автоматизации различных задач жизненного цикла. CLI Azure 2.0 позволяет быстро выполнять эти задачи. Ниже приведено несколько распространенных задач.

### <a name="view-vms-in-a-scale-set"></a>Просмотр виртуальных машин в масштабируемом наборе
Чтобы просмотреть список виртуальных машин, запущенных в масштабируемом наборе, используйте команду [az vmss list-instances](/cli/azure/vmss#list-instances) следующим образом:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Увеличение или уменьшение числа экземпляров виртуальной машины
Чтобы просмотреть количество экземпляров, присутствующих в масштабируемом наборе, используйте команду [az vmss show](/cli/azure/vmss#show) и отправьте запрос для *sku.capacity*.

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

После этого можно вручную увеличить или уменьшить число виртуальных машин в масштабируемом наборе, выполнив команду [az vmss scale](/cli/azure/vmss#scale). Приведенный ниже пример задает число виртуальных машин в масштабируемом наборе, равное *5*.

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 5
```

Правила автомасштабирования позволяют определить, как изменяется число виртуальных машин в масштабируемом наборе в соответствии с текущими потребностями в таких ресурсах, как пропускная способность сети или вычислительная мощность ЦП. В настоящее время эти правила невозможно задать с помощью Azure CLI 2.0. Для настройки автомасштабирования используйте [портал Azure](https://portal.azure.com).

### <a name="get-connection-info"></a>Получение сведений о подключении
Чтобы получить сведения о подключении для виртуальных машин в масштабируемых наборах, выполните команду [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Эта команда выводит общедоступный IP-адрес и порт для каждой виртуальной машины, которая разрешает подключение по протоколу SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Использование дисков данных с масштабируемыми наборами
Можно создавать диски данных и использовать их с масштабируемыми наборами. В предыдущем руководстве вы узнали, как [управлять дисками Azure](tutorial-manage-disks.md), и ознакомились с рекомендациями и способами повышения производительности для создания приложений на дисках данных, а не на диске ОС.

### <a name="create-scale-set-with-data-disks"></a>Создание масштабируемого набора с дисками данных
Чтобы создать масштабируемый набор и подключить к нему диски данных, добавьте параметр `--data-disk-sizes-gb` в команду [az vmss create](/cli/azure/vmss#create). В следующем примере создается масштабируемый набор, в котором к каждому экземпляру подключен диск данных объемом в *50* ГБ.

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Когда экземпляры удаляются из масштабируемого набора, также удаляются подключенные к ним диски данных.

### <a name="add-data-disks"></a>Добавление дисков данных
Чтобы добавить диск данных к экземплярам в масштабируемом наборе, выполните команду [az vmss disk attach](/cli/azure/vmss/disk#attach). В следующем примере к каждому экземпляру добавляется диск данных объемом в *50* ГБ.

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Отключение дисков данных
Чтобы удалить диск данных из экземпляра в масштабируемом наборе, выполните команду [az vmss disk detach](/cli/azure/vmss/disk#detach). В следующем примере из каждого экземпляра удаляется диск данных LUN *2*.

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Дальнейшие действия
В рамках этого руководства вы создали набор масштабирования виртуальных машин. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * создавать приложение для масштабирования с использованием файла конфигурации cloud-init;
> * создавать масштабируемый набор виртуальных машин;
> * увеличивать или уменьшать количество экземпляров в масштабируемом наборе;
> * просматривать сведения о подключении для экземпляров масштабируемого набора;
> * использовать диски данных в масштабируемом наборе.

Перейдите к следующему руководству, чтобы узнать о принципах балансировки нагрузки для виртуальных машин.

> [!div class="nextstepaction"]
> [Балансировка нагрузки между виртуальными машинами](tutorial-load-balancer.md)
