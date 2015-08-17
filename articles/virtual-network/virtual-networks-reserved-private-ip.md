<properties 
   pageTitle="Настройка статического внутреннего частного IP-адреса"
   description="Основные сведения о статических внутренних IP-адресах (DIP) и управлении ими"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Настройка статического внутреннего частного IP-адреса
В большинстве случаев для виртуальной машины не нужно указывать статический внутренний IP-адрес. Виртуальные машины в виртуальной сети будут автоматически получать внутренний IP-адрес из указанного вами диапазона. Однако в некоторых случаях указание статического IP-адреса для конкретной виртуальной машины имеет смысл. Например, если на виртуальной машине планируется запускать DNS или она будет контроллером домена.

>[AZURE.NOTE]Статический внутренний IP-адрес остается у виртуальной машины даже при переходе в состояние остановки или отзыва.

## Проверка доступности определенного IP-адреса
Чтобы проверить, доступен ли IP-адрес *10.0.0.7* в виртуальной сети с именем *TestVnet*, выполните следующую команду PowerShell и проверьте значение для *IsAvailable*:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE]Если требуется протестировать указанную выше команду в защищенной среде, следуйте инструкциям из раздела [Создание виртуальной сети](https://msdn.microsoft.com/library/azure/dn631643.aspx), чтобы создать виртуальную сеть с именем *TestVnet* и убедиться в том, что она использует адресное пространство *10.0.0.0/8*.

## Указание статического внутреннего IP-адреса при создании виртуальной машины
Приведенный ниже сценарий PowerShell создает новую облачную службу с именем *TestService*, затем получает образ из Azure, после чего создает виртуальную машину с именем *TestVM* в новой облачной службе с использованием полученного образа, задает для этой ВМ нахождение в подсети с именем *Subnet-1* и устанавливает *10.0.0.7* статическим внутренним IP-адресом виртуальной машины:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## Получение сведений о статическом внутреннем IP-адресе виртуальной машины
Чтобы просмотреть сведения о статическом внутреннем IP-адресе виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду PowerShell и обратите внимание на значения *IpAddress*:

	Get-AzureVM -Name TestVM -ServiceName TestService

	DeploymentName              : TestService
	Name                        : TestVM
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 10.0.0.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : TestVM
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## Удаление статического внутреннего IP-адреса с виртуальной машины
Чтобы удалить статический внутренний IP-адрес, добавленный на виртуальную машину в приведенном выше сценарии, выполните следующую команду PowerShell:
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## Добавление статического внутреннего IP-адреса для существующей виртуальной машины
Чтобы добавить статический внутренний IP-адрес для виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду:

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## Дальнейшие действия

[Зарезервированный IP-адрес](../virtual-networks-reserved-public-ip)

[Общедоступный IP-адрес уровня экземпляра (ILPIP)](../virtual-networks-instance-level-public-ip)

[API REST зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=August15_HO6-->