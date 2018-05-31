---
title: Создание шлюза приложений Azure, избыточного в пределах зоны
description: Сведения о том, как создать шлюз приложений, избыточный в пределах зоны, для которого не требуется отдельный шлюз в каждой зоне.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937707"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Создание шлюза приложений Azure, избыточного в пределах зоны (закрытая предварительная версия)

Платформа шлюза приложений, избыточного в пределах зоны, — это новый номер SKU, который обеспечивает множество улучшений по сравнению с существующим, включая следующие:
- **Устойчивость зоны** — развертывание шлюза приложений теперь может охватывать несколько зон доступности. Это устраняет необходимость в подготовке и закреплении отдельных экземпляров шлюза приложений в каждой зоне с помощью диспетчера трафика. Можно выбрать одну или несколько зон, где развертываются экземпляры шлюза приложений, и обеспечить устойчивость зон к сбоям. Внутренний пул приложений можно аналогичным образом распределить по зонам доступности.
- **Улучшения производительности**.
- **Статический виртуальный IP-адрес** — виртуальный IP-адрес шлюза приложений теперь поддерживает исключительно статический тип виртуального IP-адреса. Это гарантирует, что виртуальный IP-адрес, связанной со шлюзом приложений, не изменится после перезагрузки.
- **Интеграция хранилища ключей для SSL-сертификатов клиентов**.
- **Ускорение развертывания и обновления**.

> [!NOTE]
> Сейчас шлюз приложений, избыточный в пределах зоны, доступен в закрытой предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Поддерживаемые регионы

Сейчас шлюзы приложений, избыточные в пределах зоны, поддерживаются в регионе "Восточная часть США 2". Вскоре будут добавлены другие регионы.

## <a name="topologies"></a>Топологии

В текущем выпуске больше не нужно создавать шлюзы приложений с закрепленными зонами для достижения избыточности в пределах зоны. Одно и то же развертывание шлюза приложений теперь может охватывать несколько зон.

Для распределения по всем трем зонам требуются как минимум три экземпляра. Шлюз приложений распределяет экземпляры между зонами по мере добавления дополнительных экземпляров.

Предыдущие топологии с избыточностью в пределах зоны выглядели примерно так:

![Старая избыточная топология](media/create-zone-redundant/old-redundant.png)

Новая технология с избыточностью по зонам выглядит примерно так:

![Новая топология с избыточностью по зонам](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Развертывание

### <a name="prerequisites"></a>предварительным требованиям

Сейчас возможность избыточности в пределах зоны доступна только в закрытой предварительной версии. Чтобы попасть в число зарегистрированных подписчиков, отправьте почтовое сообщение по адресу appgwxzone@microsoft.com. Когда поступит подтверждение, вы сможете перейти к следующим шагам. Включите в письмо с запросом на регистрацию следующие сведения:

- Идентификатор подписки
- Имя региона
- приблизительное число необходимых шлюзов приложений.

### <a name="resource-manager-template-deployment"></a>Развертывание шаблонов Resource Manager

1. Убедитесь, что используемая подписка находится в списке зарегистрированных, как уже упоминалось выше.
2. Получив подтверждение, войдите в учетную запись Azure и выберите нужную подписку (при наличии более чем одной подписки). Не забудьте выбрать подписку, которая включена в список зарегистрированных.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Создание новой группы ресурсов

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Скачайте шаблоны из [GitHub](https://github.com/amitsriva/CrossZonePreview) и запомните папку, где они сохранены.
5. Создайте новое развертывание в созданной группе ресурсов. Перед развертыванием измените файл шаблона и параметров соответствующим образом. 

   На следующей схеме показано, где можно получить идентификатор клиента на портале Azure:

   ![Идентификатор клиента на портале](media/create-zone-redundant/tenant-id.png)

Шаблон создает следующие ресурсы:

- **Назначенное удостоверение пользователя.** Используется, чтобы включить экземпляры шлюза приложения для доступа к хранилищу ключей и получить сертификаты, сохраненные пользователем.
- **Key Vault.** Хранилище Key Vault, в котором хранится сертификат пользователя. Может использоваться уже существующее хранилище Key Vault.
- **Секрет.** Закрытый ключ, который хранится в Key Vault.
- **Политика доступа.** Политика, применяемая к Key Vault и использующая назначенное удостоверение пользователя. Она предоставляет развертываемым шлюзам приложений разрешение на получение сертификатов пользователей.
- **Общедоступный IP-адрес.** Зарезервированный IP-адрес, используемый для доступа к шлюзу приложений. Этот IP-адрес никогда не изменяется в течение жизненного цикла шлюза приложений.
- **Группа безопасности сети.** Группа NSG, автоматически созданная в подсети шлюза приложений, которая открывает трафик порта на настроенном прослушивателе. В новом SKU ее можно явно создать и администрировать. В предыдущим SKU эта группа NSG была неявной.
- **Виртуальная сеть.** Виртуальная сеть, в которой развертываются шлюз приложений и приложения.
- **Шлюз приложений.** Шлюз приложений с экземплярами в нужных зонах. По умолчанию выбраны все зоны (1, 2, 3). Имя SKU изменено на *Standard_v2*. Это имя SKU подлежит изменению. Сейчас конфигурация автомасштабирования содержит минимальное и максимальное значения, заданные как необходимое количество экземпляров. После включения автоматического масштабирования его можно настроить.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

1. Убедитесь, что используемая подписка включена в список зарегистрированных, как уже упоминалось в предварительных требованиях.
2. Скачайте и установите закрытый компонент MSI PowerShell из [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Загрузите ZIP-файл закрытого компонента PowerShell из расположения, указанного в электронном письме с подтверждением регистрации предварительной версии. Распакуйте файл на диск и запишите расположение.
4. Включив предварительную версию, загрузите ее модули перед входом в учетную запись:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Войдите в учетную запись Azure и выберите нужную подписку (при наличии более чем одной подписки). Не забудьте выбрать соответствующую подписку, которая включена в список зарегистрированных.
5. Выполните приведенные ниже команды, чтобы установить общие константы, которые содержат имена для большинства создаваемых сущностей. 

   При необходимости измените записи в соответствии со своими предпочтениями.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Создайте группу ресурсов:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Создайте назначенное удостоверение пользователя, чтобы предоставить доступ к шлюзу приложений для получения сертификатов из Key Vault.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Создайте экземпляр Key Vault для хранения сертификатов:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Отправьте сертификат в Key Vault в качестве секрета:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Назначьте политику доступа к Key Vault, используя назначенное удостоверение пользователя. Это позволит экземплярам шлюза приложений получить доступ к секрету Key Vault:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Создайте группу безопасности сети (NSG), чтобы разрешить доступ к подсети шлюза приложений на портах, где созданы новые прослушиватели.

    Например, для HTTP или HTTPS на портах по умолчанию NSG разрешит входящий доступ к портам 80, 443 и 65200–65535 для операций управления.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Создайте виртуальную сеть и подсети:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Создайте общедоступный IP-адрес зарезервированного или статического типа:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Создайте шлюз приложений:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Получите общедоступный IP-адрес созданного шлюза приложений:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

-  Будет ли мне выставлен счет за предварительную версию шлюза приложений?

   В режиме предварительной версии плата не взимается. Вам будет выставлен счет за ресурсы, отличные от шлюза приложений, такие как Key Vault, виртуальные машины и т. д.
- В каких регионах доступна предварительная версия?

   Сейчас предварительная версия доступна в регионе "Восточная часть США 2". Вскоре будут добавлены другие регионы.
- Поддерживается ли в предварительной версии портал?

   Нет, в режиме закрытой предварительной версии поддержка ограничена закрытым модулем PowerShell и шаблоном Resource Manager.

- Обеспечивается ли в закрытой предварительной версии поддержка при использовании в рабочей среде?

   Нет, в режиме закрытой предварительной версии не предоставляется соглашение об уровне обслуживания или техническая поддержка. Не рекомендуем использовать предварительную версию в рабочей среде. Поддержка в предварительной версии ограничена прямым взаимодействием с группой разработки продуктов с использованием псевдонима электронной почты.

- Как сообщать о проблемах?

   Закрытая предварительная версия может содержать ошибки и сопровождаться частыми развертываниями кода. Используйте псевдоним поддержки appgwxzone@microsoft.com для уведомления о проблемах и получения помощи.

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения


|Проблема  |Сведения  |
|---------|---------|---------|
|Выставление счетов     |Сейчас счета не выставляются.|
|Журналы диагностики (не метрики)     |Сейчас журналы производительности, а также запросов и ответов не отображаются.|
|Портал, CLI или пакет SDK     |Портал, CLI и пакет SDK не поддерживаются. Портал нельзя использовать для выдачи обновлений для предварительных версий шлюзов.|
|Иногда не удается обновить систему с помощью шаблона.     |Это происходит из-за состояния гонки с политикой доступа к Key Vault.|После создания Key Vault и назначенного удостоверения пользователя их можно удалить из шаблона. В шаблоне требуются только ссылки на секрет и удостоверение.|
|Автомасштабирование     |Сейчас автомасштабирование не поддерживается.|
|WAF     |Сейчас WAF не поддерживается.|
|Предоставляемые пользователем сертификаты и динамические виртуальные IP-адреса     |Эти возможности в новой модели не поддерживаются. Используйте Key Vault для хранения сертификатов и статические виртуальные IP-адреса.|
|Одна и та же подсеть для старой и предварительной версий шлюза приложений     |Подсеть с существующим шлюзом приложений (старой модели) нельзя использовать для закрытой предварительной версии.|
|HTTP/2, режим FIPS, WebSocket, функция "Веб-приложения Azure" в качестве серверной части     |Сейчас не поддерживаются. |


## <a name="support-and-feedback"></a>Поддержка и обратная связь

Чтобы получить поддержку и оставить отзыв, обращайтесь по адресу appgwxzone@microsoft.com. Группа разработки шлюза приложений будет рада вашим отзывам для усовершенствования продукта и при необходимости предоставит техническую поддержку.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о других возможностях шлюза приложений:

- [What is Azure Application Gateway?](overview.md) (Что собой представляет шлюз приложений Azure)