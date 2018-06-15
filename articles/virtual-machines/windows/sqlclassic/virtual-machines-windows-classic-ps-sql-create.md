---
title: Создание виртуальной машины SQL Server в Azure PowerShell (классическая модель) | Документация Майкрософт
description: Содержит описание действий и сценарии PowerShell для создания виртуальной машины Azure на основе образа из коллекции образов виртуальных машин SQL Server. В этом разделе используется классическая модель развертывания.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: 66f44e27562f33373e0b67fe6e0ebf9c6bf99e03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399667"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Подготовка виртуальной машины SQL Server к работе с помощью Azure PowerShell (классическая модель)

В данной статье описаны шаги по созданию виртуальной машины SQL Server в Azure с помощью командлетов PowerShell.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

Версию этой статьи для модели Resource Manager см. в статье [Подготовка виртуальной машины SQL Server к работе с помощью Azure PowerShell (в Resource Manager)](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Установка и настройка PowerShell
1. Если у вас нет учетной записи Azure, используйте [бесплатную пробную версию Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Скачайте и установите последнюю версию команд Azure PowerShell](/powershell/azure/overview).
3. Запустите оболочку Windows PowerShell и подключите ее к своей подписке Azure с помощью команды **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Определение целевого региона Azure

Ваша виртуальная машина SQL Server будет размещаться в облачной службе, которая находится в определенном регионе Azure. Следующие шаги помогут определить регион, учетную запись хранения и облачную службу, которые будут использоваться в оставшейся части руководства.

1. Определите центр обработки данных, на котором должна размещаться виртуальная машина SQL Server. Следующая команда PowerShell отображает список имен доступных регионов.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. После определения региона создайте переменную с именем **$dcLocation** и значением, соответствующим выбранному региону. Например, следующая команда задает восточную часть США в качестве региона:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Указание подписки и учетной записи хранения

1. Определите подписку Azure, которая будет использоваться для новой виртуальной машины.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Укажите целевую подписку Azure в переменной **$subscr** . Затем установите ее как текущую подписку Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. После этого проверьте наличие существующих учетных записей хранения. Следующий сценарий выводит все учетные записи хранения, существующие в выбранном регионе:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Если требуется создать учетную запись хранения, сначала создайте имя учетной записи в строчном регистре командой New-AzureStorageAccount, как в следующем примере: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`.

4. Назначьте имя целевой учетной записи хранения переменной **$staccount**. Затем установите текущую учетную запись хранения и подписку с помощью **Set-AzureSubscription** .

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Выбор образа виртуальной машины SQL Server

1. Получите список доступных образов виртуальных машин SQL Server из коллекции. Свойство **ImageFamily** для этих образов начинается с «SQL». Следующий запрос отображает доступное для вас семейство образов с предустановленным сервером SQL Server.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. В найденном семействе образов может быть несколько опубликованных образов. Используйте следующий сценарий, чтобы найти имя последнего опубликованного образа виртуальной машины для выбранного семейства образов (например, **SQL Server 2016 RTM Enterprise на Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Создание виртуальной машины

Наконец создайте виртуальную машину с помощью PowerShell:

1. Создайте облачную службу для размещения новой виртуальной машины. Обратите внимание, что также можно использовать существующую облачную службу. Создайте новую переменную **$svcname** с коротким именем облачной службы.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Укажите имя и размер виртуальной машины. Дополнительные сведения о размерах виртуальных машин см. в разделе [Размеры виртуальных машин в Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Укажите имя и пароль учетной записи локального администратора.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Выполните следующий сценарий, чтобы создать виртуальную машину.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Дополнительные сведения и параметры конфигурации см. в разделе **Создание своего набора команд** статьи [Создание виртуальной машины Windows с использованием PowerShell и классической модели развертывания](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Пример сценария PowerShell

В следующем примере приведен полный сценарий, создающий виртуальную машину **SQL Server 2016 RTM Enterprise на Windows Server 2012 R2**. При использовании этого сценария необходимо настроить исходные переменные на основе предыдущих шагов, описанных в этом разделе.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Подключение к удаленному рабочему столу

1. Для завершения настройки создайте файлы .RDP для запуска этих виртуальных машин в каталоге документов текущего пользователя:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Запустите файл .RDP из каталога документов. Войдите в систему с именем пользователя и паролем администратора, которые были указаны ранее (например, если имя пользователя — VMAdmin, укажите "\VMAdmin" в качестве пользователя и введите пароль).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Завершение настройки виртуальной машины SQL Server для удаленного доступа

Войдя в виртуальную машину с помощью удаленного рабочего стола, настройте SQL Server в соответствии с указаниями в подразделе [Действия по настройке подключения к SQL Server на виртуальной машине Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные инструкции по подготовке виртуальных машин с помощью PowerShell можно найти в [документации по виртуальным машинам](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Во многих случаях следующим этапом является миграция баз данных на новую виртуальную машину SQL. Руководство по миграции баз данных см. в статье [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Подробнее о создании виртуальных машин SQL на портале Azure см. в статье [Подготовка виртуальной машины SQL Server на портале Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Обратите внимание, что в учебнике, описывающем работу с порталом, виртуальные машины создаются с помощью рекомендуемой модели Resource Manager, а не классической модели, используемой в данном разделе PowerShell.

В дополнение к этим ресурсам рекомендуем ознакомиться с [другими статьями, связанными с запуском SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
