---
title: 'Доменные службы Azure Active Directory: устранение неполадок при настройке групп безопасности сети | Документация Майкрософт'
description: Руководство по устранению неполадок для доменных служб Azure AD при настройке групп безопасности сети
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: 2336277a960925a92af3578850453ba6ae78abda
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201272"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Устранение неполадок недопустимой конфигурации сети для управляемого домена
Эта статья поможет вам определить и устранить ошибки конфигурации сети, которые приводят к появлению следующего оповещения.

## <a name="alert-aadds104-network-error"></a>Оповещение AADDS104: ошибка сети
**Оповещение.** *Корпорация Майкрософт не может получить доступ к контроллерам этого управляемого домена. Это может произойти, если группа безопасности сети (NSG), настроенная в виртуальной сети, блокирует доступ к управляемому домену. Другая возможная причина заключается в том, что имеется определенный пользователем маршрут, который блокирует входящий трафик из Интернета.*

Наиболее частой причиной сетевых ошибок для доменных служб Azure AD являются неправильные конфигурации NSG. Группа безопасности сети (NSG), настроенная для виртуальной сети, должна разрешать доступ к [определенным портам](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Если эти порты заблокированы, корпорация Майкрософт не может отслеживать или обновлять управляемый домен. Кроме того, это негативно повлияет на синхронизацию между вашим каталогом Azure AD и управляемым доменом. Не закрывайте эти порты при создании NSG, чтобы не прерывать работу службы.

### <a name="checking-your-nsg-for-compliance"></a>Проверка группы безопасности сети на соответствие

1. Перейдите к странице [Группы безопасности сети](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) на портале Azure.
2. В таблице выберите NSG, связанную с подсетью, в которой включен управляемый домен.
3. На панели слева в разделе **Параметры** щелкните **Правила безопасности для входящего трафика**.
4. Проверьте настроенные правила и определите, какие правила блокируют доступ к [этим портам](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services).
5. Измените NSG, чтобы обеспечить соответствие требованиям, удалив правило, добавив правило или создав NSG. Шаги по [добавлению правила](#add-a-rule-to-a-network-security-group-using-the-azure-portal) или [созданию соответствующей требованиям NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell) представлены ниже.

## <a name="sample-nsg"></a>Пример NSG
В следующей таблице описывается пример NSG, который будет обеспечивать защиту управляемого домена, что позволит корпорации Майкрософт отслеживать и обновлять сведения, а также управлять ими.

![пример NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Доменным службам Azure AD требуется неограниченный исходящий доступ из виртуальной сети. Не рекомендуется создавать какое-либо дополнительное правило NSG, которое ограничивает исходящий доступ для виртуальной сети.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Добавление правила в группу безопасности сети с помощью портала Azure
Если не использовать PowerShell, то с помощью портала Azure в NSG можно вручную добавить отдельные правила. Выполните следующие действия, чтобы создать правила в группе безопасности сети.

1. Перейдите к странице [Группы безопасности сети](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) на портале Azure.
2. В таблице выберите NSG, связанную с подсетью, в которой включен управляемый домен.
3. На левой панели навигации в меню **Настройки** щелкните **Правила безопасности для входящего трафика** или **Правила безопасности для исходящего трафика**.
4. Создайте правило, указав информацию и нажав кнопку **Добавить**. Последовательно выберите **ОК**.
5. Убедитесь, что правило создано. Для этого найдите его в таблице правил.


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>Создание NSG для доменных служб Azure AD с помощью PowerShell
Эта NSG настраивается таким образом, чтобы разрешить входящий трафик для портов, необходимых для работы доменных служб Azure AD, и запретить другой нежелательный входящий доступ.

[Предварительные требования: установите и настройте Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json). Выполните инструкции по **установке модуля Azure PowerShell и его подключению к вашей подписке Azure**.

>[!TIP]
> Мы рекомендуем использовать последнюю версию модуля Azure PowerShell. Если у вас уже установлен модуль Azure PowerShell более старой версии, обновите его до последней версии.
>

Чтобы создать NSG с помощью PowerShell, выполните следующие действия.
1. Войдите в подписку Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Создайте NSG с тремя правилами. Следующие сценарии определяют три правила для NSG, что позволит получить доступ к портам, необходимым для запуска доменных служб Azure AD. Затем сценарии создадут NSG с этими правилами. Используйте этот же формат, чтобы добавить дополнительные правила, разрешающие другой входящий трафик, если это требуется для рабочих нагрузок, развернутых в виртуальной сети.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. И наконец, выполняется связывание NSG с выбранными виртуальной сетью и подсетью.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Полный сценарий для создания и применения NSG для доменных служб Azure AD
>[!TIP]
> Мы рекомендуем использовать последнюю версию модуля Azure PowerShell. Если у вас уже установлен модуль Azure PowerShell более старой версии, обновите его до последней версии.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>Требуется помощь?
Чтобы [оставить отзыв или обратиться за помощью](active-directory-ds-contact-us.md), свяжитесь с командой разработки продукта, отвечающей за доменные службы Azure Active Directory.
