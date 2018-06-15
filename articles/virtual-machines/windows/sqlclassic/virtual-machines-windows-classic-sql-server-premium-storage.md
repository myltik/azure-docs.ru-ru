---
title: Использование хранилища Azure уровня "Премиум" с SQL Server | Документация Майкрософт
description: В этой статье используется ресурсы, созданные с помощью классической модели развертывания, и предоставляются рекомендации по использованию хранилища Azure Premium с SQL Server, выполняющимся на виртуальных машинах Azure.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: 3d3fdd8865a293c5e2f0df6a97910ac8e2a07d4c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400619"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Использование хранилища Azure Premium Storage с SQL Server на виртуальных машинах
## <a name="overview"></a>Обзор
[Хранилище Azure Premium Storage](../premium-storage.md) — это хранилище нового поколения, обеспечивающее малую задержку и высокую пропускную способность ввода-вывода. Данное хранилище лучше справляется с интенсивными нагрузками ввода-вывода, как, например, SQL Server на [виртуальных машинах](https://azure.microsoft.com/services/virtual-machines/)IaaS.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

Данная статья содержит информацию о планировании и осуществлении миграции виртуальной машины под управлением SQL Server для использования хранилища Premium Storage. Она включает описание этапов работы с инфраструктурой Azure (сеть, хранилище) и гостевой виртуальной машиной Windows. В примере из [приложения](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) приведена комплексная схема миграции, описывающая перенос более крупных виртуальных машин, что позволит воспользоваться преимуществами улучшенного локального хранилища SSD с помощью PowerShell.

Важно понимать весь процесс использования хранилища Azure Premium Storage с сервером SQL Server на виртуальных машинах IAAS. А именно:

* Определение необходимых условий для использования хранилища Premium Storage.
* Примеры развертывания сервера SQL Server на IaaS в хранилище Premium Storage для нового развертывания.
* Примеры миграции существующих развернутых служб — как отдельных серверов, так и развернутых служб, использующих группы доступности AlwaysOn SQL.
* Возможные подходы при миграции.
* Комплексный пример, иллюстрирующий действия в Azure, Windows и SQL Server для миграции существующей реализации AlwaysOn.

Более детальные дополнительные сведения о сервере SQL Server в виртуальных машинах Azure содержатся в статье [SQL Server в виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Автор:** Дэниэл Сол (Daniel Sol) **Технические рецензенты:** Луис Карлос Варгас Херринг (Luis Carlos Vargas Herring), Санджай Мишра (Sanjay Mishra), Правин Митал (Pravin Mital), Юрген Томас (Juergen Thomas), Гонсало Руис (Gonzalo Ruiz).

## <a name="prerequisites-for-premium-storage"></a>Необходимые условия для использования хранилища Premium Storage
Существует несколько предварительных условий для использования Premium Storage.

### <a name="machine-size"></a>Размер машины
Для использования хранилища класса Premium необходимо будет использовать виртуальные машины серии DS. Если ранее вы не использовали в своей облачной службе машин серии DS, необходимо удалить существующую виртуальную машину, сохранить подключенные диски, а затем создать новую облачную службу, прежде чем создавать повторно виртуальную машину с ролью DS\* . Дополнительные сведения о размерах виртуальных машин см. в статье [Размеры виртуальных машин и облачных служб для Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Облачные службы
Виртуальные машины DS* можно использовать с хранилищем Premium Storage, только если они были созданы в новой облачной службе. При использовании SQL Server Always On в Azure прослушиватель Always On ссылается на IP-адрес внутренней или внешней подсистемы балансировки нагрузки Azure, связанный с облачной службой. Данная статья содержит информацию о том, как осуществлять миграцию с сохранением доступности в этом сценарии.

> [!NOTE]
> Виртуальная машина серии DS* должна быть первой виртуальной машиной, развернутой в новой облачной службе.
>
>

### <a name="regional-vnets"></a>Региональные виртуальные сети
Для виртуальных машин DS* необходимо настроить виртуальную сеть (VNET), на которой размещаются ваши виртуальные машины, как региональную. Это «расширяет» виртуальную сеть, позволяя готовить виртуальные машины больших размеров в других кластерах и обеспечивая возможность связи между ними. На следующем снимке экрана в выделенном пункте «Местоположение» показаны региональные виртуальные сети, в то время как на первом результате показана «узкая» виртуальная сеть.

![RegionalVNET][1]

Вы можете подать заявку в службу поддержки корпорации Майкрософт для перехода к региональной виртуальной сети. Служба Майкрософт выполнит соответствующие изменения. Для завершения перехода к региональным виртуальным сетям измените свойство AffinityGroup в конфигурации сети. Сначала экспортируйте конфигурацию сети в PowerShell, затем замените свойство **AffinityGroup** элемента **VirtualNetworkSite** свойством **Location**. Укажите `Location = XXXX`, где `XXXX` регион Azure. Затем импортируйте новую конфигурацию.

Например, рассмотрим следующую конфигурацию виртуальной сети:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Для перемещения в региональную виртуальную сеть в Западной Европе измените конфигурацию следующим образом:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>учетные записи хранения;
Вам необходимо создать учетную запись хранения, настроенную для хранилища класса Premium. Обратите внимание, что использование хранилища Premium Storage настраивается в учетной записи хранения, а не на отдельных виртуальных жестких дисках. Тем не менее при использовании виртуальной машины серии DS* вы можете подключать виртуальные жесткие диски из учетных записей хранения Premium и Standard. Вы можете воспользоваться таким решением, если не хотите размещать виртуальный жесткий диск ОС на учетной записи хранения Premium.

Следующая команда **New-AzureStorageAccountPowerShell** с **типом** Premium_LRS создает учетную запись хранилища класса Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Настройки кэша виртуальных жестких дисков
Основное различие между созданием дисков, входящих в учетную запись хранилища Premium — параметр кэша диска. Для дисков томов данных SQL Server рекомендуется использовать**кэширование чтения**. Для качестве параметра кэша диска для томов журнала транзакций следует выбрать значение**Нет**. Это отличается от рекомендаций для учетных записей хранения уровня "Стандартный".

После присоединения виртуальных жестких дисков параметры кэша изменить нельзя. Необходимо отключить и заново подключить виртуальный жесткий диск с обновленным параметром кэша.

### <a name="windows-storage-spaces"></a>Дисковое пространство Windows
Вы можете использовать [дисковое пространство Windows](https://technet.microsoft.com/library/hh831739.aspx), как это делалось в предыдущем хранилище класса Standard. Это позволит выполнить миграцию виртуальной машины, которая уже использует дисковое пространство. На примере в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (шаг 9 и далее) показан код Powershell для извлечения и импортирования виртуальной машины с несколькими присоединенными виртуальными жесткими дисками.

Пулы носителей использовались со стандартной учетной записью хранения Azure  для повышения пропускной способности и сокращения задержек. Возможно, вам интересно будет попробовать пулы носителей в Premium Storage для новых развертываний, однако они создают дополнительные сложности при настройке хранилища.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Какие виртуальные диски Azure подходят для пулов носителей
Поскольку существуют различные рекомендации относительно параметров кэша для подключенных виртуальных жестких дисков, можно скопировать виртуальные жесткие диски в учетную запись хранения Premium. Тем не менее при их повторном присоединении к новой виртуальной машине серии DS вам может понадобиться изменить параметры кэша. Если у вас имеются отдельные виртуальные жесткие диски для файлов данных SQL и файлов журнала, проще применить рекомендованные параметры кэша хранилища Premium Storage (а не один виртуальный жесткий диск, содержащий оба типа файлов).

> [!NOTE]
> При наличии файлов данных SQL Server и файлов журнала на одном и том же томе выбор параметров кэширования зависит от схем доступа ввода-вывода для рабочих нагрузок базы данных. Только тестирование может показать, какой параметр кэширования лучше всего подходит для данного сценария.
>
>

Однако при использовании дискового пространства Windows, состоящего из нескольких виртуальных жестких дисков, вам необходимо просмотреть свои исходные скрипты, чтобы определить, к какому конкретно пулу относятся подключенные VHD. Это позволит вам соответствующим образом настроить параметры кэша для каждого диска.

При отсутствии доступа к исходному скрипту, показывающему, какие виртуальные жесткие диски относятся к пулу носителей, для определения связей между диском и пулом носителей можно использовать следующие шаги.

Для каждого диска выполните следующие действия:

1. Получите список присоединенных к виртуальной машине дисков с помощью команды **Get-AzureVM** :

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Запишите имя диска и номер логического устройства.

    ![DisknameAndLUN][2]
3. Подключитесь к виртуальной машине с помощью удаленного рабочего стола. Затем перейдите к **Управление компьютером** | **Диспетчер устройств** | **Диски**. Просмотрите свойства каждого диска в «Виртуальных дисках Microsoft»

    ![VirtualDiskProperties][3]
4. Номер логического устройства в данном случае — это номер логического устройства, указанный вами при присоединении виртуального жесткого диска к виртуальной машине.
5. Чтобы перейти к разделу перехода к "Виртуальный диск (Майкрософт)", выберите вкладку **Сведения**, затем список **Свойства** и перейдите в раздел **Ключ драйвера**. В пункте **Значение** вы видите параметр **Смещение**, который на следующем снимке экрана равен 0002. 0002 обозначает PhysicalDisk2, с которым связан пул носителей.

    ![VirtualDiskPropertyDetails][4]
6. Для каждого пула носителей выгрузите связанные диски:

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Теперь вы можете использовать эту информацию, чтобы связать подсоединенные VHD с физическими дисками в пулах носителей.

После сопоставления виртуальных жестких дисков с пулами носителей их можно отсоединить и скопировать в учетную запись хранения Premium, после чего их можно присоединить с правильными параметрами кэша. Ознакомьтесь с примером в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), шаги 8–12. Эти шаги показывают, как извлечь конфигурацию подключенного к виртуальной машине диска VHD в CSV-файл, скопировать диски VHD, изменить параметры кэша конфигурации диска и, наконец, повторно развернуть виртуальную машину как машину серии DS со всеми присоединенными дисками.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Пропускная способность хранилища виртуальной машины и хранилища VHD
Рабочие показатели хранилища зависят от указанного размера виртуальной машины DS* и размеров виртуального жесткого диска. Виртуальные машины имеют различные квоты по числу виртуальных жестких дисков, которые можно к ним подключать, и максимально поддерживаемую пропускную способность (МБ/с). Дополнительные сведения о пропускной способности см. в статье [Размеры виртуальных машин и облачных служб в Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Повышенная производительность диска достигается при увеличении размеров диска. Это следует учитывать при планировании миграции. Дополнительные сведения см. в [таблице производительности и типов дисков](../premium-storage.md#scalability-and-performance-targets).

Наконец, следует помнить, что виртуальные машины имеют различную максимальную пропускную способность, которую они поддерживают для всех подключенных дисков. При высокой нагрузке возможно достижение максимальной пропускной способности, доступной для виртуальной машины данного размера. Например, Standard_DS14 поддерживает до 512 МБ/с. Таким образом, с тремя дисками P30 вы можете достичь максимальной пропускной способности диска виртуальной машины. Однако в данном примере лимит пропускной способности может быть превышен, в зависимости от состава операций чтения и записи ввода-вывода.

## <a name="new-deployments"></a>Новые развертывания
В следующих двух разделах показано, как развернуть виртуальные машины SQL Server для хранилища Premium Storage. Как упоминалось ранее, помещать диск операционной системы в хранилище Premium не обязательно. Такое решение можно выбрать в случае, если вы планируете разместить какие-либо интенсивные рабочие нагрузки ввода-вывода на виртуальном жестком диске операционной системы.

В первом примере показано использование существующих образов из коллекции Azure. Во втором примере показано, как использовать имеющийся пользовательский образ виртуальной машины в существующей учетной записи хранения Standard.

> [!NOTE]
> В этих примерах предполагается, что вы уже создали региональную виртуальную сеть.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Создание новой виртуальной машины с хранилищем Premium Storage с помощью образа из Коллекции
В приведенном ниже примере показано, как разместить виртуальный жесткий диск операционной системы в хранилище Premium Storage и присоединить виртуальные жесткие диски хранилища Premium Storage. Тем не менее диск ОС можно разместить также в учетной записи хранения Standard, присоединив затем виртуальные жесткие диски, находящиеся в учетной записи хранения Premium. Мы продемонстрируем оба сценария.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Шаг 1. Создайте учетную запись хранения Premium
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Шаг 2. Создайте новую облачную службу
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Шаг 3. Зарезервируйте VIP облачной службы (необязательно)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Шаг 4. Создайте контейнер виртуальных машин
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Шаг 5. Разместите виртуальный жесткий диск операционной системы в хранилище Standard или Premium 
    #NOTE: Set up subscription and default storage account which is used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Шаг 6. Создайте виртуальную машину
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Создайте новую виртуальную машину для использования хранилища Premium Storage с помощью настраиваемого образа
В этом сценарии показана ситуация, где у вас есть существующие настраиваемые образы, хранящиеся в учетной записи хранения Standard. Как уже упоминалось, для помещения виртуального жесткого диска ОС в хранилище класса Premium необходимо скопировать образ, имеющийся в учетной записи хранилища класса Standard, и передать его в хранилище класса Premium. Только после этого его можно будет использовать. Если образ хранится локально, вы также можете использовать этот метод для его копирования непосредственно в учетную запись хранения Premium.

#### <a name="step-1-create-storage-account"></a>Шаг 1. Создайте учетную запись хранения
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Шаг 2. Создайте облачную службу
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Шаг 3. Используйте существующий образ
Вы можете использовать существующий образ. Вы также можете [взять образ имеющегося компьютера](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Обратите внимание, что виртуальная машина, образ которой вы создаете, не обязательно должна быть серии DS*. Далее показано, как скопировать созданный образ в учетную запись хранилища уровня "Премиум" с помощью командлета PowerShell **Start-AzureStorageBlobCopy**.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Шаг 4. Скопируйте BLOB-объект между учетными записями хранения
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Шаг 5. Регулярно проверяйте состояние копирования:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Шаг 6. Добавьте диск образа на репозиторий диска Azure в пункте "Подписка"
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Вы можете обнаружить, что даже если состояние указывает на успешный результат, ошибка аренды диска может сохраняться. В этом случае подождите около 10 минут.
>
>

#### <a name="step-7--build-the-vm"></a>Шаг 7. Создание виртуальной машины
Здесь вы создаете виртуальную машину из образа и присоединяете два виртуальных жестких диска хранилища Premium Storage:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This needs to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Существующие развернутые службы, не использующие группы доступности AlwaysOn
> [!NOTE]
> Для операций с существующими развернутыми службами сначала ознакомьтесь с разделом [Необходимые условия для использования хранилища класса Premium](#prerequisites-for-premium-storage) этой статьи.
>
>

Существуют различные рекомендации относительно развертывания SQL Server — как без использования групп доступности AlwaysOn, так и с их использованием. Если вы не используете AlwaysOn и у вас есть изолированный сервер SQL Server, вы можете обновить хранилище до уровня Premium с помощью новой облачной службы и учетной записи хранения. Можно воспользоваться следующими вариантами:

* **Создать новую виртуальную машину SQL Server**. Вы можете создать новую виртуальную машину сервера SQL Server, использующую учетную запись хранения Premium, согласно описанию в пункте «Новые развертывания». Затем выполните резервное копирование и восстановление конфигурации сервера SQL Server и баз данных пользователя. Приложение необходимо обновить для того, чтобы оно ссылалось на новый SQL Server при внутреннем или внешнем доступе. Вам необходимо будет скопировать все объекты «за пределами базы данных», как при выполнении миграции сервера SQL Server Side by Side (SxS). Это относится к таким объектам как имена входа, сертификаты и связанные серверы.
* **Миграция существующей виртуальной машины SQL Server**. Для этого необходимо перевести виртуальную машину SQL Server в автономный режим, а затем перевести ее в новую облачную службу. Данная процедура включает копирование всех присоединенных виртуальных жестких дисков в учетную запись хранилища класса Premium. Когда виртуальная машина перейдет в оперативный режим, приложение будет ссылаться на имя узла сервера, как ранее. Имейте в виду, что размер существующего диска влияет на характеристики производительности. Так, например, диск 400 ГБ округляется до P20. Если вы знаете, что такая производительность диска вам не требуется, вы можете повторно создать виртуальную машину как машину серии DS, а затем подключить виртуальные жесткие диски хранилища Premium с необходимым вам размером и характеристиками производительности. После этого вы можете отсоединять и заново присоединять файлы баз данных SQL Server.

> [!NOTE]
> При копировании виртуальных жестких дисков необходимо следить за размером — от размера зависит тип диска хранилища класса Premium, к которому они будут относиться, а это определяет характеристики производительности диска. Azure округляет значение до ближайшего размера диска, поэтому при наличии диска 400 ГБ его значение округляется до P20. В зависимости от имеющихся требований к операциям ввода-вывода виртуального жесткого диска операционной системы вам может не потребоваться его перенесение в учетную запись хранения Premium.
>
>

Если доступ к SQL Server осуществляется извне, виртуальный IP-адрес облачной службы изменится. Вам также необходимо обновить конечные точки, списки управления доступом и параметры DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Существующие развернутые службы, использующие группы доступности AlwaysOn
> [!NOTE]
> Для операций с существующими развернутыми службами сначала ознакомьтесь с разделом [Необходимые условия для использования хранилища класса Premium](#prerequisites-for-premium-storage) этой статьи.
>
>

В начале этого раздела мы рассмотрим, как Always On взаимодействует с сетью Azure. Затем мы разделим миграции на два сценария: миграции, в которых допускается некоторое время простоя, и миграции, при которых необходимо добиться минимального времени простоя.

Локальные группы доступности AlwaysOn сервера SQL Server используют прослушиватель локально, при этом он регистрирует виртуальное имя DNS и IP-адрес, который разделяется между одним или несколькими серверами SQL. При подключении клиентов они направляются через  IP-адрес прослушивателя на основной сервер SQL. Это сервер, которому на данный момент принадлежит ресурс IP-адреса AlwaysOn.

![DeploymentsUseAlways On][6]

В Microsoft Azure можно иметь только один IP-адрес сетевой карты на виртуальной машине, поэтому для достижения того же уровня абстракции, что и при локальной работе, Azure использует IP-адрес, присвоенный внутренним или внешним подсистемам балансировки нагрузки (ILB/ELB). Разделенный между серверами ресурс IP-адреса настраивается на тот же IP, что и ILB/ELB. Он отображается в DNS, а клиентский трафик передается через ILB/ELB на реплику основного сервера SQL Server. Внутренней или внешней подсистеме балансировки нагрузки известно, какой сервер SQL Server является основным, так как он использует пробы для проверки ресурса IP-адреса AlwaysOn. В предыдущем примере он проверяет каждый узел, имеющий конечную точку, на которую ссылается ELB/ILB. Узел, который ответит, является основным сервером SQL.

> [!NOTE]
> ILB и ELB присваиваются определенной облачной службе Azure, поэтому любая миграция в облако Azure, вероятнее всего, повлечет за собой изменение IP-адреса подсистемы балансировки нагрузки.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Миграция развернутых служб AlwaysOn, допускающая некоторое время простоя
Существуют две стратегии миграции развернутых служб AlwaysOn, допускающей некоторое время простоя.

1. **Добавление дополнительных реплик в существующий кластер AlwaysOn.**
2. **Миграция в новый кластер AlwaysOn.**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Добавление дополнительных реплик в существующий кластер AlwaysOn
Первый способ — добавить несколько дополнительных реплик в группу доступности AlwaysOn. Их необходимо добавить в новую облачную службу, а затем обновить прослушиватель новым IP-адресом подсистемы балансировки нагрузки.

##### <a name="points-of-downtime"></a>Точки простоя:
* Проверка кластера.
* Тестирование отработки отказа AlwaysOn для новых вторичных реплик.

При использовании пулов носителей Windows в виртуальной машине для большей пропускной способности ввода-вывода они отключатся при полной проверке кластера. Выполнение проверки необходимо при добавлении узлов в кластер. Выполнение этой проверки может требовать некоторого времени, поэтому вам следует провести испытание в репрезентативной тестовой среде, чтобы получить данные о приблизительной продолжительности проверки.

Необходимо иметь достаточно времени для того, чтобы можно было выполнить вручную тестирование отработки отказа и хаотическое тестирование в только что добавленных узлах. Это позволит обеспечить надлежащую работу функций высокой доступности AlwaysOn.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Перед началом проверки следует остановить все экземпляры SQL Server, в которых используются пулы носителей.
>
> ##### <a name="high-level-steps"></a>Пошаговые действия
>

1. Создайте два новых сервера SQL в новой облачной службе с присоединенным хранилищем Premium.
2. Скопируйте ПОЛНЫЕ резервные копии и восстановите их с помощью **NORECOVERY**.
3. Скопируйте зависимые объекты «не из базы данных пользователя», например имена входа и т. д.
4. Создайте новую внутреннюю подсистему балансировки нагрузки (ILB) или используйте внешнюю подсистему балансировки нагрузки (ELB), а затем настройте конечные точки с балансировкой нагрузки на обоих новых узлах.

   > [!NOTE]
   > Прежде чем продолжить, проверьте, чтобы конечная точка была правильно настроена для всех узлов.
   >
   >
5. Остановите доступ пользователя или приложения к SQL Server (если используются пулы носителей).
6. Остановите службы ядра SQL Server на всех узлах (если используются пулы носителей).
7. Добавьте новые узлы в кластер и запустите полную проверку.
8. Если проверка прошла успешно, запустите все службы SQL Server.
9. Выполните резервное копирование журналов транзакций и восстановление баз данных пользователя.
10. Добавьте новые узлы в группу доступности AlwaysOn и настройте **синхронную**репликацию.
11. Добавьте ресурс IP-адреса внутреннего или внешнего балансировщика нагрузки новой облачной службы с помощью PowerShell для AlwaysOn на основе примера с несколькими сайтами, приведенного в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). В кластерах Windows установите **возможных владельцев** ресурса **IP-адреса**, указав новые узлы. Ознакомьтесь с разделом «Добавление ресурса IP-адреса в одной подсети» в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Выполните отработку отказа для одного из новых узлов.
13. Укажите новые узлы как «Партнеры автоматической отработки отказов» и проверьте отработку отказов.
14. Удалите исходные узлы из группы доступности.

##### <a name="advantages"></a>Преимущества
* Новые серверы SQL (SQL Server и приложения) можно проверить перед их добавлением в AlwaysOn.
* Вы можете изменять размер виртуальной машины и настраивать хранилище в соответствии с вашими требованиями. Тем не менее полезно было бы оставить все пути файлов SQL без изменений.
* Вы можете управлять началом передачи копий баз данных во вторичные реплики. Это отличается от использования командлета Azure **Start-AzureStorageBlobCopy** для копирования виртуальных жестких дисков, так как это асинхронная копия.

##### <a name="disadvantages"></a>Недостатки
* При использовании пулов носителей Windows возникает время простоя во время полной проверки кластеров для новых дополнительных узлов.
* В зависимости от версии SQL Server и существующего количества вторичных реплик вы можете не иметь возможности добавить несколько вторичных реплик, не удалив уже существующие.
* При настройке вторичных реплик передача данных SQL может занять много времени.
* В случае параллельной работы новых машин это может повлечь дополнительные расходы при миграции.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Миграция в новый кластер AlwaysOn
Другая стратегия — создание нового кластера AlwaysOn с совершенно новыми узлами в новой облачной службе, с последующим перенаправлением клиентов для его использования.

##### <a name="points-of-downtime"></a>Точки простоя
При переносе приложений и пользователей в новый прослушиватель AlwaysOn возникает простой. Время простоя зависит от следующих значений:

* Время, затраченное на восстановление резервных копий журнала транзакций в базах данных на новых серверах.
* Время, затраченное на обновление клиентских приложений для использования нового прослушивателя AlwaysOn.

##### <a name="advantages"></a>Преимущества
* Возможность проверять реальные изменения рабочей среды, SQL Server и сборки операционной системы.
* Возможность настраивать хранилище и уменьшать при необходимости размер виртуальной машины. Это может способствовать снижению затрат.
* Во время этого процесса можно обновить сборку или версию SQL Server. Существует также возможность обновления операционной системы.
* Предыдущий кластер AlwaysOn можно использовать как надежную цель для отката.

##### <a name="disadvantages"></a>Недостатки
* Чтобы оба кластера AlwaysOn работали одновременно, необходимо изменить DNS-имя прослушивателя. Это создает дополнительные административные затраты при миграции, так как строки клиентских приложений должны отражать новое имя прослушивателя.
* Вам необходимо реализовать механизм синхронизации между двумя средами для обеспечения их максимальной близости — это позволит минимизировать конечные требования к синхронизации перед миграцией.
* Дополнительные затраты во время миграции возникают, если у вас работает новая среда.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Миграция развернутых служб AlwaysOn для минимального времени простоя
Существуют две стратегии миграции развернутых служб AlwaysOn для минимального времени простоя:

1. **Использование существующей вторичной реплики: один узел**
2. **Использование существующей вторичной реплики (реплик): несколько узлов**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Использование существующей вторичной реплики: один узел
Одна стратегия для минимального временем простоя предполагает получение вторичной реплики существующего облака и удаление его из текущей облачной службы. Затем следует скопировать виртуальные жесткие диски на новую учетную запись хранения Premium и создать виртуальную машину в новой облачной службе. После этого необходимо обновить прослушиватель в кластеризации и отработку отказов.

##### <a name="points-of-downtime"></a>Точки простоя
* Время простоя возникает при обновлении конечного узла значением конечной точки со сбалансированной нагрузкой.
* Повторное подключение вашего клиента может задерживаться в зависимости от конфигурации клиента/DNS.
* Дополнительное время простоя возникнет, если вы решите перевести группу кластера AlwaysOn в автономный режим, чтобы переключить IP-адреса. Избежать этого можно посредством использования зависимости OR и возможных владельцев для добавленного ресурса IP-адреса. Ознакомьтесь с разделом «Добавление ресурса IP-адреса в одной подсети» в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Если добавленный узел должен действовать как партнер по отработке отказов AlwaysOn, необходимо добавить конечную точку Azure со ссылкой на набор с балансировкой нагрузки. Когда вы запускаете для этого команду **Add-AzureEndpoint**, текущие подключения должны оставаться открытыми, но установить новые подключения к прослушивателю невозможно, пока не обновится подсистема балансировки нагрузки. При тестировании это длилось 90-120 секунд — указанную длительность необходимо проверить.
>
>

##### <a name="advantages"></a>Преимущества
* Дополнительные затраты при миграции не возникают.
* Миграция один к одному.
* Меньший уровень сложности.
* Позволяет увеличить операции ввода-вывода из SKU хранилища Premium. Когда диски отсоединяются от виртуальной машины и копируются в новую облачную службу, можно воспользоваться инструментом стороннего разработчика для увеличения размера виртуального жесткого диска, что обеспечит также увеличение пропускной способности. Для увеличения размера виртуального жесткого диска ознакомьтесь с комментариями на [форуме](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Недостатки
* Возникают временные потери в высокой доступности и аварийном восстановлении во время миграции.
* Так как это миграция 1:1, необходимо использовать минимальный размер виртуальной машины, который поддерживает число виртуальных жестких дисков, так что вам может не удаться уменьшить размер виртуальных машин.
* Этот сценарий будет использовать командлет Azure **Start-AzureStorageBlobCopy** , который является асинхронным. Соглашение об уровне обслуживания после завершения копирования отсутствует. Время копий может быть различным, так как оно зависит от ожидания в очереди, а также от объема передаваемых данных. Время копирования увеличивается, если передача осуществляется на другой центр обработки данных Azure, поддерживающий хранилище Premium в другом регионе. При наличии всего 2 узлов следует воспользоваться доступными возможностями устранения проблем, если копирование занимает больше времени, чем при тестировании. Можно использовать следующие возможности.
  * Добавить временный 3-й узел SQL Server для обеспечения высокой доступности перед миграцией с согласованным временем простоя.
  * Выполнять миграцию не в рамках запланированного обслуживания Azure.
  * Убедиться, что кворум кластера настроен правильно.  

##### <a name="high-level-steps"></a>Пошаговые действия
В настоящем документе не представлено полное и исчерпывающее описание, однако в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) содержатся детальные сведения, которыми вы можете воспользоваться.

![MinimalDowntime][8]

* Получите конфигурацию диска и удалите узел (не удаляйте подключенные виртуальные жесткие диски).
* Создайте учетную запись хранения Premium и скопируйте виртуальные жесткие диски из учетной записи хранения Standard
* Создайте новую облачную службу и повторно разверните виртуальную машину SQL2 в этой службе. Создайте виртуальную машину с помощью скопированного исходного виртуального жесткого диска ОС и присоедините скопированные виртуальные жесткие диски.
* Настройте ILB/ELB и добавьте конечные точки.
* Обновите прослушиватель одним из следующих способов:
  * Переведите группу AlwaysOn в автономный режим и обновите прослушиватель AlwaysOn с помощью нового IP-адреса внутренней или внешней подсистемы балансировки нагрузки.
  * Или добавьте ресурс IP-адреса ILB/ELB новой облачной службы через PowerShell в кластеризацию Windows. Затем укажите возможных владельцев ресурса IP-адреса узла SQL2, для которого выполняется миграция, и настройте его как зависимость OR в сетевом имени. Ознакомьтесь с разделом «Добавление ресурса IP-адреса в одной подсети» в [приложении](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Проверьте конфигурацию и распространение DNS для клиентов.
* Перенесите виртуальную машину SQL1 и выполните шаги 2—4.
* Если используется шаг 5ii, добавьте SQL1 как возможного владельца для добавляемого ресурса IP-адреса.
* Проверьте отработку отказа.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Использование существующей вторичной реплики (реплик): несколько узлов
При наличии узлов в более чем одном центре обработки данных Azure или гибридной среды можно использовать конфигурацию AlwaysOn в этой среде, чтобы свести к минимуму время простоя.

Для этого измените синхронизацию AlwaysOn на синхронную для локального или вторичного центра обработки данных Azure, а затем выполните отработку отказа с переходом на этот SQL Server. Затем скопируйте виртуальные жесткие диски в учетную запись хранения Premium и повторно разверните машину в новой облачной службе. Обновите прослушиватель, затем возвратите систему в рабочее состояние.

##### <a name="points-of-downtime"></a>Точки простоя
Время простоя состоит из времени перехода на альтернативный центр обработки данных и обратно. Время простоя также зависит от конфигурации клиента/DNS: повторное подключение вашего клиента может задерживаться.
Рассмотрим следующий пример гибридной конфигурации AlwaysOn:

![MultiSite1][9]

##### <a name="advantages"></a>Преимущества
* Можно использовать существующую инфраструктуру.
* Имеется возможность предварительного обновления хранилища Azure в центре обработки данных Azure для аварийного восстановления.
* Хранилище центра обработки данных Azure для аварийного восстановления можно перенастраивать.
* Во время миграции выполняются как минимум два перехода на другой ресурс, не считая тестовых переходов.
* Нет необходимости перемещения данных SQL Server с помощью резервного копирования и восстановления.

##### <a name="disadvantages"></a>Недостатки
* В зависимости от клиентского доступа к SQL Server может увеличиваться задержка при работе SQL Server на одном центре обработки данных, а приложения — на другом.
* Копирование виртуальных жестких дисков в хранилище Premium может занять много времени. Это может повлиять на ваше решение о сохранении узла в группе доступности. Об этом следует помнить при интенсивных рабочих нагрузках журнала во время миграции, потому что основной узел должен хранить нереплицированные транзакции в своем журнале транзакций. Это может привести к значительному увеличению размера журнала.
* Этот сценарий будет использовать командлет Azure **Start-AzureStorageBlobCopy** , который является асинхронным. Соглашение об уровне обслуживания после завершения отсутствует. Время копий может быть различным, так как оно зависит от ожидания в очереди, а также от объема передаваемых данных. Поскольку во 2-м центре обработки данных имеется только один узел, необходимо принять меры для устранения проблемы на случай, если копирование займет больше времени, чем при тестировании, . Можно выполнить следующие действия:
  * Добавить временный 2-й узел сервера SQL для обеспечения высокой доступности перед миграцией с согласованным временем простоя.
  * Выполнять миграцию не в рамках запланированного обслуживания Azure.
  * Убедиться, что кворум кластера настроен правильно.

Этот сценарий предполагает, что вы задокументировали процесс установки и знаете, как составлено хранилище, для внесения изменений с целью оптимальной настройки параметров кэша диска.

##### <a name="high-level-steps"></a>Пошаговые действия
![Multisite2][10]

* Сделайте локальный/альтернативный центр обработки данных первичным сервером SQL, и настройте его вторым партнером автоматического перехода на другой ресурс (AFP).
* Получите информацию о конфигурации диска от SQL2 и удалите узел (не удаляйте подключенные виртуальные жесткие диски).
* Создайте учетную запись хранения Premium и скопируйте виртуальные жесткие диски из учетной записи хранения Standard.
* Создайте новую облачную службу и создайте виртуальную машину SQL2 с подключенными дисками хранилища Premium.
* Настройте ILB/ELB и добавьте конечные точки.
* Обновите прослушиватель AlwaysOn с помощью нового IP-адреса внутренней или внешней подсистемы балансировки нагрузки и протестируйте отработку отказа.
* Проверьте конфигурацию DNS.
* Измените AFP на SQL2, затем перенесите SQL1 и выполните шаги 2—5.
* Проверьте отработку отказа.
* Переключите AFP обратно на SQL1 и SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Приложение. Миграция многосайтового кластера AlwaysOn в хранилище класса Premium 
В оставшейся части этой статьи представлен подробный пример преобразования кластера Always On с несколькими сайтами в хранилище класса Premium. В примере также показан перевод прослушивателя с использования внешней подсистемы балансировки нагрузки (ELB) на внутреннюю подсистему балансировки нагрузки (ILB).

### <a name="environment"></a>Среда
* Windows 2k12 / SQL 2k12
* 1 файл БД на SP 
* 2 x пулы носителей на каждом узле

![Приложение1][11]

### <a name="vm"></a>ВМ:
На этом примере мы собираемся продемонстрировать переход от ELB на ILB. Подсистема ELB была доступна раньше, чем ILB, поэтому в данном примере показано, как переключиться на ILB во время миграции.

![Приложение2][12]

### <a name="pre-steps-connect-to-subscription"></a>Предварительные действия: подключитесь к подписке
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Шаг 1. Создайте новую учетную запись хранения и облачную службу
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Шаг 2. Увеличение значения разрешенных ошибок на ресурсах <Optional>
На некоторых ресурсах, принадлежащих группе доступности Always On, существуют ограничения на количество сбоев, которые могут возникнуть в течение определенного периода, когда служба кластеров пытается перезапустить группу ресурсов. Рекомендуется увеличить это значение на время выполнения процедуры, потому как если вы не будете выполнять или вызывать переход на другой ресурс вручную посредством отключения машин, вы можете приблизиться к заданному ограничению.

Рекомендуем увеличить ограничение по сбоям вдвое — для этого в диспетчере отказоустойчивости кластеров перейдите к свойствам группы ресурсов AlwaysOn:

![Приложение3][13]

Установите значение максимальных отказов на 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Шаг 3. Добавление ресурса IP-адреса для группы кластера <Optional>
Если у вас есть только один IP-адрес для группы кластера и он настраивается на подсеть облака, следите за тем, чтобы случайно не перевести в автономный режим все узлы кластера в облаке в данной сети — в таком случае ресурс IP-адреса кластера и сетевое имя кластера не могут перейти в оперативный режим. В этом случае данное действие предотвращает обновления для других ресурсов кластера.

#### <a name="step-4-dns-configuration"></a>Шаг 4. Настройка DNS
Плавность перехода зависит от того, как используется и обновляется DNS.
При установке Always On создается группа ресурсов кластера Windows. Если открыть диспетчер отказоустойчивости кластеров, вы увидите, что он включает по меньшей мере три ресурса. Данный документ касается следующих двух ресурсов.

* Имя виртуальной сети (VNN) — DNS-имя, к которому подключается клиент при попытке соединения с серверами SQL Server через Always On.
* Ресурс IP-адреса — IP-адрес, связанный с именем виртуальной сети. Можно иметь несколько таких адресов, а в конфигурации с несколькими узлами будет отдельный IP-адрес для каждой подсети или узла.

При подключении к SQL Server драйвер клиента SQL Server получает DNS-записи, связанные с прослушивателем, и пытается подключиться к каждому IP-адресу, связанному с Always On. Ниже мы рассматриваем некоторые факторы, которые могут повлиять на это.

Количество одновременных DNS-записей, связанных с именем прослушивателя, зависит не только от количества связанных IP-адресов, но и от параметра RegisterAllIpProviders в отказоустойчивой кластеризации для ресурса имени виртуальной сети AlwaysOn.

При развертывании AlwaysOn в Azure для создания прослушивателя и IP-адреса выполняются различные действия. При этом необходимо вручную задать для параметра RegisterAllIpProviders значение 1, что отличается от локального развертывания AlwaysOn, где данный параметр уже равен 1.

Если "RegisterAllIpProviders" равен 0, то вы увидите только DNS-записи в DNS, связанной с прослушивателем:

![Приложение4][14]

Если RegisterAllIpProviders равен 1:

![Приложение5][15]

В приведенном ниже коде настройки имени виртуальной сети выгружаются и устанавливаются автоматически. Для вступления изменений в силу необходимо перевести виртуальную сеть в автономный режим, а затем вернуть ее в оперативный режим. Это переводит прослушиватель в автономный режим, вызывая прерывание подключения клиента.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

На одном из дальнейших этапов миграции необходимо обновить IP-адрес прослушивателя Always On, который ссылается на подсистему балансировки нагрузки. Это потребует удаления и последующего добавления ресурса IP-адреса. После обновления IP необходимо убедиться, что новый IP-адрес был обновлен в зоне DNS, и что клиенты обновляют свой локальный кэш DNS.

Если клиенты находятся в разных сегментах сети и ссылаются на другой DNS-сервер, необходимо учитывать то, что происходит во время миграции с передачей зоны DNS — время повторного подключения приложения ограничено по меньшей мере временем переноса зон любых новых IP-адресов для прослушивателя. Если ограничения по времени являются для вас слишком жесткими, вам необходимо рассмотреть и протестировать возможность принудительного использования  добавочной передачи зоны вместе с группой специалистов по Windows, а также установить для записи узла DNS более низкое значение времени жизни (TTL), чтобы клиенты выполнили обновление. Дополнительные сведения см. в статье [Добавочные передачи зоны](https://technet.microsoft.com/library/cc958973.aspx) и [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

По умолчанию TTL для записи DNS, связанной с прослушивателем AlwaysOn в Azure, составляет 1200 секунд. Вам может потребоваться уменьшить этот параметр в случае ограничений во времени в процессе миграции, чтобы обеспечить, что клиенты обновят свои DNS с указанием обновленного IP-адреса для прослушивателя. Вы можете просматривать и изменять конфигурацию, выгрузив конфигурацию виртуальной сети:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

> [!NOTE]
> Чем ниже "HostRecordTTL", тем больше объем трафика DNS.

##### <a name="client-application-settings"></a>Параметры клиентского приложения
Если клиентское приложение SQL поддерживает SQLClient .NET 4.5, то в этом случае можно использовать ключевое слово "MULTISUBNETFAILOVER=TRUE". Данное решение является предпочтительным, так как оно обеспечивает более быстрое соединение с группой доступности AlwaysOn SQL во время перехода на другой ресурс. Оно перечисляет все IP-адреса, параллельно связанные с прослушивателем AlwaysOn, и интенсивнее выполняет попытки повторного подключения TCP при отработке отказа.

Дополнительные сведения о предыдущих параметрах см. в разделе [Ключевое слово и связанные функции MultiSubnetFailover](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Ознакомьтесь также со статьей [Поддержка SqlClient для высокого уровня доступности и аварийного восстановления](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Шаг 5. Параметры кворума кластера
Так как вы собираетесь выбрать отключение как минимум одного SQL Server за один раз, вам следует изменить параметры кворума кластера. При использовании файлового ресурса-свидетеля (FSW) с двумя узлами необходимо задать кворум, чтобы разрешить большинство узлов, а также использовать динамическое голосование, позволяя одному узлу продолжать работать.

    Set-ClusterQuorum -NodeMajority  

Дополнительные сведения о настройке кворума кластера и управлении им см. в статье [Configure and Manage the Quorum in a Windows Server 2012 Failover Cluster](https://technet.microsoft.com/library/jj612870.aspx) (Настройка кворума и управление им в отказоустойчивом кластере Windows Server 2012).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Шаг 6. Извлеките существующие конечные точки и списки управления доступом
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Сохраните этот текст в файл.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Шаг 7. Измените партнеров перехода на другой ресурс и режимы репликации
При наличии более двух серверов SQL следует изменить переход на другой ресурс для другой вторичной репликации в другом центре обработки данных или на локальный ресурс на "Синхронный" и указать его как автоматический партнер отработки отказа (AFP). Это позволит вам сохранить высокий уровень доступности в процессе внесения изменений. Это можно сделать через TSQL или изменить при помощи SSMS:

![Приложение6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Шаг 8. Удалите дополнительную виртуальную машину из облачной службы
Вам следует планировать миграцию вторичного узла облака в первую очередь. Если он на данный момент является основным, вам следует инициировать переход на другой ресурс вручную.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Шаг 9. Измените параметры кэширования диска в файле CSV и сохраните
Для томов данных следует установить параметр "Только для чтения".

Для томов TLOG следует установить параметр "Нет".

![Приложение7][17]

#### <a name="step-10-copy-vhds"></a>Шаг 10. Скопируйте виртуальные жесткие диски
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Состояние копирования виртуальных жестких дисков можно проверить в учетной записи хранения Premium:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Приложение8][18]

Подождите, пока все они будут успешно сохранены.

Информация об отдельных больших двоичных объектах.

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Шаг 11. Зарегистрируйте диск ОС
    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Шаг 12. Импортируйте вторичную реплику в новую облачную службу
В приведенном ниже коде также используется дополнительная опция: здесь вы можете импортировать машину и использовать запоминаемые виртуальные IP-адреса.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Шаг 13. Создайте ILB в новой облачной службе, добавьте конечные точки с балансировкой нагрузки и списки управления доступом
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>Шаг 14. Обновите AlwaysOn
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Приложение9][19]

Теперь удалите старый IP-адрес облачной службы.

![Приложение10][20]

#### <a name="step-15-dns-update-check"></a>Шаг 15. Проверка обновлений DNS
Теперь необходимо проверить DNS-серверы в клиентских сетях SQL Server, чтобы убедиться, что кластеризация добавила запись о дополнительном узле для дополнительного IP-адреса. Если эти DNS-серверы не были обновлены, можно воспользоваться принудительной передачей зоны DNS, чтобы клиенты в своих подсетях могли использовать оба IP-адреса AlwaysOn. В таком случае вам не нужно будет ждать автоматической репликации DNS.

#### <a name="step-16-reconfigure-always-on"></a>Шаг 16. Перенастройте AlwaysOn 
На этом этапе вы ожидаете полной повторной синхронизации вторичного узла, относительно которого был проведен перенос, с локальным узлом и переключения в режим синхронной репликации, а также назначения его AFP.  

#### <a name="step-17-migrate-second-node"></a>Шаг 17. Перенесите второй узел
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Шаг 18. Измените параметры кэширования диска в файле CSV и сохраните
Для параметров кэша томов данных следует установить значение "Только для чтения".

Для параметров кэша томов TLOG следует установить значение "Нет".

![Приложение11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Шаг 19. Создайте новую независимую учетную запись хранения для дополнительного узла
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Шаг 20. Скопируйте виртуальные жесткие диски
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Состояние копирования виртуального жесткого диска можно проверить для всех VHD: ForEach ($disk в $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. DiskLabel $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Приложение12][22]

Подождите, пока все они будут успешно сохранены.

Информация об отдельных больших двоичных объектах.

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Шаг 21. Зарегистрируйте диск ОС
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Шаг 22. Добавьте конечные точки с балансировкой нагрузки и списки управления доступом
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Шаг 23. Проверьте отработку отказа
Дождитесь, пока перенесенный узел синхронизируется с локальным узлом Always On. Переведите его в режим синхронной репликации и подождите, пока он синхронизируется. Затем выполните переход с локального узла на первый перенесенный узел, который назначен AFP. После успешного выполнения этого действия измените последний перенесенный узел на AFP.

Вам будет необходимо проверить переходы между всеми узлами и выполнить тесты на несоответствия для обеспечения правильной и своевременной работы переходов на другой ресурс.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Шаг 24. Верните исходные параметры кворума кластера, DNS TTL, параметры перехода на другой ресурс и параметры синхронизации
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Добавление ресурса IP-адреса в одной подсети
Если у вас есть только два сервера SQL и вы хотите перенести их в новую облачную службу, но при этом оставить их в той же подсети, вы можете исключить перевод прослушивателя в автономный режим для удаления исходного IP-адреса Always On и добавления нового IP-адреса. Если вы переносите виртуальные машины в другую подсеть, вам не потребуется выполнять эти действия благодаря наличию дополнительной кластерной сети, которая ссылается на эту подсеть.

После возобновления перенесенной вторичной реплики и добавления нового ресурса IP-адреса для новой облачной службы перед переходом существующего основного сервера на другой ресурс, необходимо выполнить указанные ниже действия в диспетчере отказоустойчивости кластеров.

Чтобы добавить IP-адрес, см. [приложение](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), шаг 14.

1. Для текущего ресурса IP-адреса измените возможного владельца на существующий сервер-источник SQL Server (в примере — dansqlams4):

    ![Приложение13][23]
2. Для нового ресурса IP-адреса измените возможного владельца на перенесенный сервер-получатель SQL Server (в примере — dansqlams5):

    ![Приложение14][24]
3. После этого можно выполнить переход на другой ресурс, а после миграции последнего узла следует изменить возможных владельцев таким образом, чтобы этот узел был добавлен в качестве возможного владельца:

    ![Приложение15][25]

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Хранилище Azure Premium](../premium-storage.md)
* [Виртуальные машины](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server в виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
