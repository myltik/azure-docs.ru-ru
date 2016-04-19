<properties 
	pageTitle="Подключение приложения к виртуальной сети с помощью PowerShell" 
	description="Инструкции по подключению и работе с виртуальными сетями V1 и V2 с помощью PowerShell" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="ccompy"/>

# Подключение приложения к виртуальной сети с помощью PowerShell #

## Обзор ##

В службе приложений Azure приложение (веб-приложение, мобильное приложение или приложение API) можно подключить к виртуальной сети в подписке. Эта функция называется интеграцией с виртуальной сетью. Функцию интеграции с виртуальной сетью не следует путать с функцией среды службы приложений, которая позволяет запустить экземпляр службы приложений Azure в виртуальной сети.

Интерфейс для функции интеграции с виртуальной сетью есть на новом портале. Он позволяет выполнить интеграцию с сетями V1 или V2. Дополнительные сведения об этой функции см. в разделе "Интеграция приложения с виртуальной сетью Azure".

Эта статья посвящена не пользовательскому интерфейсу для этой функции, а включению интеграции с помощью PowerShell. Команды для виртуальных сетей V1 отличаются от команд для виртуальных сетей V2, поэтому статья содержит два раздела.

Перед выполнением действий, описанных в этом документе, убедитесь, что у вас:

1. установлена последняя версия пакета SDK для Azure PowerShell. Ее можно установить с помощью установщика веб-платформы.
1. Приложение в службе приложений Azure, работающее на SKU класса "Стандартный" или "Премиум"

## Виртуальные сети V1 (классические) ##

В этом документе рассматриваются три следующих действия для виртуальных сетей V1:

- подключение приложения к существующей виртуальной сети V1, в которой есть шлюз и которая настроена для подключения типа "точка-сеть";
- обновление сведений об интеграции с виртуальной сетью для приложения;
- отключение приложения от сети V1.

### Подключение приложения к виртуальной сети V1 (классической) ###

Подключение приложения к виртуальной сети состоит из трех этапов:

1. Объявите веб-приложению о том, что оно будет присоединено к конкретной виртуальной сети. Приложение создаст сертификат, который будет передан виртуальной сети для подключения типа "точка-сеть".
1. Отправьте сертификат веб-приложения в виртуальную сеть, а затем получите URI пакета VPN для подключения типа "точка-сеть".
1. Обновите подключение веб-приложений к виртуальной сети с помощью URI пакета для подключения типа "точка-сеть".

Шаги 1 и 3 можно полностью реализовать в качестве сценариев, но для шага 2 необходимо выполнить однократное действие на портале или необходим доступ для выполнения действий PUT и PATCH в конечной точке ARM виртуальной сети (чтобы включить его, обратитесь в службу поддержки Azure). Перед выполнением действий убедитесь, что у вас есть классическая виртуальная сеть с подключением типа "точка-сеть" и созданным и развернутым шлюзом. Для создания шлюза и включения подключения "точка-сеть" выполните на портале действия, описанные в статье [Создание VPN-шлюза][createvpngateway].

Виртуальная сеть V1 должна находиться в той же подписке, что и план службы приложений, содержащий приложение, с которым выполняется интеграция.

#####Настройка пакета SDK для Azure PowerShell#####

Откройте окно PowerShell и настройте учетную запись и подписку Azure с помощью следующей команды:

	Login-AzureRmAccount

При выполнении этой команды появится запрос на ввод учетных данных Azure. После входа выполните команду

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

или

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

для выбора подписки, которую нужно использовать. Убедитесь, что вы используете именно ту подписку, в которой находятся виртуальная сеть и план службы приложений.

#####Переменные, используемые в этом документе#####

Для упрощения указанных ниже команд мы запишем необходимую конфигурацию в переменную PowerShell $Configuration.

Установите эту переменную в PowerShell со следующими параметрами:

	$Configuration = @{} 
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]" 
	$Configuration.VnetResourceGroup = "[Your vnet resource group]" 
	$Configuration.VnetName = "[Your vnet name]"
 
Расположение приложения необходимо указывать без пробелов, например: "westus" для западной части США

	$Configuration.WebAppLocation = "[Your web app Location]" 

Следующий элемент представляет собой расположение будущего сертификата. Этот путь должен быть доступным для записи в локальной системе. Обязательно укажите ".cer" в конце.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]" 

Для просмотра установленной переменной наберите $Configuration.

	> $Configuration
	
	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

В остальной части документа предполагается, что вы создали переменную описанным способом.

#####Объявление виртуальной сети для приложения#####

Используйте следующую команду, чтобы сообщить приложению, что оно будет использовать указанную виртуальную сеть. При выполнении этой команды приложение создаст необходимые сертификаты:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Если команда выполнится успешно, $vnet будет содержать переменную Properties. Переменная Properties должна содержать как отпечаток сертификата, так и данные сертификата.

#####Отправка сертификата веб-приложения в виртуальную сеть#####

Для каждого сочетания виртуальной сети и подписки необходимо выполнить однократное действие вручную. То есть при подключении приложений в подписке A к виртуальной сети A это действие нужно выполнить только один раз независимо от того, сколько приложений вы настраиваете. При добавлении нового приложения к другой виртуальной сети это действие необходимо будет выполнить еще раз. Причина состоит в том, что набор сертификатов создается на уровне подписки в службе приложений Azure и один раз для каждой виртуальной сети, к которой будут подключаться приложения.

Если вы выполнили эти действия или интеграцию с той же виртуальной сетью с помощью портала, сертификаты уже будут настроены.

Сначала необходимо создать CER-файл. Затем необходимо отправить CER-файл в виртуальную сеть. Чтобы создать CER-файл с помощью вызова API на предыдущем шаге, выполните следующие команды.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Сертификат будет находиться в расположении, которое было задано с помощью параметра $Configuration.GeneratedCertificatePath

Чтобы отправить сертификат вручную, откройте новый портал на [портале Azure][azureportal] и выберите "Обзор виртуальных сетей (классических)" –> "VPN-подключения" –> "Точка-сеть" –> "Управление сертификатами". Здесь можно отправить сертификат.

#####Получение пакета "точка-сеть"#####

Следующий шаг при настройке подключения веб-приложения к виртуальной сети — получение пакета "точка-сеть" и его указание в веб-приложении.

Сохраните следующий шаблон в файл с именем GetNetworkPackageUri.json в произвольном каталоге на вашем компьютере, например "C:\\Azure\\Templates\\GetNetworkPackageUri.json"

	{ 
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#", 
		"contentVersion": "1.0.0.0", 
		"parameters": { 
			"certData": { 
				"type": "string" 
			}, 
			"certThumbprint": { 
				"type": "string" 
			}, 
			"networkName": { 
				"type": "string" 
			} 
		}, 
		"variables": { 
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]" 
			}, 
			"resources": [ 
			], 
		"outputs" : { 
			"PackageUri" : 
			{ 
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string" 
			} 
		} 
	} 


Установите входные параметры:

	$parameters = @{"certData" = $vnet.Properties.certBlob ; 
	certThumbprint = $vnet.Properties.certThumbprint ; 
	"networkName" = $Configuration.VnetName } 

Вызовите сценарий:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json 


Переменная $output.Outputs.packageUri будет содержать URI пакета, который передастся веб-приложению.

#####Отправка пакета "точка-сеть" в приложение#####

Последним шагом является отправка пакета в приложение. Для этого просто выполните следующую команду:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation 

Может появиться сообщение о перезаписи существующего ресурса, обязательно подтвердите перезапись.

После успешного выполнения этой команды приложение будет подключено к виртуальной сети. Это можно проверить, перейдя в консоль приложения и введя следующую команду:

	SET WEBSITE_

Если вы видите переменную среды WEBSITE\_VNETNAME со значением, соответствующим имени целевой виртуальной сети, это означает, что все конфигурации успешно установлены.

###Обновление сведений об интеграции с виртуальной сетью для виртуальной сети V1 (классической)###

Для обновления или повторной синхронизации данных просто повторите действия, выполненные при первоначальной настройке интеграции. К этим действиям относятся:

1. определение данных конфигурации;
1. объявление виртуальной сети для приложения;
1. получение пакета "точка-сеть";
1. отправка пакета "точка-сеть" в приложение.

###Отключение приложения от виртуальной сети V1 (классической)###

Для отключения приложения вам понадобятся данные конфигурации, которые использовались при интеграции с виртуальной сетью. Получив эти данные, достаточно выполнить следующую команду для отключения приложения от виртуальной сети.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Виртуальные сети V2 (Resource Manager) ##

Виртуальные сети V2 (Resource Manager) имеют API-интерфейсы ARM, и есть несколько вещей, которые позволят упростить настройку по сравнению с виртуальными сетями V1. Мы предоставим вам сценарий, который позволит:

- Создать виртуальную сеть V2 и интегрировать с ней свое приложение.
- Создать шлюз и настроить подключение "точка-сеть" в существующей виртуальной сети V2, а затем интегрировать с ней свое приложение.
- Интегрировать приложение с существующей виртуальной сетью V2, в которой есть шлюз и для которой настроено подключение типа "точка-сеть". 
- Отключить приложение от виртуальной сети V2.

###Сценарий интеграции службы приложений для виртуальной сети V2###

Скопируйте приведенный ниже сценарий и сохраните его в файл. Если вы не хотите использовать сценарий как есть, изучите его, чтобы узнать, как выполнить настройку для виртуальной сети V2.


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}
    
	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host
    
	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]
		    
		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }
    
    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }
    
    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }
    
    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	    
	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip
	    
	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData
	    
	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }
    
    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"
	    
	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"
	    
	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"
	    
	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup
	    
	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
		    
		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }
    
	    $ErrorActionPreference = "Stop";
	    
	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.
	    
	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location
	    
	    Write-Host "Creating App assocation to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	       
	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location
	    
	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
	    
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	    
	    Write-Host "Finished!"
    }
    
    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";
		
		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location
		   
		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}
		
		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }
	    
	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames
	    
	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.
	       
	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }
	       
	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."
		    
		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"
		    
		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"
		    
		    $changeRequested = 0
		    
		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }
		    
		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"
		    
		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
			    
			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }
		    
		    $ErrorActionPreference = "Stop";
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }
		    
		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
		    
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]
		       
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName
		    
		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates
		    
		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }
		    
		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }
	    
	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
	    
	    Write-Host "Finished!"
    }
       
    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"
		    
		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }
    
    Write-Host "Please Login"
    Login-AzureRmAccount
    
    # Choose subscription. If there's only one we will choose automatically
    
    $subs = Get-AzureRmSubscription
    $subscriptionId = ""
    
    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }
    
    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()
	       
	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }
	    
	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $resourceGroup = Read-Host "Please enter the Resource Group of your App"
    
    $appName = Read-Host "Please enter the Name of your App"
    
    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options
    
    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }
    
Сохраните копию сценария. В этом документе сценарий называется V2VnetAllinOne.ps1, но можно указать любое имя. Для этого сценария не нужно указывать никаких аргументов. Просто запустите его. Сначала сценарий попросит вас выполнить вход в систему. После входа сценарий отображает сведения о вашей учетной записи и список подписок для выбора. Не считая первого сообщения о вводе учетных данных, первоначальный запуск сценария выглядит так:

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login
	
	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :
	
	Choose a subscription
	
	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)
	
	Choose an option:
	3
	
	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47
	
	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?
	
	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

В остальной части документа описан каждый из этих 3 вариантов.

###Создание виртуальной сети V2 (Resource Manager) и интеграция с ней###

Чтобы создать новую виртуальную сеть V2 и интегрировать с ней свое приложение, выберите 1) Добавить к приложению НОВУЮ виртуальную сеть. После этого вас попросят указать имя виртуальной сети. В приведенном ниже примере я указал имя "v2pshell".

Сценарий сообщает сведения о создаваемой виртуальной сети. При желании можно изменить любой из параметров. При выполнении этого примера я создал виртуальную сеть со следующими параметрами:

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

Если требуется изменить какой-то параметр, наберите "Y" и внесите необходимые изменения. После изменения всех параметров виртуальной сети наберите "N" или просто нажмите ВВОД при появлении запроса на изменение параметров. После этого сценарий будет сообщать вам о некоторых выполняемых действиях вплоть до создания шлюза виртуальной сети. Создание шлюза может занять до одного часа. Хотя на этом шаге нет индикатора выполнения, после его окончания вы получите сообщение о создании шлюза.

После завершения работы сценария на экране появится сообщение **Завершено**. К этому моменту у вас будет создана виртуальная сеть V2 с выбранным именем и параметрами, и эта новая виртуальная сеть будет интегрирована с приложением.

###Интеграция приложения с существующей виртуальной сетью V2###

Если при интеграции с существующей виртуальной сетью указать виртуальную сеть V2, у которой нет шлюза или для которой не настроено подключение "точка-сеть", то сценарий настроит их. Если они уже настроены, сценарий сразу перейдет к интеграции приложения. Чтобы запустить этот процесс, просто выберите вариант 2) Добавить к приложению СУЩЕСТВУЮЩУЮ виртуальную сеть.

Этот вариант можно использовать только при наличии существующей виртуальной сети V2, которая находится в той же подписке, что и ваше приложение. После выбора варианта 2 появится список ваших виртуальных сетей V2.

	Select a VNET to integrate with
	
	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2
	
	Choose an option:
	5

Просто выберите виртуальную сеть, с которой нужно выполнить интеграцию. Если у вас уже есть шлюз с настроенным подключением "точка-сеть", то сценарий просто интегрирует ваше приложение с виртуальной сетью. Если у вас нет шлюза, потребуется указать подсеть шлюза. Подсеть шлюза должна находиться в адресном пространстве виртуальной сети и не может находиться ни в какой другой подсети. Поэтому для виртуальной сети без шлюза вы получите следующий результат при выполнении этого шага:

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

В этом примере я создал шлюз виртуальной сети со следующими параметрами:

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App assocation to VNET

Если вы хотите изменить любой из этих параметров, сделайте это. В противном случае нажмите ВВОД, и сценарий приступит к созданию шлюза и подключению приложения к виртуальной сети. При этом на создание шлюза все равно требуется около часа. После выполнения всех действий сценарий выдаст сообщение "Завершено".

###Отключение приложения от виртуальной сети V2###

При отключении приложения от виртуальной сети V2 не происходит отключение шлюза или подключение "точка-сеть". В конце концов, их можно использовать и для других целей. Они также не отключаются от других приложений кроме указанных вами. Чтобы выполнить это действие, выберите вариант 3) Удалить виртуальную сеть из приложения. При выборе этого варианта вы увидите следующее:

	Currently connected to VNET v2pshell
	
	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Несмотря на слово "удалить", сценарий не удаляет виртуальную сеть. Он удаляет только интеграцию. После подтверждения действия команда будет выполнена довольно быстро, и после ее завершения вы увидите сообщение "True".

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->