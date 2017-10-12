---
title: "Защита служб IIS с помощью SSL-сертификатов в Azure | Документация Майкрософт"
description: "Узнайте, как защитить веб-сервер IIS с помощью SSL-сертификатов на виртуальной машине Windows в Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Защита веб-сервера IIS с помощью SSL-сертификатов на виртуальной машине Windows в Azure
Чтобы защитить веб-серверы, можно использовать сертификат Secure Sockets Later (SSL) для шифрования веб-трафика. SSL-сертификаты могут храниться в Azure Key Vault и разрешать безопасное развертывание сертификатов на виртуальных машинах Windows в Azure. Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание Azure Key Vault
> * создать или передать сертификат в Key Vault;
> * Создание виртуальной машины и установка веб-сервера IIS
> * Внедрение сертификата в виртуальную машину и настройка IIS с помощью SSL-привязки

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="overview"></a>Обзор
Azure Key Vault защищает криптографические ключи и секреты, в том числе сертификаты или пароли. Key Vault помогает оптимизировать управление сертификатами и позволяет контролировать ключи, которые предоставляют доступ к этим сертификатам. Можно создать самозаверяющий сертификат в Key Vault или передать существующий доверенный сертификат.

Вместо того чтобы использовать образ виртуальной машины, включающий встроенные сертификаты, можно внедрить сертификаты в работающую виртуальную машину. Этот процесс гарантирует установку на веб-сервер самых последних сертификатов во время развертывания. Если вы обновляете или заменяете сертификат, вам не нужно создавать новый пользовательский образ виртуальной машины. Последние сертификаты внедряются автоматически при создании дополнительных виртуальных машин. При этом сертификат никогда не покидает платформу Azure и не отображается в скрипте, журнале командной строки или шаблоне.


## <a name="create-an-azure-key-vault"></a>Создание Azure Key Vault
Прежде чем создать Key Vault и сертификаты, выполните командлет [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup), чтобы создать группу ресурсов. В следующем примере создается имя группы ресурсов *myResourceGroupSecureWeb* в расположении *восточная часть США*:

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Чтобы создать Key Vault, используйте командлет [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). У каждого Key Vault должно быть уникальное имя в нижнем регистре. Замените `<mykeyvault>` в следующем примере собственным уникальным именем Key Vault.

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Создание сертификата и его сохранение в Key Vault
Для использования в рабочей среде импортируйте действительный сертификат, подписанный доверенным поставщиком, выполнив командлет [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). В следующем примере показано, как с помощью командлета [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) создать самозаверяющий сертификат, использующий политику сертификата из [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Теперь вы можете создать виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Следующий пример создает необходимые компоненты виртуальной сети, конфигурацию операционной системы, а затем виртуальную машину *myVM*.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Создание виртуальной машины может занять несколько минут. На последнем шаге с помощью расширения пользовательских сценариев Azure устанавливается веб-сервер IIS. Для этого используется командлет [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Добавление сертификата на виртуальную машину из Key Vault
Чтобы добавить сертификат из Key Vault на виртуальную машину, получите идентификатор сертификата с помощью командлета [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Добавьте сертификат на виртуальную машину с помощью командлета [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Настройка IIS для использования сертификата
Обновите конфигурацию IIS с помощью расширения пользовательских сценариев и командлета [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Это обновление применяет внедренный из Key Vault в IIS сертификат и настраивает веб-привязку:

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Тестирование защищенного веб-приложения
Получите общедоступный IP-адрес своей виртуальной машины с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее `myPublicIP`.

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

Теперь можно открыть веб-браузер и ввести в адресной строке `https://<myPublicIP>`. Чтобы принять предупреждение системы безопасности, если используется самозаверяющий сертификат безопасности, выберите **Сведения**, а затем — **Перейти на веб-страницу**:

![Принятие предупреждения о безопасности веб-браузера](./media/tutorial-secure-web-server/browser-warning.png)

На экране отобразится защищенный веб-сайт IIS, как в показано следующем примере:

![Просмотр работающего защищенного сайта IIS](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Дальнейшие действия

С помощью этого руководства вы защитили веб-сервер IIS SSL-сертификатом, который хранится в Azure Key Vault. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание Azure Key Vault
> * создать или передать сертификат в Key Vault;
> * Создание виртуальной машины и установка веб-сервера IIS
> * Внедрение сертификата в виртуальную машину и настройка IIS с помощью SSL-привязки

Чтобы увидеть предварительно созданные примеры скриптов виртуальной машины, перейдите по ссылке ниже.

> [!div class="nextstepaction"]
> [Примеры скриптов для виртуальной машины Windows](./powershell-samples.md)