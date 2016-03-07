<properties 
   pageTitle="Как создавать группы безопасности сети в диспетчере ресурсов Azure с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как создавать и развертывать группы безопасности сети в диспетчере ресурсов Azure с помощью PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="telmos" />

# Как создавать группы безопасности сети в диспетчере ресурсов с помощью PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать группы безопасности сети с помощью классической модели развертывания](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Для приведенных ниже примеров команд PowerShell требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), нажмите **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

## Как создавать сетевую группу безопасности для подсети переднего плана
Чтобы создать сетевую группу безопасности под названием *NSG-FrontEnd* по описанному выше сценарию, выполните указанные ниже действия.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Войдите в Azure и выберите подписку.

3. Создайте правило безопасности, разрешающее доступ из Интернета к порту 3389.

		$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. Создайте правило безопасности, разрешающее доступ из Интернета к порту 80.

		$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. Добавьте созданные выше правила в новую группу безопасности сети под названием **NSG-FrontEnd**.

		$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

6. Проверьте правила, созданные в группе безопасности сети.

		$nsg

	В выходных данных отображаются только правила безопасности:

		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

6. Свяжите созданную выше группу безопасности сети с подсетью *FrontEnd*.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	В выходных данных отображаются только параметры подсети *FrontEnd* (обратите внимание на значение свойства **NetworkSecurityGroup**):

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

>[AZURE.WARNING] Результаты выполнения представленной выше команды отображают содержимое объекта конфигурации виртуальной сети, который существует только на том компьютере, где выполняется PowerShell. Чтобы сохранить эти параметры в Azure, выполните командлет `Set-AzureRmVirtualNetwork`.

7. Сохраните новые параметры виртуальной сети в Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	В выходных данных отображается только часть, относящаяся к группе безопасности сети:

		"NetworkSecurityGroup": {
		  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		}

## Как создавать сетевую группу безопасности для внутренней подсети
Чтобы создать сетевую группу безопасности под названием *NSG-BackEnd* по описанному выше сценарию, выполните следующие действия.

1. Создайте правило безопасности, которое разрешает доступ из подсети переднего плана к порту 1433 (порту, используемому по умолчанию в SQL Server).

		$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. Создайте правило безопасности, блокирующее доступ к Интернету.

		$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. Добавьте созданные выше правила в новую группу безопасности сети под названием **NSG-BackEnd**.

		$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

6. Свяжите созданную выше группу безопасности сети с подсетью *BackEnd*.

		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	В выходных данных отображаются только параметры подсети *BackEnd* (обратите внимание на значение свойства **NetworkSecurityGroup**):

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

7. Сохраните новые параметры виртуальной сети в Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

<!---HONumber=AcomDC_0224_2016-->