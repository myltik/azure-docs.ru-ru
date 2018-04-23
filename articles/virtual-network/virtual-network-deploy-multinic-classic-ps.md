---
title: Создание виртуальных машин (классическая модель) с несколькими сетевыми картами с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать виртуальные машины (классическая модель) с несколькими сетевыми картами с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a86a3cecffa50143afbb7aad20b1a40baeb87c38
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Создание виртуальных машин (классическая модель) с несколькими сетевыми интерфейсами с помощью PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Можно создать виртуальные машины в Azure и подключить к каждой из них несколько сетевых карт. Применение нескольких сетевых карт дает возможность разделять типы трафика между сетевыми картами. Например, одна сетевая карта может обмениваться данными с Интернетом, а другая — только с внутренними ресурсами, не подключенными к Интернету. Возможность разделения сетевого трафика между несколькими сетевыми картами требуется для работы многих виртуальных сетевых устройств, таких как решения для доставки приложений и оптимизации глобальной сети.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Действия для модели развертывания с помощью Resource Manager см. в [этой статье](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

В приведенных ниже действиях используются следующие группы ресурсов: *IaaSStory* для веб-серверов и *IaaSStory-BackEnd* для серверов базы данных.

## <a name="prerequisites"></a>предварительным требованиям

Перед созданием серверов базы данных необходимо создать группу ресурсов *IaaSStory* со всеми ресурсами, необходимыми для этого сценария. Чтобы создать эти ресурсы, выполните приведенные ниже действия. Создайте виртуальную сеть, следуя инструкциям в [этой статье](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Создание внутренних виртуальных машин
Внутренние виртуальные машины зависят от создания следующих ресурсов:

* **Внутренняя подсеть**. Чтобы разделить трафик, серверы базы данных будут входить в отдельную подсеть. Приведенный ниже сценарий предполагает наличие этой подсети в виртуальной сети с именем *WTestVnet*.
* **Учетная запись хранения для дисков данных**. Для повышения производительности для дисков данных на серверах баз данных будет использоваться технология твердотельного накопителя (SSD), которая требует наличия учетной записи хранения класса Premium. Расположение Azure, в которое выполняется развертывание, должно поддерживать хранилище класса Premium.
* **Группа доступности**. Все серверы баз данных будут добавлены в одну группу доступности, чтобы гарантировать, что как минимум одна из виртуальных машин будет запущена и доступна во время обслуживания.

### <a name="step-1---start-your-script"></a>Шаг 1. Запуск сценария
Полный сценарий PowerShell можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Чтобы изменить сценарий для работы в вашей среде, сделайте следующее:

1. Измените значения следующих переменных в зависимости от существующей группы ресурсов, развернутой в соответствии с инструкциями в разделе [Предварительные требования](#Prerequisites)выше.

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Измените значения следующих переменных на основе значений, которые вы хотите использовать для внутреннего развертывания.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Шаг 2. Создание необходимых ресурсов для виртуальных машин
Необходимо создать облачную службу и учетную запись хранения для дисков данных для всех виртуальных машин. Кроме того, для виртуальных машин необходимо указать образ и учетную запись локального администратора. Чтобы создать эти ресурсы, выполните следующие действия:

1. Создайте облачную службу.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Создайте учетную запись хранения класса Premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Задайте созданную ранее учетную запись хранения в качестве текущей учетной записи хранения для подписки.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Выберите образ виртуальной машины.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Задайте учетные данные пароля учетной записи локального администратора.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Шаг 3. Создание виртуальных машин
Необходимо использовать цикл, чтобы создать необходимое количество виртуальных машин, и создать в нем необходимые сетевые карты и виртуальные машины. Чтобы создать сетевые карты и виртуальные машины, сделайте следующее:

1. Запустите цикл `for` для повтора команды, которая позволяет создать виртуальную машину и две сетевые карты необходимое количество раз на основе значения переменной `$numberOfVMs`.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Создайте объект `VMConfig`, указывающий образ, размер и группу доступности для виртуальной машины.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Подготовьте виртуальную машину в качестве виртуальной машины Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Задайте сетевую карту по умолчанию и назначьте ей статический IP-адрес.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Добавьте вторую сетевую карту для каждой виртуальной машины.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Создайте по два диска данных для каждой виртуальной машины.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Создайте каждую виртуальную машину и завершите цикл.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Шаг 4. Запуск сценария
Теперь, когда вы скачали и изменили сценарий в соответствии со своими потребностями, запустите сценарий для создания виртуальных машин внутренней базы данных с несколькими сетевыми картами.

1. Сохраните сценарий и запустите его из командной строки **PowerShell** или **интегрированной среды сценариев PowerShell**. Вы увидите начальный вывод сценария, как показано ниже.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Укажите учетные данные и нажмите кнопку **ОК**. Возвращаются следующие выходные данные.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Шаг 5. Настройка маршрутизации в операционной системе виртуальной машины

Служба DHCP Azure назначает шлюз по умолчанию для первого (основного) сетевого интерфейса, подключенного к виртуальной машине. Azure не назначает шлюз по умолчанию для второго (дополнительного) сетевого интерфейса, подключенного к виртуальной машине. Таким образом, невозможно по умолчанию связываться с ресурсами, к которым подключен дополнительный сетевой интерфейс вне подсети. Дополнительные сетевые интерфейсы могут взаимодействовать с ресурсами вне их подсети. Инструкции по включению дополнительных сетевых интерфейсов см. в разделе [Маршрутизация в операционной системе виртуальной машины с несколькими сетевыми интерфейсами](virtual-network-network-interface-vm.md).
