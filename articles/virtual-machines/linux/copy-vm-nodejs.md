---
title: Создание копии виртуальной машины Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Узнайте, как создать копию виртуальной машины Linux в Azure, развернутой посредством модели Resource Manager, с помощью Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: bb350f8d14ad451ad3ff7cd617ca3f90967aaa4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Создание копии виртуальной машины Linux, работающей в Azure, с помощью Azure CLI 1.0
В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux, используя модель развертывания с помощью Resource Manager. Сначала следует скопировать операционную систему и диски данных в новый контейнер, а затем настроить сетевые ресурсы и создать новую виртуальную машину.

Кроме того, можно [передать пользовательский образ диска и создать виртуальную машину на его основе](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- Azure CLI 1.0 — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="before-you-begin"></a>Перед началом работы
Перед началом описанной ниже процедуры необходимо выполнить следующие условия.

* Необходимо скачать [интерфейс командной строки Azure (Azure CLI)](../../cli-install-nodejs.md) и установить его на компьютере. 
* Необходимы также некоторые сведения о существующей виртуальной машине Linux в Azure.

| Сведения об исходной виртуальной машине | Как получить |
| --- | --- |
| Имя виртуальной машины |`azure vm list` |
| Имя группы ресурсов |`azure vm list` |
| Расположение |`azure vm list` |
| Имя учетной записи хранения |`azure storage account list -g <resourceGroup>` |
| Имя контейнера |`azure storage container list -a <sourcestorageaccountname>` |
| Имя исходного VHD-файла виртуальной машины |`azure storage blob list --container <containerName>` |

* Необходимо будет выбрать параметры новой виртуальной машины:    <br> - имя контейнера;    <br> - имя виртуальной машины;    <br> - размер виртуальной машины;    <br> - имя виртуальной сети;    <br> - имя подсети;    <br> - имя IP-адреса;    <br> - имя сетевой карты.

## <a name="login-and-set-your-subscription"></a>Вход и задание подписки
1. Войдите в интерфейс командной строки.

    ```azurecli
    azure login
    ```
2. Убедитесь, что режим Resource Manager включен.

    ```azurecli
    azure config mode arm
    ```
3. Задайте соответствующую подписку. Для просмотра всех подписок можно использовать команду azure account list.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Остановка виртуальной машины
Остановите исходную виртуальную машину и отмените ее распределение. Для получения списка всех виртуальных машин в подписке и имен их групп ресурсов можно использовать команду azure vm list.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Копирование виртуального жесткого диска
Скопировать виртуальный жесткий диск из исходного хранилища в место назначения можно с помощью команды `azure storage blob copy start`. В этом примере мы скопируем виртуальный жесткий диск в ту же учетную запись хранения, но в другой контейнер.

Чтобы скопировать виртуальный жесткий диск в другой контейнер в той же учетной записи хранения, введите следующую команду.

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Настройка виртуальной сети для новой виртуальной машины
Настройте виртуальную сеть и сетевую карту для новой виртуальной машины. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Создание виртуальной машины
Теперь можно создать виртуальную машину из переданного виртуального диска, [используя шаблон Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) или интерфейс командной строки, указав универсальный код ресурса (URI) скопированного диска. Для этого введите следующую команду:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать, как использовать интерфейс командной строки Azure для управления новой виртуальной машиной, ознакомьтесь со статьей [Команды Azure CLI в режиме Resource Manager](../azure-cli-arm-commands.md).

