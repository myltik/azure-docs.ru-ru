---
title: "Доменные службы Azure Active Directory: устранение неполадок при настройке групп безопасности сети | Документация Майкрософт"
description: "Руководство по устранению неполадок для доменных служб Azure AD при настройке групп безопасности сети"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Узнайте, как устранить неполадки при настройке групп безопасности сети в доменных службах Azure AD



## <a name="aadds104-network-error"></a>AADDS104: ошибка сети

**Оповещение.** *Корпорация Майкрософт не может получить доступ к контроллерам этого управляемого домена. Это может произойти, если группа безопасности сети (NSG), настроенная в виртуальной сети, блокирует доступ к управляемому домену. Другая возможная причина заключается в том, что имеется определенный пользователем маршрут, который блокирует входящий трафик из Интернета.*

Наиболее частой причиной сетевых ошибок для доменных служб Azure AD могут выступать атрибуты, что свидетельствует о неправильных настройках NSG. Чтобы корпорация Майкрософт могла обслуживать и поддерживать управляемый домен, для доступа к [определенным портам](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) в подсети необходимо использовать группы безопасности сети (NSG). Если эти порти заблокированы, корпорация Майкрософт не сможет получить доступ к необходим ресурсам, что может привести к ухудшению работы службы. При создании NSG не закрывайте эти порты, чтобы не прерывать работу служб.

## <a name="sample-nsg"></a>Пример NSG
В следующей таблице описывается пример NSG, который будет обеспечивать защиту управляемого домена, что позволит корпорации Майкрософт отслеживать и обновлять сведения, а также управлять ими.

![пример NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Доменным службам Azure AD требуется неограниченный исходящий доступ. Рекомендуется не создавать дополнительных исходящих правил для NSG.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Добавление правил в группу безопасности сети с помощью портала Azure
Если не использовать PowerShell, то с помощью портала Azure в NSG можно вручную добавить отдельные правила. Выполните следующие действия, чтобы создать правила в группе безопасности сети.

1. Перейдите к странице [Группы безопасности сети](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) на портале Azure.
2. Из таблицы выберите группу NSG, связанную с доменом.
3. На левой панели навигации в меню "Настройки" щелкните **Правила безопасности для входящего трафика** или **Правила безопасности для исходящего трафика**.
4. Создайте правило, указав информацию и нажав кнопку **Добавить**. Последовательно выберите **ОК**.
5. Убедитесь, что правило создано. Для этого найдите его в таблице правил.


## <a name="create-a-default-nsg-using-powershell"></a>Создание NSG по умолчанию с помощью PowerShell

Предыдущие шаги можно использовать для создания NSG с помощью PowerShell, которая хранит все порты, необходимые для запуска доменных служб Azure AD, при этом запрещая любой другой нежелательный доступ.


Для этого разрешения требуется установить и запустить [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Подключитесь к каталогу Azure AD.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Войдите в подписку Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Создайте NSG с тремя правилами. Следующие сценарии определяют три правила для NSG, что позволит получить доступ к портам, необходимым для запуска доменных служб Azure AD. Затем сценарии создадут NSG с этими правилами. Кроме того, можно добавить дополнительное правило, если оно соответствует используемому формату.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. И наконец, сценарий связывают NSG с выбранной виртуальной сетью и подсетью.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Полный сценарий

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Эта NSG по умолчанию не блокирует доступ к порту, используемому для защищенного протокола LDAP. Чтобы узнать, как создать правило для порта, ознакомтесь с [этой статьей](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Свяжитесь с нами
Чтобы [оставить отзыв или обратиться за помощью](active-directory-ds-contact-us.md), свяжитесь с командой разработки продукта, отвечающей за доменные службы Azure Active Directory.
