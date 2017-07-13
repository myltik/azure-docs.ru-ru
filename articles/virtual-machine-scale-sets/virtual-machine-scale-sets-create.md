---
title: "Создание масштабируемого набора виртуальных машин Azure | Документация Майкрософт"
description: "Создание и развертывание масштабируемого набора виртуальных машин Linux или Windows в Azure с помощью Azure CLI, PowerShell, шаблона или Visual Studio."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 05/23/2017
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 736918ea310f276d961fa396f719b2b7809f0c0f
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017

---

<a id="create-and-deploy-a-virtual-machine-scale-set" class="xliff"></a>

# Создание и развертывание масштабируемого набора виртуальных машин
Наборы масштабирования виртуальных машин позволяют легко развертывать идентичные виртуальные машины (ВМ) в виде набора и управлять ими. Масштабируемые наборы обеспечивают высокую степень масштабируемости и персонализацию уровня вычислений для гипермасштабируемых приложений. Кроме того, они поддерживают образы платформ Windows и Linux, а также пользовательские образы и расширения. Дополнительные сведения о масштабируемых наборах см. в разделе [Общие сведения о масштабируемых наборах виртуальных машин в Azure](virtual-machine-scale-sets-overview.md).

В этом руководстве показано, как создать масштабируемый набор виртуальных машин, **не используя** портал Azure. Сведения об использовании портала Azure см. в разделе [Как создать масштабируемый набор виртуальных машин с помощью портала Azure](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Дополнительные сведения о ресурсах Azure Resource Manager см. в статье [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../azure-resource-manager/resource-manager-deployment-model.md).

<a id="sign-in-to-azure" class="xliff"></a>

## Вход в Azure

Если для создания масштабируемого набора вы используете Azure CLI 2.0 или Azure PowerShell, необходимо сначала войти в подписку.

Дополнительные сведения о том, как выполнить установку, настройку и вход в Azure с помощью Azure CLI или PowerShell, см. в разделах [Getting Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) (Приступая к работе с Azure CLI 2.0) или [Overview of Azure PowerShell](/powershell/azure/overview) (Обзор Azure PowerShell).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

<a id="create-a-resource-group" class="xliff"></a>

## Создание группы ресурсов

Необходимо сначала создать группу ресурсов, с которой будет связан масштабируемый набор виртуальных машин.

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

<a id="create-from-azure-cli" class="xliff"></a>

## Создание с помощью Azure CLI

Создать масштабируемый набор виртуальных машин с помощью Azure CLI достаточно просто. Если не указать значения по умолчанию, они будут заданы автоматически. Например, если не указать данные виртуальной сети, она будет создана автоматически. Если пропустить приведенные ниже компоненты, они будут созданы автоматически: 
- подсистема балансировки нагрузки;
- виртуальную сеть;
- общедоступный IP-адрес.

При выборе образа виртуальной машины, который вы хотите использовать в масштабируемом наборе виртуальных машин, доступно несколько вариантов.

- URN  
Идентификатор ресурса:  
**Win2012R2Datacenter**.

- Псевдоним URN  
Понятное имя URN:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**.

- Идентификатор настраиваемого ресурса  
Путь к ресурсу Azure:  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**.

- Веб-ресурс  
Путь к универсальному коду ресурса (URI) в формате HTTP:  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**.

>[!TIP]
>Список доступных образов можно получить с помощью команды `az vm image list`.

Чтобы создать масштабируемый набор виртуальных машин, необходимо указать следующее:

- Группа ресурсов 
- Имя
- образ операционной системы;
- данные аутентификации. 
 
В следующем примере создается простой масштабируемый набор виртуальных машин (эта операция может занять несколько минут).

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

После завершения выполнения команды будет создан масштабируемый набор виртуальных машин. Может потребоваться получить IP-адрес виртуальной машины, чтобы подключиться к ней. С помощью приведенной ниже команды можно получить много различных сведений о виртуальной машине (включая IP-адрес). 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

<a id="create-from-powershell" class="xliff"></a>

## Создание с помощью PowerShell

PowerShell сложнее в использовании, чем Azure CLI. Azure CLI предоставляет значения по умолчанию для сетевых ресурсов (таких как подсистемы балансировки нагрузки, IP-адреса и виртуальные сети), а PowerShell — нет. Также с помощью PowerShell немного сложнее указать образ. Вы можете получить образы с помощью следующих командлетов:

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

Эти командлеты можно использовать в последовательности. Ниже приведен пример того, как получить все образы для региона **Западная часть США 2**, в котором расположен издатель **microsoft**.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

Ниже приведен рабочий процесс создания масштабируемого набора виртуальных машин.

1. Создайте объект конфигурации, содержащий сведения о масштабируемом наборе.
2. Укажите базовый образ операционной системы.
3. Настройте параметры операционной системы: аутентификацию, префикс имени виртуальной машины, пользователя и пароль.
4. Настройте сеть.
5. Создайте масштабируемый набор.

В этом примере создается простой масштабируемый набор с двумя экземплярами для компьютера под управлением Windows Server 2016.

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

<a id="create-from-a-template" class="xliff"></a>

## Создание с помощью шаблона

Вы можете развернуть масштабируемый набор виртуальных машин с помощью шаблона Azure Resource Manager. Можно создать собственный шаблон или использовать шаблон из [репозитория шаблонов](https://azure.microsoft.com/resources/templates/?term=vmss). Эти шаблоны можно развернуть непосредственно в подписке Azure.

>[!NOTE]
>Чтобы создать собственный шаблон, следует создать текстовый JSON-файл. Общие сведения о способах создания и настройки шаблонов см. в разделе [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Пример шаблона доступен на [сайте GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set). Дополнительные сведения о том, как создать и использовать этот пример, см. в разделе [Подробнее о шаблонах масштабируемых наборов виртуальных машин](.\virtual-machine-scale-sets-mvss-start.md).

<a id="create-from-visual-studio" class="xliff"></a>

## Создание с помощью Visual Studio

С помощью Visual Studio можно создать проект группы ресурсов Azure и добавить в него шаблон масштабируемого набора виртуальных машин. Его можно импортировать с сайта GitHub или из коллекции веб-приложений Azure. Кроме того, автоматически создается сценарий PowerShell для развертывания. Дополнительные сведения см. в разделе [Как создать масштабируемый набор виртуальных машин с помощью Visual Studio](virtual-machine-scale-sets-vs-create.md).

<a id="create-from-the-azure-portal" class="xliff"></a>

## Создание с помощью портала Azure

Портал Azure удобен для быстрого создания масштабируемого набора. Дополнительные сведения см. в разделе [Как создать масштабируемый набор виртуальных машин с помощью портала Azure](virtual-machine-scale-sets-portal-create.md).

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Узнайте больше о [дисках данных](virtual-machine-scale-sets-attached-disks.md).

Узнайте, как [управлять своими приложениями](virtual-machine-scale-sets-deploy-app.md).

