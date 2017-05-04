---
title: "Настройка пиринговой связи между виртуальными сетями Azure с помощью шаблона | Документация Майкрософт"
description: "Настройка пиринга виртуальных сетей с использованием шаблона Azure Resource Manager."
services: virtual-network
documentationcenter: 
author: narayanannamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 75f8d10e-23e8-44bd-9972-aab74048cf38
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 86eec91025ae8ae553fa1f76a687e440784e7855
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-peering-using-an-azure-resource-manager-template"></a>Настройка пиринга виртуальных сетей с использованием шаблона Azure Resource Manager
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Ниже описывается, как настроить пиринг виртуальных сетей с использованием шаблонов Resource Manager.

1. Если вы ранее не использовали Azure PowerShell, следуйте указаниям в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview) до этапа входа в Azure и выбора подписки.

   > [!NOTE]
   > Командлет PowerShell для управления пирингом виртуальных сетей включен в выпуск [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. Ниже показано определение пирингового соединения между виртуальными сетями (от VNet1 к VNet2) на основе описанного выше сценария. Скопируйте приведенное ниже содержимое и вставьте его в файл VNetPeeringVNet1.json.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
    ```
3. Ниже показано определение пирингового соединения между виртуальными сетями (от VNet2 к VNet1) на основе описанного выше сценария.  Скопируйте приведенное ниже содержимое и вставьте его в файл VNetPeeringVNet2.json.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
    ```
    Как видно в шаблоне выше, вы можете изменить несколько свойств пиринга виртуальных сетей.
   
   | Параметр | Описание | значение по умолчанию |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Определяет, будет ли адресное пространство пиринговой виртуальной сети включено как часть тега Virtual_network. |Да |
   | AllowForwardedTraffic |Определяет, будет ли приниматься трафик, поступающий не из пиринговой виртуальной сети. |Нет |
   | AllowGatewayTransit |Разрешает пиринговой виртуальной сети использовать шлюз вашей виртуальной сети. |Нет |
   | UseRemoteGateways |Необходимо использовать шлюз вашей пиринговой виртуальной сети. Этот шлюз должен быть настроен. Кроме того, должен быть выбран параметр AllowGatewayTransit. Этот параметр нельзя использовать, если вы уже настроили шлюз. |Нет |
   
    Каждое пиринговое соединение между виртуальными сетями имеет описанные выше свойства. Например, вы можете указать для параметра AllowVirtualNetworkAccess значение True, чтобы настроить пиринговое соединение от VNet1 к VNet2, или значение False, чтобы настроить пиринговое соединение в другом направлении.
4. Чтобы развернуть файл шаблона, выполните командлет `New-AzureRmResourceGroupDeployment` для создания или обновления развертывания. Дополнительные сведения об использовании шаблонов Resource Manager см. в [этой статье](../azure-resource-manager/resource-group-template-deploy.md).

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
    ```

   > [!NOTE]
   > Замените имя группы ресурсов и файла шаблона соответствующими значениями.
   > 
   > 
   
    Ниже приведен пример на основе предыдущего сценария:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
    ```

    Возвращаемые выходные данные:
   
        DeploymentName        : VNetPeeringVNet1
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
    ```

    Возвращаемые выходные данные:
   
        DeploymentName        : VNetPeeringVNet2
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. Завершив развертывание, вы можете выполнить следующий командлет, чтобы просмотреть состояние пиринга.

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
    ```

    Возвращаемые выходные данные:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : VNet101
        VirtualNetworkName    : VNet1
        ProvisioningState        : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Когда мы настроим пиринг для нашего сценария, вы сможете устанавливать подключение между любыми виртуальными машинами, входящими в эти две виртуальные сети. По умолчанию свойство `AllowVirtualNetworkAccess` имеет значение *True*. Поэтому при пиринге виртуальных сетей будут подготовлены соответствующие списки управления доступом, обеспечивая обмен данными между этими виртуальными сетями. Но вы также можете заблокировать возможность подключения (например, к определенной подсети или виртуальной машине) с помощью правил группы безопасности сети (NSG), реализуя более точное управление доступом между двумя виртуальными сетями. См. дополнительные сведения о [группах безопасности сети](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Ниже описано, как настроить пиринг между виртуальными сетями в рамках подписок.

1. Войдите в Azure, используя учетную запись привилегированного пользователя A в подписке A, и запустите следующий командлет:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
    ```

    Это не является обязательным требованием. Пиринг можно настроить, даже если пользователи отдельно отправляют запросы о пиринговой связи для своих виртуальных сетей. Нужно только, чтобы эти запросы совпадали. Добавление привилегированного пользователя из другой виртуальной сети в качестве пользователя локальной сети упрощает настройку.
2. Войдите в Azure, используя учетную запись привилегированного пользователя В для подписки В, и запустите следующий командлет:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
    ```

    > [!IMPORTANT]
    > Если вы создаете пиринговую связь между двумя виртуальными сетями, созданными по модели развертывания с помощью Azure Resource Manager, переходите к следующим действиям из этого раздела. Если две виртуальные сети созданы с помощью разных моделей развертывания, пропустите оставшиеся шаги в этом разделе и выполните действия, описанные в [этом разделе](#x-model).

3. Затем в рамках сеанса пользователя А выполните следующий командлет:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all
    ```
   
    Используется следующий формат JSON:

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
    ```
4. Затем в рамках сеанса пользователя В выполните следующий командлет:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
    ```

    Используется следующий формат JSON:

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
    ```

    Когда в нашем примере пиринг будет настроен, вы сможете устанавливать подключение между любыми виртуальными машинами, входящими в эти две виртуальные сети, в разных подписках.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. В этом сценарии можно развернуть следующий шаблон, чтобы настроить пиринг виртуальных сетей. Необходимо присвоить свойству `AllowForwardedTraffic` значение *True*, чтобы разрешить виртуальному сетевому устройству в пиринговой сети отправлять и принимать трафик.

    Следующий файл JSON является содержимым шаблона для настройки пирингового соединения между сетями HubVNet и VNet1. Обратите внимание, что свойству AllowForwardedTraffic присвоено значение false.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
    ```
2. Следующий файл JSON является содержимым шаблона для настройки пирингового соединения между сетями VNet1 и HubVnet. Обратите внимание, что свойству AllowForwardedTraffic присвоено значение true.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }
    ```
3. Настроив пиринговую связь, изучите эту [статью](virtual-network-create-udr-arm-ps.md). Из нее вы узнаете, как настроить пользовательский маршрут для перенаправления трафика VNet1 через виртуальное устройство. Указывая адрес следующего прыжка в маршруте, вы можете выбрать IP-адрес виртуального устройства в пиринговой виртуальной сети HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Чтобы настроить пиринг между виртуальными сетями из разных моделей развертывания, выполните следующие действия:

1. Если вы создаете пиринговую связь между виртуальными сетями, развернутыми в *одной* подписке с помощью разных моделей развертывания, перейдите к шагу 2. Функция создания пиринговой связи между виртуальными сетями, развернутыми с помощью разных моделей развертывания в *разных* подписках, доступна в режиме **предварительной версии**. В отличие от функций в общедоступных версиях, функции в предварительной версии не обеспечивают аналогичный уровень надежности и не подкреплены соглашением об уровне обслуживания. Если вы создаете пиринговую связь между виртуальными сетями, развернутыми в разных подписках с помощью разных моделей развертывания, вам нужно сначала выполнить следующие задачи.
    - Зарегистрируйте функцию предварительной версии в подписке Azure. Для этого введите следующие команды в PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` и `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`.
    - Выполните шаги 1–2 из раздела этой статьи, посвященного [пиринговой связи между разными подписками](#x-sub).
2. Ниже показано определение пирингового соединения между виртуальными сетями (от VNET1 к VNET2) в этом сценарии. Для настройки пиринга между классической виртуальной сетью и виртуальной сетью Azure Resource Manager требуется только одно соединение.

    Укажите идентификатор подписки классической виртуальной сети или VNET2 и измените MyResouceGroup на имя группы соответствующего ресурса.

    ```json
       {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [

            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNET1/LinkToVNET2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"
        }

            }
            }
        ]
        }
    ```
3. Чтобы развернуть файл шаблона, выполните следующий командлет для создания или обновления развертывания.

    ```powershell
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
    ```

    Возвращаемые выходные данные:
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
4. После успешного завершения развертывания можно выполнить следующий командлет, чтобы просмотреть состояние пиринга.

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
    ```

    Возвращаемые выходные данные:
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Когда пиринг будет настроен между классической виртуальной сетью и виртуальной сетью Resource Manager, вы сможете подключать любую виртуальную машину в VNET1 к виртуальной машине в VNET2 и наоборот.


