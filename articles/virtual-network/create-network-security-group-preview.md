---
title: Фильтрация сетевого трафика с помощью групп безопасности сети и приложений Azure (предварительная версия) | Документы Майкрософт
description: Сведения о создании групп безопасности сети и приложений (предварительная версия) для ограничения типов входящего и исходящего сетевого трафика для виртуальных машин.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2018
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Фильтрация сетевого трафика с помощью групп безопасности сети и приложений (предварительная версия)

В этом руководстве описывается создание групп безопасности сети для фильтрации входящего и исходящего сетевого трафика для групп виртуальных машин с группами безопасности приложений. Дополнительные сведения о группах безопасности сети и приложений см. в статье [Обзор безопасности](security-overview.md). 

В следующих разделах приведены шаги по созданию групп безопасности сети с помощью интерфейса командной строки Azure ([Azure CLI](#azure-cli)) и [Azure PowerShell](#powershell). Результат использования любого из этих инструментов для создания групп безопасности сети одинаков. Щелкните нужную ссылку, чтобы перейти в соответствующий раздел этого руководства. 

Эта статья содержит инструкции по созданию групп безопасности сети с использованием модели развертывания Resource Manager. Мы рекомендуем использовать эту модель при создании групп безопасности сети. Если вам требуется создать группу безопасности сети в классической модели, см. статью [Как создать группы безопасности сети (классические)](virtual-networks-create-nsg-classic-ps.md). Если вы не знакомы с моделями развертывания Azure, см. статью [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> В этом руководстве используются компоненты группы безопасности сети, которые доступны в предварительной версии. Для функций предварительной версии не обеспечивается такой же уровень доступности и надежности, как для общедоступного выпуска. Если вы хотите развернуть группы безопасности сети с помощью компонентов только общедоступного выпуска, см. статью [Создание группы безопасности сети](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Команды Azure CLI одинаковы для ОС Windows, Linux или macOS. Тем не менее существуют различия в сценариях между оболочками операционных систем. Сценарии ниже выполняется в оболочке Bash. 

1. [Установить и настроить Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Убедитесь, вы используете Azure CLI версии 2.0.18 или более поздней, введя команду `az --version`. Если это не так, установите последнюю версию.
3. Войдите в Azure с помощью команды `az login`.
4. Зарегистрируйтесь для использования предварительной версии, выполнив следующие команды:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Убедитесь, что регистрация прошла успешно, выполнив следующую команду:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > Регистрация может занять не более часа. Не продолжайте процедуру, пока в столбце *state* выходных данных предыдущей команды не появится значение **Registered**. Если вы продолжите до завершения регистрации, вам не удастся корректно выполнить процедуру.

6. Выполните следующий сценарий bash, чтобы создать группу ресурсов:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Создайте три группы безопасности приложений, по одной для каждого типа сервера:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Создайте группу безопасности сети:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Создайте правила безопасности в NSG, настроив группы безопасности приложений в качестве цели:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Создайте виртуальную сеть: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Свяжите группу безопасности сети с подсетью в виртуальной сети:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Создайте три сетевых интерфейса, по одному для каждого типа сервера: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Только соответствующее правило безопасности, созданное на шаге 9, применяется к сетевому интерфейсу в зависимости от группы безопасности приложений, в которую он входит. Например, для *myNic2* действует только правило *AppRule*, так как сетевой интерфейс входит в группу безопасности приложений *AppServers* и правило указывает группу *AppServers* в качестве места назначения. Правила *WebRule* и *DatabaseRule* не применяются к *myNic2*, так как сетевой интерфейс не входит в группы безопасности приложений *WebServers* и *DatabaseServers*. Но для *myNic1* действуют правила *WebRule* и *AppRule*, потому что сетевой интерфейс *myNic1* входит в группы безопасности приложений *WebServers* и *AppServers* и правила указывают группы *WebServers* и *AppServers* в качестве мест назначения. 

13. Создайте одну виртуальную машину для каждого типа сервера, подключив соответствующий сетевой интерфейс к каждой виртуальной машине. В этом примере мы создаем виртуальные машины Windows, однако можно заменить *win2016datacenter* на *UbuntuLTS*, чтобы создать виртуальные машины Linux.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Необязательно.** Удалите ресурсы, созданные в этом руководстве, с помощью шагов, описанных в разделе [Удаление ресурсов](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Установите и настройте [PowerShell](/powershell/azure/install-azurerm-ps).
2. Убедитесь, что установлен модуль AzureRM версии 4.4.0 или более поздней. Текущую установленную версию можно узнать с помощью команды `Get-Module -ListAvailable AzureRM`. Если требуется установка или обновление, установите последнюю версию модуля AzureRM из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. В сеансе PowerShell войдите в Azure с использованием [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) с помощью команды `login-azurermaccount`.
4. Зарегистрируйтесь для использования предварительной версии, выполнив следующие команды:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Убедитесь, что регистрация прошла успешно, выполнив следующую команду:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > Регистрация может занять не более часа. Не продолжайте процедуру, пока в столбце *RegistrationState* (Состояние регистрации) выходных данных предыдущей команды не появится значение **Registered** (Зарегистрировано). Если вы продолжите до завершения регистрации, вам не удастся корректно выполнить процедуру.
        
6. Создайте группу ресурсов:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Создайте три группы безопасности приложений, по одной для каждого типа сервера:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Создайте правила безопасности для каждого типа сервера.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Создайте группу безопасности сети:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Создайте конфигурацию подсети и свяжите с ней группу безопасности сети:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Создайте виртуальную сеть: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Создайте три сетевых интерфейса, по одному для каждого типа сервера. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Только соответствующее правило безопасности, созданное на шаге 8, применяется к сетевому интерфейсу в зависимости от группы безопасности приложений, в которую он входит. Например, для *myNic2* действует только правило *AppRule*, так как сетевой интерфейс входит в группу безопасности приложений *AppServers* и правило указывает группу *AppServers* в качестве места назначения. Правила *WebRule* и *DatabaseRule* не применяются к *myNic2*, так как сетевой интерфейс не входит в группы безопасности приложений *WebServers* и *DatabaseServers*. Но для *myNic1* действуют правила *WebRule* и *AppRule*, потому что сетевой интерфейс *myNic1* входит в группы безопасности приложений *WebServers* и *AppServers* и правила указывают группы *WebServers* и *AppServers* в качестве мест назначения. 

13. Создайте одну виртуальную машину для каждого типа сервера, подключив соответствующий сетевой интерфейс к каждой виртуальной машине. В этом примере создаются виртуальные машины Windows, однако перед выполнением сценария можно изменить *-Windows* на *-Linux*, *MicrosoftWindowsServer* на *Canonical*, *WindowsServer* на *UbuntuServer* и *2016-Datacenter* на *14.04.2-LTS*, чтобы создать виртуальные машины Linux.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Необязательно.** Удалите ресурсы, созданные в этом руководстве, с помощью шагов, описанных в разделе [Удаление ресурсов](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Удаление сетевого адаптера из группы безопасности приложений
Если сетевой интерфейс удален из группы безопасности приложений, к нему не применяется ни одно из правил, указывающих группу безопасности приложений.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы удалить *myNic3* из всех групп безопасности приложений, введите следующую команду:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Чтобы удалить *myNic3* из всех групп безопасности приложений, введите следующие команды:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Удаление ресурсов

По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата: При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

### <a name="delete-portal"></a>Портал Azure

1. В поле поиска на портале введите **myResourceGroup**. В результатах поиска щелкните **myResourceGroup**.
2. В колонке **myResourceGroup** щелкните значок **Удалить**.
3. Чтобы подтвердить удаление, введите **myResourceGroup** в поле **Введите имя группы ресурсов**, а затем щелкните **Удалить**.

### <a name="delete-cli"></a>Интерфейс командной строки Azure

В окне сеанса интерфейса командной строки введите следующую команду:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

В окне сеанса PowerShell введите следующую команду:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

