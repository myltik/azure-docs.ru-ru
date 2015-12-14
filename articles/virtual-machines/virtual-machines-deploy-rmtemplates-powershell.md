<properties
	pageTitle="Виртуальные машины диспетчера ресурсов Azure | Microsoft Azure"
	description="Управление виртуальными машинами с помощью диспетчера ресурсов Azure, шаблонов и PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Управление виртуальными машинами с помощью диспетчера ресурсов Azure и PowerShell

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [PowerShell - Windows](virtual-machines-deploy-rmtemplates-powershell.md)
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

Использование шаблонов Azure PowerShell и диспетчера ресурсов предоставляет разнообразные гибкие возможности для управления ресурсами в Microsoft Azure. Задачи, описанные в этой статье, можно использовать для создания ресурсов виртуальных машин и управления ими.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-tutorial-classic-portal.md).

Шаблон диспетчера ресурсов и PowerShell используются в следующих задачах:

- [Создание виртуальной машины](#windowsvm)
- [создание виртуальной машины с помощью специального диска](#customvm);
- [создание нескольких виртуальных машин в виртуальной сети с внешним балансировщиком нагрузки](#multivm);

В этих задачах используется только PowerShell:

- [Удаление группы ресурсов](#removerg)
- [Отображение информации о виртуальной машине](#displayvm)
- [Запуск виртуальной машины](#start)
- [Останов виртуальной машины](#stop)
- [Перезапуск виртуальной машины](#restart)
- [Удаление виртуальной машины](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Шаблоны диспетчера ресурсов Azure и группы ресурсов

В некоторых задачах, приведенных в этой статье, рассказывается, как с помощью шаблонов диспетчера ресурсов Azure и PowerShell автоматизировать развертывание виртуальных машин Azure и управление ими.

Большинство приложений, работающих в Microsoft Azure, созданы на основе сочетания облачных ресурсов различных типов. Эти типы представлены виртуальными машинами (одной или несколькими) и учетными записями хранения, базой данных SQL или виртуальной сетью. С помощью шаблонов диспетчера ресурсов Azure можно управлять этими ресурсами совокупно, используя для ресурсов описание JSON, а также связанные параметры конфигурации и развертывания.

Определив шаблон ресурсов на основе JSON, вы можете использовать его с командой PowerShell для развертывания определенных ресурсов в Azure. Эти команды можно выполнять либо отдельно в командной оболочке PowerShell, либо интегрировать их в сценарий, содержащий дополнительную логику автоматизации.

Ресурсы, созданные с использованием шаблонов диспетчера ресурсов Azure, развертываются как в новой, так и в существующей *группе ресурсов Azure*. Группа ресурсов позволяет совокупно управлять несколькими развернутыми ресурсами как логической группой. Это означает, что вы можете управлять общим жизненным циклом группы или приложения.

Если вас интересует разработка шаблонов, ознакомьтесь со статьей [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).

### Создание группы ресурсов

Для задач по созданию ресурсов необходима группа ресурсов (если она еще не создана).

В следующей команде замените *resource group name* именем новой группы ресурсов, а *Azure location* — расположением центра обработки данных Azure, в котором необходимо разместить ресурс. Затем выполните эту команду.

	New-AzureRmResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>ЗАДАЧА: создание виртуальной машины

В этой задаче используется шаблон из коллекции шаблонов. Дополнительные сведения о шаблоне см. в разделе [Развертывание простой виртуальной машины Windows в Западном регионе США](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

В следующей команде замените *deployment name* именем, которое вы хотите использовать для развертывания, а *resource group name* — именем существующей группы ресурсов. Затем выполните эту команду.

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Ниже приведен пример:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Появится запрос на ввод значений параметров в разделе **parameters** JSON-файла:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

Результат буде выглядеть примерно так:

	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>ЗАДАЧА: создание виртуальной машины с помощью специального диска

В этой задаче используется шаблон из коллекции шаблонов. Дополнительные сведения о шаблоне см. в разделе [Создание виртуальной машины с помощью специального диска VHD](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/).

В следующей команде замените *deployment name* именем, которое вы хотите использовать для развертывания, а *resource group name* — именем существующей группы ресурсов. Затем выполните эту команду.

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Ниже приведен пример:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Появится запрос на ввод значений параметров в разделе **parameters** JSON-файла:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]В примере выше используется VHD-файл, который существует в учетной записи хранилища с именем saacct. Имя диска указано в качестве параметра шаблона.

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>ЗАДАЧА: создание нескольких виртуальных машин в виртуальной сети с внешним балансировщиком нагрузки

В этой задаче используется шаблон из коллекции шаблонов. Дополнительные сведения о шаблоне см. в разделе [Создание виртуальной машины с помощью специального диска VHD](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

В следующей команде замените *deployment name* именем, которое вы хотите использовать для развертывания, а *resource group name* — именем существующей группы ресурсов. Затем выполните эту команду.

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

Появится запрос на ввод значений параметров в разделе **parameters** JSON-файла:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>ЗАДАЧА: удаление группы ресурсов

В следующей команде замените *resource group name* именем группы ресурсов, которую требуется удалить. Затем выполните эту команду.

	Remove-AzureRmResourceGroup  -Name "resource group name"

> [AZURE.NOTE]Чтобы пропустить запрос на подтверждение, используйте параметр **–Force**.

Если параметр -Force не используется, появляется запрос на подтверждение:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>ЗАДАЧА: отображение информации о виртуальной машине

В следующей команде замените *resource group name* именем группы ресурсов, содержащей виртуальную машину, а *VM name* — именем виртуальной машины. Затем выполните эту команду.

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Результат буде выглядеть примерно так:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>ЗАДАЧА: запуск виртуальной машины

В следующей команде замените *resource group name* именем группы ресурсов, содержащей виртуальную машину, а *VM name* — именем виртуальной машины. Затем выполните эту команду.

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Результат буде выглядеть примерно так:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>ЗАДАЧА: остановка виртуальной машины

В следующей команде замените *resource group name* именем группы ресурсов, содержащей виртуальную машину, а *VM name* — именем виртуальной машины. Затем выполните эту команду.

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Появится запрос на подтверждение:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Результат буде выглядеть примерно так:

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>ЗАДАЧА: перезапуск виртуальной машины

В следующей команде замените *resource group name* именем группы ресурсов, содержащей виртуальную машину, а *VM name* — именем виртуальной машины. Затем выполните эту команду.

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Результат буде выглядеть примерно так:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>ЗАДАЧА: удаление виртуальной машины

В следующей команде замените *resource group name* именем группы ресурсов, содержащей виртуальную машину, а *VM name* — именем виртуальной машины. Затем выполните эту команду.

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]Чтобы пропустить запрос на подтверждение, используйте параметр **–Force**.

Если параметр -Force не используется, появляется запрос на подтверждение:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Результат буде выглядеть примерно так:

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

Вот видео, в котором выполняется эта задача:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## Дополнительные ресурсы
[Шаблоны быстрого запуска Azure](http://azure.microsoft.com/documentation/templates/) и [платформы приложений](virtual-machines-app-frameworks.md)

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=AcomDC_1203_2015-->