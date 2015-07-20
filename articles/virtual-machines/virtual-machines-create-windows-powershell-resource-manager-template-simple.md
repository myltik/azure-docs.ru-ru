<properties
	pageTitle="Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов и PowerShell"
	description="Используйте шаблон диспетчера ресурсов и Azure PowerShell для создания виртуальной машины Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="josephd"/>

# Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и Azure PowerShell

Вы можете с легкостью создать виртуальную машину Azure под управлением Windows, используя шаблон диспетчера ресурсов и Azure PowerShell. Этот шаблон позволяет создать одну виртуальную машину под управлением Windows в новой виртуальной сети с единой подсетью в новой группе ресурсов.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

Прежде чем приступить к работе, выполните следующие действия, чтобы проверить правильность настройки и готовность к использованию Azure, Windows PowerShell и Azure PowerShell:

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Создание виртуальной машины Windows

Выполните следующие шаги, чтобы создать виртуальную машину Windows с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью Azure PowerShell.

Выполните команды в следующем примере и укажите имя развертывания Azure, имя группы ресурсов и расположение центра обработки данных Azure. Обратите внимание, что при выполнении команды **New-AzureResourceGroupDeployment** вы увидите запрос на ввод значений для параметров раздела parameters, который находится в JSON-файле. Если вы указали значения всех параметров, команда создаст группу ресурсов и виртуальную машину.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Должно отобразиться примерно следующее:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
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
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Теперь в новой группе ресурсов есть новая виртуальная машина Windows с именем MyWindowsVM.

## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)

[Создание виртуальной машины Windows с помощью диспетчера ресурсов Azure и Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Создание виртуальной машины Windows с помощью Azure PowerShell и диспетчера служб Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Установка и настройка Azure PowerShell](../powershell-install-configure.md)

<!---HONumber=July15_HO2-->