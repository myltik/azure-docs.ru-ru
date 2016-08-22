.<properties
   pageTitle="Развертывание виртуальных машин по классической модели развертывания с несколькими сетевыми картами с использованием интерфейса командной строки Azure | Microsoft Azure"
   description="Сведения о развертывании виртуальных машин по классической модели развертывания с несколькими сетевыми картами с использованием интерфейса командной строки Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#Развертывание виртуальных машин с несколькими сетевыми картами с использованием интерфейса командной строки Azure (классическая модель)

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью модели Resource Manager](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

В настоящее время в одной облачной службе нельзя одновременно использовать виртуальные машины с одной сетевой картой и виртуальные машины с несколькими сетевыми картами. Поэтому необходимо реализовать внутренние серверы в другой облачной службе, отдельной от всех прочих компонентов в сценарии. В приведенных ниже действиях используются следующие облачные службы: *IaaSStory* в качестве основной группы ресурсов и *IaaSStory-BackEnd* для внутренних серверов.

## Предварительные требования

Перед развертыванием внутренних серверов необходимо выполнить развертывание основной облачной службы со всеми ресурсами, необходимыми для этого сценария. Нужно создать по крайней мере виртуальную сеть с подсетью для внутреннего сервера. Чтобы узнать, как развертывать виртуальную сеть, см. статью [Создание виртуальной сети с использованием интерфейса командной строки Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Развертывание внутренних виртуальных машин

Внутренние виртуальные машины зависят от создания ресурсов, перечисленных ниже.

- **Учетная запись хранения для дисков данных**. Для повышения производительности для дисков данных на серверах баз данных будет использоваться технология твердотельного накопителя (SSD), которая требует наличия учетной записи хранения класса Premium. Расположение Azure, в которое выполняется развертывание, должно поддерживать хранилище класса Premium.
- **Сетевые карты**. У каждой виртуальной машины будет две сетевые карты: одна для доступа к базе данных, другая — для управления.
- **Группа доступности**. Все серверы баз данных будут добавлены в одну группу доступности, чтобы гарантировать, что как минимум одна из виртуальных машин будет запущена и доступна во время обслуживания.

### Шаг 1. Запуск сценария

Полный сценарий Bash можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Чтобы изменить сценарий для работы в вашей среде, сделайте следующее:

1. Измените значения следующих переменных в зависимости от существующей группы ресурсов, развернутой в соответствии с инструкциями в разделе [Предварительные требования](#Prerequisites) выше.

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. Измените значения следующих переменных на основе значений, которые нужно использовать для внутреннего развертывания.

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

### Шаг 2. Создание необходимых ресурсов для виртуальных машин

1. Создайте облачную службу для всех внутренних виртуальных машин. Обратите внимание, что используется переменная `$backendCSName` для имени группы ресурсов и `$location` для региона Azure.

		azure service create --serviceName $backendCSName \
		    --location $location

2. Создайте учетную запись хранения класса Premium для дисков операционной системы и данных для виртуальных машин.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS

### Шаг 3. Создание виртуальных машин с несколькими сетевыми картами

1. Запустите цикл для создания нескольких виртуальных машин на основе переменных `numberOfVMs`.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Укажите имя и IP-адрес каждой сетевой карты для каждой виртуальной машины.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x

		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. Создайте виртуальную машину. Обратите внимание, что использован параметр `--nic-config`, содержащий список всех сетевых карт с именем, подсетью и IP-адресом.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Создайте по два диска данных для каждой виртуальной машины.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### Шаг 4. Запуск сценария

Теперь, когда вы скачали и изменили сценарий в соответствии со своими потребностями, запустите сценарий для создания виртуальных машин внутренней базы данных с несколькими сетевыми картами.

1. Сохраните сценарий и запустите его в терминале **Bash**. Вы увидите начальный вывод сценария, как показано ниже.

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

<!---HONumber=AcomDC_0810_2016-->