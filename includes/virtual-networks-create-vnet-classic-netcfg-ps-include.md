## Создание виртуальной сети с помощью файла конфигурации сети в PowerShell

В Azure для определения всех виртуальных сетей, доступных для подписки, используется файл XML. Вы можете загрузить этот файл и внести в него изменения, чтобы изменить или удалить существующие виртуальные сети и создать новые. В этом документе вы узнаете, как загрузить этот файл, который называется файлом конфигурации сети (или NETCGF), и внести в него изменения для создания новой виртуальной сети. Проверьте [схему конфигурации виртуальной сети Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx), чтобы получить дополнительные сведения о файле конфигурации сети.

Чтобы создать виртуальную сеть с помощью файла NETCGF, используя PowerShell, выполните описанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md) до момента входа Azure и выбора подписки.
2. В консоли Azure PowerShell воспользуйтесь командлетом **Get-AzureVnetConfig** для загрузки файла конфигурации сети, выполнив указанную ниже команду. Обратите внимание на результат выполнения команды.

		Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

		XMLConfiguration                                                                                                     
		----------------                                                                                                     
		<?xml version="1.0" encoding="utf-8"?>...  

3. Откройте файл, сохраненный при выполнении описанного выше шага 2, в любом XML- или текстовом редакторе и найдите элемент **<VirtualNetworkSites>**. Если вы уже создавали какие-то сети, для каждой сети будет указан ее собственный элемент **<VirtualNetworkSite>**.
4. Для создания виртуальной сети, описанной в этом сценарии, добавьте следующий XML-код сразу после элемента **<VirtualNetworkSites>**:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

9.  Сохраните файл конфигурации сети.
10. В консоли Azure PowerShell воспользуйтесь командлетом **Set-AzureVnetConfig** для передачи файла конфигурации сети, выполнив указанную ниже команду. После выполнения команды вы увидите значение **Успешно** в разделе **OperationStatus**. Если это не так, проверьте XML-файл на наличие ошибок.

		Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

		OperationDescription OperationId                          OperationStatus
		-------------------- -----------                          ---------------
		Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
	
11. В консоли Azure PowerShell воспользуйтесь командлетом **Get AzureVnetSite**, чтобы убедиться в том, что новая сеть была добавлена, выполнив указанную ниже команду. Посмотрите, как изменятся свойства новой виртуальной сети после выполнения этой команды.

		Get-AzureVNetSite -VNetName TestVNet

		AddressSpacePrefixes : {192.168.0.0/16}
		Location             : Central US
		AffinityGroup        : 
		DnsServers           : {}
		GatewayProfile       : 
		GatewaySites         : 
		Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
		InUse                : False
		Label                : 
		Name                 : TestVNet
		State                : Created
		Subnets              : {FrontEnd, BackEnd}
		OperationDescription : Get-AzureVNetSite
		OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
		OperationStatus      : Succeeded

<!---HONumber=August15_HO9-->