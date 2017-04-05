---
title: "Управление виртуальными машинами Linux с помощью Azure CLI | Документация Майкрософт"
description: "Руководство по управлению виртуальной машиной Linux с помощью Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 14d3907d9ddbe42c9f919ce4d29ba7d58c7c5296
ms.lasthandoff: 03/29/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Управление виртуальной машиной Linux с помощью Azure CLI

Работая с этим руководством, вы создадите виртуальную машину и выполните стандартные задачи по управлению, такие как добавление диска, автоматизация установки программного обеспечения и создание моментального снимка виртуальной машины. 

Для работы с этим руководством нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="step-1--log-in-to-azure"></a>Шаг 1. Вход в Azure

Сначала откройте окно терминала и войдите в подписку Azure, выполнив команду [az login](/cli/azure/#login).

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>Шаг 2. Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины. В этом примере создается группа ресурсов `myResourceGroup` в регионе `westeurope`. 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>Шаг 3. Подготовка конфигурации

При развертывании виртуальной машины можно использовать **cloud-init** для автоматизации настройки (например, для установки пакетов, создания файлов и выполнения скриптов). В этом руководстве автоматически настраиваются две процедуры:

- установка веб-сервера NGINX;
- подготовка второго диска на виртуальной машине.

Так как **cloud-init** настраивается во время развертывания виртуальной машины, конфигурацию **cloud-init** следует определить до создания виртуальной машины.

Создайте файл `cloud-init.txt` и скопируйте в него следующее содержимое. Эта конфигурация устанавливает пакет NGINX и выполняет команды по форматированию и подключению второго диска.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>Шаг 4. Создание виртуальной машины

Создайте виртуальную машину, выполнив команду [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

При создании виртуальной машины доступно несколько вариантов, таких как образ операционной системы, определение размера диска и учетные данные администратора. В этом примере создается виртуальная машина `myVM` под управлением Ubuntu. Создается диск на 50 ГБ и подключается к виртуальной машине с использованием аргумента `--data-disk-sizes-gb`. Аргумент `--custom-data` принимает конфигурацию cloud-init и размещает ее на виртуальной машине. И наконец, создаются ключи SSH, если их нет.

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

После создания виртуальной машины Azure CLI выводит следующие данные. Обратите внимание на общедоступный IP-адрес. Он используется при получении доступа к виртуальной машине. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Во время развертывания виртуальной машины конфигурация **cloud-init** может занять несколько минут. 

## <a name="step-5--configure-firewall"></a>Шаг 5. Настройка брандмауэра

[Группа безопасности сети](../virtual-network/virtual-networks-nsg.md) Azure управляет входящим и исходящим трафиком одной или нескольких виртуальных машин. Правила группы безопасности сети разрешают или запрещают передачу сетевого трафика на определенный порт или диапазон портов. Эти правила также могут включать префикс адреса источника, чтобы на виртуальную машину передавался только трафик от определенного источника.

В предыдущем разделе мы установили веб-сервер NGINX. К нему невозможно получить доступ из Интернета без правила группы безопасности сети, разрешающего передачу входящего трафика через порт 80. На этом шаге представлена процедура создания правила группы безопасности сети, разрешающего входящие подключения на порт 80.

### <a name="create-nsg-rule"></a>Создание правила группы безопасности сети

Чтобы создать правило группы безопасности сети для входящих подключений, используйте команду [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port). В следующем примере открывается порт `80` для виртуальной машины.

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

Теперь перейдите к общедоступному IP-адресу виртуальной машины. После создания правила группы безопасности сети отобразится веб-сайт NGINX по умолчанию.

![Сайт NGINX по умолчанию](./media/virtual-machines-linux-tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>Шаг 6. Создание моментального снимка виртуальной машины

При создании моментального снимка диска создается копия диска на определенный момент времени, предназначенная только для чтения. На этом шаге создается моментальный снимок диска операционной системы виртуальных машин. С помощью моментального снимка диска операционной системы виртуальную машину можно легко восстановить до определенного состояния или создать виртуальную машину с идентичным состоянием.

### <a name="create-snapshot"></a>Создание моментального снимка

Перед созданием моментального снимка требуется идентификатор или имя диска. Чтобы получить идентификатор диска, используйте команду [az vm show](https://docs.microsoft.com/cli/azure/vm#show). В этом примере идентификатор диска хранится в переменной и будет использован в дальнейшем.

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Получив идентификатор диска, выполните следующую команду, создающую моментальный снимок.

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Создание диска на основе моментального снимка

Этот моментальный снимок можно преобразовать в диск, с помощью которого можно повторно создать виртуальную машину.

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Восстановление виртуальной машины на основе моментального снимка

Чтобы продемонстрировать восстановление виртуальной машины, удалите существующую виртуальную машину. 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

При повторном создании виртуальной машины будет повторно использован существующий сетевой интерфейс. При этом сохраняются настройки безопасности сети.

Получите имя сетевого интерфейса, выполнив команду [az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list). В этом примере имя помещается в переменную `nic`, которая будет использоваться в следующем шаге.

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

Создайте виртуальную машину на основе диска моментального снимка.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

Запишите новый общедоступный IP-адрес и перейдите по нему в интернет-браузере. Вы увидите, что NGINX работает в восстановленной виртуальной машине. 

### <a name="reconfigure-data-disk"></a>Перенастройка диска данных

Теперь диск данных можно переподключить к виртуальной машине. 

Сначала найдите имена дисков данных, выполнив команду [az disk list](https://docs.microsoft.com/cli/azure/disk#list). В этом примере имя диска помещается в переменную `datadisk`, которая будет использоваться в следующем шаге.

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Подключите диск, выполнив команду [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach).

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

Кроме того, диск нужно подключить в операционной системе. Для подключения диска подключитесь к виртуальной машине и выполните команду `sudo mount /dev/sdc1 /datadrive` или другую выбранную операцию по подключению диска. 

## <a name="step-7--management-tasks"></a>Шаг 7. Задачи управления

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать скрипты для автоматизации повторяющихся или сложных задач. С помощью Azure CLI в командной строке или в скриптах можно выполнять множество распространенных задач управления. 

### <a name="get-ip-address"></a>Получение IP-адреса

Эта команда возвращает частный и общедоступный IP-адрес виртуальной машины.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>Изменение размера виртуальной машины

Чтобы изменить размер виртуальной машины Azure, необходимо знать названия размеров, доступных в выбранном регионе Azure. Эти размеры можно найти, выполнив команду [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes).

```azurecli
az vm list-sizes --location westeurope --output table
```

Чтобы изменить размер виртуальной машины, выполните команду [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize). 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>Прекращение работы виртуальной машины

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>Запуск виртуальной машины

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов

При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Руководство: [Create load balanced virtual machines](./virtual-machines-linux-tutorial-load-balance-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Создание виртуальных машин с балансировкой нагрузки)

Примеры: [Примеры Azure CLI для виртуальных машин Linux](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
