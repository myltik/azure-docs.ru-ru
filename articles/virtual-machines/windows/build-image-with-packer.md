---
title: Создание образов виртуальных машин Windows в Azure с помощью Packer | Документация Майкрософт
description: Сведения об использовании Packer для создания образов виртуальных машин Windows в Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: iainfou
ms.openlocfilehash: e06db46d5e1d7862f7b47b75e38d0b10df628f48
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778568"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Использование Packer для создания образов виртуальных машин Windows в Azure
Каждая виртуальная машина в Azure создается из образа, который определяет дистрибутив Windows и версию операционной системы. Образы могут содержать предварительно установленные приложения и конфигурации. Azure Marketplace предоставляет большое количество образов Майкрософт и сторонних разработчиков для наиболее распространенных операционных систем и приложений. Кроме того, вы можете создать собственные настраиваемые образы, отвечающие конкретным потребностям. В этой статье описывается определение и создание пользовательских образов в Azure с использованием инструмента с открытым кодом [Packer](https://www.packer.io/).


## <a name="create-azure-resource-group"></a>Создание группы ресурсов Azure
В процессе сборки исходной виртуальной машины Packer создает временные ресурсы Azure. Чтобы сохранить эту исходную виртуальную машину для использования в качестве образа, необходимо определить группу ресурсов. Выходные данные процесса сборки Packer хранятся в этой группе ресурсов.

Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Создание учетных данных Azure
Packer выполняет проверку подлинности с помощью субъекта-службы Azure. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и средствами автоматизации, такими как Packer. Вы можете определять разрешения на то, какие операции может выполнять субъект-служба в Azure, и управлять ими.

Создайте субъект-службу с помощью команды [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) и назначьте ей разрешения на создание и управление ресурсами с помощью [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "Azure Packer" `
    -Password (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force)
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Для выполнения проверки подлинности в Azure вам также необходимо получить идентификаторы вашего клиента и подписки Azure с помощью команды [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

Эти идентификаторы используются в следующем шаге.


## <a name="define-packer-template"></a>Определение шаблона Packer
Чтобы собрать образы, создайте шаблон в формате JSON. В шаблоне определите средства разработки и подготовки, выполняющие процесс сборки. Packer использует [средство разработки для Azure](https://www.packer.io/docs/builders/azure.html), которое позволяет определить ресурсы Azure, такие как учетные данные субъекта-службы, созданные на предыдущем шаге.

Создайте файл с именем *windows.json* и вставьте следующее содержимое. Введите свои значения следующим образом:

| Параметр                           | Где можно получить |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Просмотрите идентификатор субъекта-службы с помощью `$sp.applicationId` |
| *client_secret*                     | Пароль, указанный в `$securePassword` |
| *tenant_id*                         | Выходные данные команды `$sub.TenantId` |
| *subscription_id*                   | Выходные данные команды `$sub.SubscriptionId` |
| *object_id*                         | Просмотрите идентификатор объекта субъекта-службы с помощью `$sp.Id` |
| *managed_image_resource_group_name* | Имя группы ресурсов, созданной на первом шаге |
| *managed_image_name*                | Имя создаваемого образа управляемого диска |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

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
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Этот шаблон создает виртуальную машину Windows Server 2016, устанавливает службы IIS, а затем подготавливает виртуальную машину с помощью средства Sysprep. Установка IIS показывает, как можно использовать средство подготовки PowerShell для выполнения дополнительных команд. В этом случае окончательный образ Packer включает в себя также установочные файлы и параметры необходимого программного обеспечения.


## <a name="build-packer-image"></a>Создание образа Packer
Если средство Packer еще не установлено на локальном компьютере, [следуйте инструкциям по его установке](https://www.packer.io/docs/install/index.html).

Создайте образ, указав файл шаблона Packer следующим образом:

```bash
./packer build windows.json
```

Ниже приведен пример выходных данных предыдущей команды.

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Через несколько минут Packer создаст виртуальную машину, запустит средства подготовки и очистит развертывание.


## <a name="create-a-vm-from-the-packer-image"></a>Создание виртуальной машины на основе образа Packer
Теперь можно создать виртуальную машину из образа с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Если они еще не существуют, создаются поддерживающие сетевые ресурсы. При появлении запроса введите имя пользователя с правами администратора и пароль. Этот пользователь будет создан на виртуальной машине. В следующем примере создается виртуальная машина *myVM* из образа *myPackerImage*.

```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Если вы хотите создать виртуальные машины не в группе ресурсов или регионе, содержащем образ Packer, укажите идентификатор образа, а не его имя. Идентификатор образа можно получить, выполнив команду [Get-AzureRmImage](/powershell/module/AzureRM.Compute/Get-AzureRmImage).

Создание виртуальной машины из образа Packer занимает несколько минут.


## <a name="test-vm-and-webserver"></a>Тестирование виртуальной машины и веб-сервера
Получите общедоступный IP-адрес своей виртуальной машины с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIP*.

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Чтобы увидеть в действии виртуальную машину, которая включает в себя установленный экземпляр IIS из средства подготовки Packer, введите общедоступный IP-адрес в веб-браузере.

![Сайт IIS по умолчанию](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Дополнительная информация
В этом примере вы создали образ виртуальной машины с уже установленными службами IIS с помощью Packer. Этот образ виртуальной машины можно использовать наряду с имеющимися рабочими процессами развертывания, как например развертывание приложений на виртуальных машинах, созданных из образа с помощью Team Services, Ansible, Chef или Puppet.

Дополнительный пример шаблонов Packer для других дистрибутивов Windows см. в этом [репозитории GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
