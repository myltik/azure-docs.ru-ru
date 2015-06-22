<properties 
   pageTitle="Общедоступный IP-адрес уровня экземпляра (ILPIP)"
   description="Общие сведения об ILPIP-адресах и работе с ними"
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

# Общие сведения об общедоступных IP-адресах уровня экземпляра
Общедоступный IP-адрес уровня экземпляра (ILPIP) — это IP-адрес, который можно назначить непосредственно виртуальной машине или экземпляру роли, а не облачной службе, в которой они находятся. Он не заменяет собой виртуальный IP-адрес, назначенный облачной службе, а представляет собой дополнительный IP-адрес, с помощью которого можно подключаться непосредственно к виртуальной машине или экземпляру роли.

>[AZURE.NOTE]Ранее ILPIP-адрес назывался общедоступным IP-адресом.

![Разница между ILPIP-адресом и виртуальным IP-адресом](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Как показано на рис. 1, для доступа к облачной службе используется виртуальный IP-адрес, а для обращения к отдельным виртуальным машинам, как правило, применяется формат &lt;виртуальный IP-адрес&gt;:&lt;номер порта&gt;. Назначив определенной виртуальной машине ILPIP-адрес, вы сможете обращаться к ней напрямую.

При создании облачной службы в среде Azure автоматически создаются соответствующие A-записи DNS, благодаря которым к службе можно обращаться по полному доменному имени вместо ее фактического виртуального IP-адреса. То же самое выполняется и для ILPIP-адресов, благодаря чему к виртуальным машинам и экземплярам ролей можно обращаться по полному доменному имени, не указывая ILPIP-адрес.


>[AZURE.NOTE]Каждой виртуальной машине или экземпляру роли можно назначить только один ILPIP-адрес. В одной подписке разрешается использовать до пяти ILPIP-адресов. В настоящее время ILPIP-адреса не поддерживаются для виртуальных машин с несколькими сетевыми адаптерами.

## Зачем запрашивать ILPIP-адрес?
Вы можете отправить запрос на создание ILPIP-адреса для своей виртуальной машины или экземпляра роли, если хотите подключаться к ним непосредственно по определенному IP-адресу, не используя формат &lt;виртуальный IP-адрес облачной службы&gt;:&lt;номер порта&gt;. **Пассивный FTP**: создав ILPIP-адрес для виртуальной машины, вы сможете принимать трафик практически на любой порт, и вам не придется открывать для этого конечную точку. Благодаря этому можно, например, создать пассивный FTP-сервер, порты на котором выбираются динамически. **Внешний IP-адрес**: исходящий трафик с виртуальной машины отправляется с ILPIP-адресом в качестве источника, который уникальным образом идентифицирует эту машину для внешних отправителей и получателей.

>[AZURE.NOTE]Использование зарезервированных ILPIP-адресов может привести к увеличению стоимости подписки Azure. Дополнительные сведения о ценах на ILPIP-адреса см. в статье [Цены на IP-адреса](http://azure.microsoft.com/pricing/details/ip-addresses/).

## Отправка запроса на получение ILPIP-адреса на этапе создания виртуальной машины
Приведенный ниже скрипт PowerShell создает новую облачную службу под названием *FTPService*, загружает образ из среды Azure, создает на его основе виртуальную машину под названием *FTPInstance*, задает для нее ILPIP-адрес и добавляет эту машину в новую службу.

	New-AzureService -ServiceName FTPService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## Получение сведений об ILPIP-адресе виртуальной машины
Чтобы узнать ILPIP-адрес виртуальной машины, созданной с помощью приведенного выше скрипта, выполните следующую команду PowerShell и обратите внимание на значения *PublicIPAddress* и *PublicIPName*:

	Get-AzureVM -Name FTPInstance -ServiceName FTPService

	DeploymentName              : FTPService
	Name                        : FTPInstance
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : ReadyRole
	IpAddress                   : 100.74.118.91
	InstanceStateDetails        : 
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : FTPInstance
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : FTPInstance
	AvailabilitySetName         : 
	DNSName                     : http://ftpservice888.cloudapp.net/
	Status                      : ReadyRole
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 104.43.142.188
	PublicIPName                : ftpip
	NetworkInterfaces           : {}
	ServiceName                 : FTPService
	OperationDescription        : Get-AzureVM
	OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
	OperationStatus             : OK

## Удаление ILPIP-адреса виртуальной машины
Чтобы удалить ILPIP-адрес, назначенный виртуальной машине в приведенном выше скрипте, выполните следующую команду PowerShell:
	
	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Remove-AzurePublicIP `
	| Update-AzureVM

## Назначение ILPIP-адреса существующей виртуальной машине
Чтобы назначить ILPIP-адрес существующей виртуальной машине, созданной с помощью приведенного выше скрипта, выполните следующую команду:

	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Set-AzurePublicIP -PublicIPName ftpip2 `
	| Update-AzureVM

## Связывание ILPIP-адреса с виртуальной машиной с помощью файла конфигурации службы
Чтобы связать ILPIP-адрес с виртуальной машиной, также можно воспользоваться файлом конфигурации службы (CSCFG-файлом). В XML-коде ниже показано, как настроить облачную службу для использования зарезервированного IP-адреса *MyReservedIP* в качестве ILPIP-адреса экземпляра роли.
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
      <NetworkConfiguration>
	    <VirtualNetworkSite name="VNet"/>
	    <AddressAssignments>
	      <InstanceAddress roleName="VMRolePersisted">
	        <Subnets>
	          <Subnet name="Subnet1"/>
	          <Subnet name="Subnet2"/>
	        </Subnets>
	        <PublicIPs>
	          <PublicIP name="MyReservedIP" domainNameLabel="MyReservedIP" />
	        </PublicIPs>
	      </InstanceAddress>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## См. также

[Зарезервированный частный IP-адрес (DIP)](../virtual-networks-reserved-private-ip)

[Зарезервированный общедоступный IP-адрес](../virtual-networks-reserved-public-ip)

[Обзор виртуальной сети](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[API REST зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx)

<!---HONumber=58--> 