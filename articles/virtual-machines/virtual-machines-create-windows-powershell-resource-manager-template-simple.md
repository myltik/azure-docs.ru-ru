<properties
	pageTitle="Создание виртуальной машины с помощью шаблона | Microsoft Azure"
	description="Используйте шаблон диспетчера ресурсов и Azure PowerShell для создания виртуальной машины Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов и PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


Вы можете с легкостью создать виртуальную машину Azure под управлением Windows, используя шаблон диспетчера ресурсов и Azure PowerShell. Этот шаблон позволяет создать одну виртуальную машину под управлением Windows в новой виртуальной сети с единой подсетью в новой группе ресурсов.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Создание виртуальной машины Windows

Выполните следующие шаги, чтобы создать виртуальную машину Windows с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью Azure PowerShell.

В следующих командах введите имя, которое вы хотите использовать для развертывания, имя для новой группы ресурсов и расположение, где следует создать ресурс.


		$deployName="<deployment name>"
		$RGName="<resource group name>"
		$locName="<Azure location, such as West US>"
		$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
		New-AzureRmResourceGroup –Name $RGName –Location $locName
		New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


При выполнении команды **New-AzureRmResourceGroupDeployment** вы увидите запрос на ввод значений для параметров раздела parameters, который находится в JSON-файле. Если вы указали значения всех параметров, команда создаст группу ресурсов и виртуальную машину.

Вы увидите нечто вроде этого:


		cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
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
											Name                   Type                       Value
											===============        =========================  ==========
											newStorageAccountName  String                     newsaacct
											adminUsername          String                     WinAdmin1
											adminPassword          SecureString
											dnsNameForPublicIP     String                     contoso
											windowsOSVersion       String                     2012-R2-Datacenter
		Outputs           :


## Дальнейшие действия

Узнайте, как управлять созданной виртуальной машиной, просмотрев сведения в статье [Управление виртуальными машинами с помощью диспетчера ресурсов Azure и PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0114_2016-->