---
title: "Создание образов виртуальных машин Windows в Azure с помощью Packer | Документация Майкрософт"
description: "Сведения об использовании Packer для создания образов виртуальных машин Windows в Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/13/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 035286e74e66cf1eb12a51551fa55a2a0ea5517c
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Использование Packer для создания образов виртуальных машин Windows в Azure
Каждая виртуальная машина в Azure создается из образа, который определяет дистрибутив Windows и версию операционной системы. Образы могут содержать предварительно установленные приложения и конфигурации. Azure Marketplace предоставляет большое количество образов Майкрософт и сторонних разработчиков для наиболее распространенных операционных систем и приложений. Кроме того, вы можете создать собственные настраиваемые образы, отвечающие конкретным потребностям. В этой статье описывается определение и создание пользовательских образов в Azure с использованием средства с открытым кодом [Packer](https://www.packer.io/).


## <a name="create-supporting-azure-resources"></a>Создание вспомогательных ресурсов Azure
В процессе сборки исходной виртуальной машины Packer создает временные ресурсы Azure. Чтобы сохранить эту исходную виртуальную машину для использования в качестве образа, необходимо определить группу ресурсов и учетную запись хранения. Выходные данные процесса сборки Packer хранятся в этой группе ресурсов и учетной записи хранения.

Сначала создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

Чтобы создать учетную запись хранения, используйте командлет [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Имя учетной записи хранения должно быть уникальным, содержать от 3 до 24 символов и состоять только из цифр и строчных букв. В следующем примере создается учетная запись хранения с именем *mystorageaccount*:

```powershell
$storageAccountName = "mystorageaccount"
New-AzureRmStorageAccount -ResourceGroupName $rgName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"
```


## <a name="create-azure-credentials"></a>Создание учетных данных Azure
Packer выполняет проверку подлинности с помощью субъекта-службы Azure. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и средствами автоматизации, такими как Packer. Вы можете определять разрешения на то, какие операции может выполнять субъект-служба в Azure, и управлять ими.

Создайте субъект-службу с помощью команды [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) и назначьте ей разрешения на создание и управление ресурсами с помощью [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "Azure Packer" -Password "P@ssw0rd!"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Для выполнения проверки подлинности в Azure вам также необходимо получить идентификаторы вашего клиента и подписки Azure с помощью команды [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId
$sub.SubscriptionId
```

Эти идентификаторы используются в следующем шаге.


## <a name="define-packer-template"></a>Определение шаблона Packer
Чтобы собрать образы, создайте шаблон в формате JSON. В шаблоне определите средства разработки и подготовки, выполняющие процесс сборки. Packer использует [средство подготовки для Azure](https://www.packer.io/docs/builders/azure.html), которое позволяет определить ресурсы Azure, такие как учетные данные субъекта-службы, созданные на предыдущем шаге.

Создайте файл с именем *windows.json* и вставьте следующее содержимое. Введите свои значения следующим образом:

| Параметр       | Где можно получить |
|-----------------|----------------------------------------------------|
| *client_id*      | Просмотрите идентификатор субъекта-службы с помощью `$sp.applicationId` |
| *client_secret*  | Пароль, указанный в `$securePassword` |
| *tenant_id*      | Выходные данные команды `$sub.TenantId` |
| *subscription_id* | Выходные данные команды `$sub.SubscriptionId` |
| *object_id*       | Просмотрите идентификатор объекта субъекта-службы с помощью `$sp.Id` |
| *storage_account* | Имя, указанное в `$storageAccountName` |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": "true",
    "winrm_insecure": "true",
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force}",
      "& $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /shutdown /quiet"
    ]
  }]
}
```

Этот шаблон создает виртуальную машину Windows Server 2016, устанавливает службы IIS, а затем подготавливает виртуальную машину с помощью средства Sysprep.


## <a name="build-packer-image"></a>Создание образа Packer
Если средство Packer еще не установлено на локальном компьютере, [следуйте инструкциям по его установке](https://www.packer.io/docs/install/index.html).

Создайте образ, указав файл шаблона Packer следующим образом:

```bash
./packer build windows.json
```

Ниже приведен пример выходных данных предыдущей команды:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the certificate's URL ...
==> azure-arm:  -> Key Vault Name        : 'pkrkvbxpchwtl43'
==> azure-arm:  -> Key Vault Secret Name : 'packerKeyVaultSecret'
==> azure-arm:  -> Certificate URL       : 'https://pkrkvbxpchwtl43.vault.azure.net/secrets/packerKeyVaultSecret/6c12261b552c48ebadb7d4a88d99d011'
==> azure-arm: Setting the certificate's URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '40.121.160.214'
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /tmp/packer-powershell-provisioner759984171
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04"><Obj S="progress" RefId="0"><TN RefId="0"><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N="SourceId">1</
I64><PR N="Record"><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd?se=2017-07-13T22%3A25%3A02Z&sig=BYAbOXakavYV%2FbdWYfGqUIdsz2GXivbk0hxG0
5Mc09k%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json?se=2017-07-13T22%3A25%3A02Z&sig=wDMO3aSifbWLSISwoUOfkDMc5z7iKbGV
3us64gGvvlw%3D&sp=r&sr=b&sv=2015-02-21
```

Через несколько минут Packer создаст виртуальную машину, запустит средства подготовки и очистит развертывание.


## <a name="create-azure-image"></a>Создание образа Azure
Выходными данными процесса сборки Packer является виртуальный жесткий диск (VHD) в указанной учетной записи хранения. Создайте образ Azure из этого виртуального жесткого диска с помощью команды [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) и укажите путь `OSDiskUri`, определенный в конце выходных данных сборки Packer. В следующем примере создается образ с именем `myImage`:

```powershell
$osVhdUri = "https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd"
$imageName = "myImage"

$imageConfig = New-AzureRmImageConfig -Location $location
$imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig `
    -OsType "Windows" `
    -OsState "Generalized" `
    -BlobUri $osVhdUri
$image = New-AzureRmImage -ImageName $imageName `
    -ResourceGroupName $rgName `
    -Image $imageConfig
```

Этот образ можно использовать для создания виртуальных машин в своей подписке Azure. Вы можете создавать виртуальные машины не только в той же группе ресурсов, где расположен исходный образ.


## <a name="create-vm-from-azure-image"></a>Создание виртуальной машины на основе образа Azure
Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential).

```powershell
$cred = Get-Credential
```

Теперь можно создать виртуальную машину из образа с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVM* из образа *myImage*.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod "Static" `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```

Создание виртуальной машины занимает несколько минут.


## <a name="test-vm-and-iis"></a>Тестирование виртуальной машины и служб IIS
Получите общедоступный IP-адрес своей виртуальной машины с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIP*.

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIP" | select "IpAddress"
```

После этого можно ввести общедоступный IP-адрес в веб-браузер.

![Сайт IIS по умолчанию](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Дальнейшие действия
В этом примере вы создали образ виртуальной машины с уже установленными службами IIS с помощью Packer. Этот образ виртуальной машины можно использовать наряду с имеющимися рабочими процессами развертывания, как например развертывание приложений на виртуальных машинах, созданных из образа с помощью Team Services, Ansible, Chef или Puppet.

Дополнительный пример шаблонов Packer для других дистрибутивов Windows см. в этом [репозитории GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
