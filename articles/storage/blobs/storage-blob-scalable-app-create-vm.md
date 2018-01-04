---
title: "Создание виртуальной Машины и учетную запись хранилища для масштабируемых приложений в Azure | Документы Microsoft"
description: "Дополнительные сведения о развертывании виртуальной Машины, используемый для выполнения масштабируемые приложения, с помощью хранилища BLOB-объектов Azure"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 0fd1cd93ca6faabcbe0007136fe427028e722733
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/23/2017
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Создание виртуальной машины и учетная запись хранения для масштабируемых приложений

Это руководство представляет первую часть цикла. В этом учебнике показано, что при развертывании приложения, передачи и загрузки больших объемов случайные данные с учетной записью хранения Azure. По завершении, у вас есть консольное приложение, работающий на виртуальной машине, отправки и загрузки больших объемов данных в учетную запись хранения.

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Создание виртуальной машины
> * Настройте расширение пользовательского скрипта

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell версии 3.6 или более поздней. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
 
Образец загружает 50 больших файлов в контейнер больших двоичных объектов в учетной записи хранилища Azure. Учетная запись хранения предоставляет уникальное пространство имен для хранения ваших объектов данных в службе хранилища Azure и доступа к ним. Создать учетную запись хранилища в группе ресурсов, созданные с помощью [New AzureRmStorageAccount](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccount) команды.

В следующей команде замените `<blob_storage_account>` глобально уникальным именем учетной записи хранения больших двоичных объектов везде, где встречается этот заполнитель.

```powershell-interactive
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте конфигурацию виртуальной машины. Эта конфигурация содержит параметры, которые используются при развертывании виртуальной машины, в том числе образ виртуальной машины, ее размер и настройки аутентификации. При выполнении этого шага будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины.

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Конфигурация развертывания

В этом учебнике существуют необходимые условия, которые должны быть установлены на виртуальной машине. Расширение пользовательского скрипта используется для выполнения сценария PowerShell, который выполняет следующие задачи:

> [!div class="checklist"]
> * Установка основных компонентов .NET 2.0
> * Установка chocolatey
> * Установка GIT
> * Клонировать репозиторий образца
> * Восстановление пакетов NuGet
> * Создает случайными данными 50 файлов 1 ГБ

Запустите следующий командлет, чтобы завершить конфигурацию виртуальной машины. Этот шаг может потребоваться 5-15 минут.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzureRMVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Дальнейшие действия

Первая часть ряда вы узнали о создании учетной записи хранилища, развертывании виртуальной машины и настройка виртуальной машины с обязательным необходимые компоненты, такими как:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Создание виртуальной машины
> * Настройте расширение пользовательского скрипта

Перейти к части два ряда для передачи больших объемов данных в учетной записи хранилища с помощью экспоненциального повторных попыток и параллелизма.

> [!div class="nextstepaction"]
> [Отправка больших объемов больших файлов одновременно для учетной записи хранения](storage-blob-scalable-app-upload-files.md)
