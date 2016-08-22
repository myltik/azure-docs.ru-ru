.<properties
	pageTitle="Перемещение виртуальной машины Windows | Microsoft Azure"
	description="Из этой статьи вы узнаете, как перенести виртуальную машину Windows в другую подписку или группу ресурсов Azure в модели развертывания Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cynthn"/>

	


# Перемещение виртуальной машины Windows в другую подписку или группу ресурсов Azure 

В этой статье описано перемещение виртуальной машины Windows между группами ресурсов или подписками. Перемещение между подписками может понадобиться, если вы создали виртуальную машину в личной подписке и вам нужно переместить ее в корпоративную подписку, чтобы продолжить работу.

> [AZURE.NOTE] Во время перемещения будут созданы новые идентификаторы ресурсов. После перемещения виртуальной машины вам нужно будет обновить средства и сценарии, чтобы использовать новые идентификаторы ресурсов.


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## Использование PowerShell для перемещения виртуальной машины

Чтобы переместить виртуальную машину в другую группу ресурсов, необходимо убедиться, что также перемещаются все зависимые ресурсы. Чтобы воспользоваться командлетом Move-AzureRMResource, требуется указать имя и тип ресурса. Эти данные можно получить из командлета Find-AzureRMResource.

	Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
	

Чтобы переместить виртуальную машину, необходимо перенести несколько ресурсов. Можно просто создать для каждого ресурса отдельные переменные, а затем перечислить их. Этот пример содержит большую часть основных ресурсов для виртуальной машины, но при необходимости можно добавить дополнительные.

	$sourceRG = "<sourceResourceGroupName>"
	$destinationRG = "<destinationResourceGroupName>"
    
	$vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
	$diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
	$vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
	$nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
	$ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
	$nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
	
	Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Чтобы переместить ресурсы в другую подписку, добавьте параметр **-DestinationSubscriptionId**.

	Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Вам будет предложено подтвердить перемещение указанных ресурсов. Введите **Y**, чтобы подтвердить перемещение ресурсов.

  
## Дальнейшие действия

Вы можете перемещать разные типы ресурсов между группами ресурсов и подписками. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->