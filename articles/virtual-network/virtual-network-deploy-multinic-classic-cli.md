---
title: Создание виртуальных машин (классическая модель) с несколькими сетевыми картами (Azure CLI 1.0) | Документация Майкрософт
description: Узнайте, как создать виртуальную машину (классическая модель) с несколькими сетевыми картами с помощью интерфейса командной строки Azure (CLI) версии 1.0.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2631e531368df4f00d7ffd8fef585038658797b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Создание виртуальных машин (классическая модель) с несколькими сетевыми картами с помощью Azure CLI 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Можно создать виртуальные машины в Azure и подключить к каждой из них несколько сетевых карт. Применение нескольких сетевых карт дает возможность разделять типы трафика между сетевыми картами. Например, одна сетевая карта может обмениваться данными с Интернетом, а другая — только с внутренними ресурсами, не подключенными к Интернету. Возможность разделения сетевого трафика между несколькими сетевыми картами требуется для работы многих виртуальных сетевых устройств, таких как решения для доставки приложений и оптимизации глобальной сети.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Действия для модели развертывания с помощью Resource Manager см. в [этой статье](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

В приведенных ниже действиях используются следующие группы ресурсов: *IaaSStory* для веб-серверов и *IaaSStory-BackEnd* для серверов базы данных.

## <a name="prerequisites"></a>предварительным требованиям
Перед созданием серверов базы данных необходимо создать группу ресурсов *IaaSStory* со всеми ресурсами, необходимыми для этого сценария. Чтобы создать эти ресурсы, выполните приведенные ниже действия. Создайте виртуальную сеть, следуя инструкциям в [этой статье](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Развертывание внутренних виртуальных машин
Внутренние виртуальные машины зависят от создания следующих ресурсов:

* **Учетная запись хранения для дисков данных**. Для повышения производительности для дисков данных на серверах баз данных будет использоваться технология твердотельного накопителя (SSD), которая требует наличия учетной записи хранения класса Premium. Расположение Azure, в которое выполняется развертывание, должно поддерживать хранилище класса Premium.
* **Сетевые карты**. У каждой виртуальной машины будет две сетевые карты: одна для доступа к базе данных, другая — для управления.
* **Группа доступности**. Все серверы баз данных будут добавлены в одну группу доступности, чтобы гарантировать, что как минимум одна из виртуальных машин будет запущена и доступна во время обслуживания.

### <a name="step-1---start-your-script"></a>Шаг 1. Запуск сценария
Полный сценарий Bash можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Чтобы изменить скрипт для работы в вашей среде, сделайте следующее:

1. Измените значения следующих переменных в зависимости от существующей группы ресурсов, развернутой в соответствии с инструкциями в разделе [Предварительные требования](#Prerequisites)выше.

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Измените значения следующих переменных на основе значений, которые вы хотите использовать для внутреннего развертывания.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Шаг 2. Создание необходимых ресурсов для виртуальных машин
1. Создайте облачную службу для всех внутренних виртуальных машин. Обратите внимание, что используется переменная `$backendCSName` для имени группы ресурсов и `$location` для региона Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Создайте учетную запись хранения класса Premium для дисков операционной системы и данных для виртуальных машин.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Шаг 3. Создание виртуальных машин с несколькими сетевыми картами
1. Запустите цикл для создания нескольких виртуальных машин на основе переменных `numberOfVMs`.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Укажите имя и IP-адрес каждой сетевой карты для каждой виртуальной машины.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Создайте виртуальную машину. Обратите внимание, что использован параметр `--nic-config`, содержащий список всех сетевых карт с именем, подсетью и IP-адресом.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Создайте по два диска данных для каждой виртуальной машины.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Шаг 4. Запуск сценария
Теперь, когда вы скачали и изменили сценарий в соответствии со своими потребностями, запустите сценарий для создания виртуальных машин внутренней базы данных с несколькими сетевыми картами.

1. Сохраните сценарий и запустите его в терминале **Bash** . Вы увидите начальный вывод сценария, как показано ниже.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Через несколько минут выполнение завершится и отобразится остальной вывод, как показано ниже.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Шаг 5. Настройка маршрутизации в операционной системе виртуальной машины

Служба DHCP Azure назначает шлюз по умолчанию для первого (основного) сетевого интерфейса, подключенного к виртуальной машине. Azure не назначает шлюз по умолчанию для второго (дополнительного) сетевого интерфейса, подключенного к виртуальной машине. Таким образом, невозможно по умолчанию связываться с ресурсами, к которым подключен дополнительный сетевой интерфейс вне подсети. Дополнительные сетевые интерфейсы могут взаимодействовать с ресурсами вне их подсети. Инструкции по включению дополнительных сетевых интерфейсов см. в разделе [Маршрутизация в операционной системе виртуальной машины с несколькими сетевыми интерфейсами](virtual-network-network-interface-vm.md).
