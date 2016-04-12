<properties
	pageTitle="Развертывание ресурсов с использованием библиотек .NET | Microsoft Azure"
	description="Узнайте, как использовать библиотеки компонентов Compute, Storage и Network для .NET, чтобы создавать и удалять ресурсы в Microsoft Azure с помощью диспетчера ресурсов."
	services="virtual-machines-windows,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="davidmu"/>

# Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

В этом учебнике демонстрируется использование библиотек Compute, Network и Storage для .NET для создания и удаления ресурсов в Microsoft Azure. Кроме того, в нем имеются сведения о том, как проверить подлинность запросов к диспетчеру ресурсов Azure с помощью Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Для работы с этим учебником необходимы указанные ниже компоненты.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Учетная запись хранения Azure.](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0.](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

На выполнение этих действий требуется примерно 30 минут.

## Шаг 1. Добавление приложения в Azure AD и настройка разрешений

Чтобы использовать Azure AD для аутентификации запросов к диспетчеру ресурсов Azure, необходимо добавить приложение в каталог по умолчанию. Подробнее см. в разделе [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](../resource-group-authenticate-service-principal.md).

1. Откройте командную строку Azure PowerShell и выполните следующую команду. При появлении соответствующего запроса введите учетные данные своей подписки.

			Login-AzureRmAccount

2. В следующей команде замените слово "{password}" паролем, который вы будете использовать, а затем выполните ее, чтобы создать приложение:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Запишите идентификатор приложения, который возвращается после создания приложения, так как он потребуется для следующего шага. Кроме того, идентификатор приложения отображается в поле идентификатора клиента приложения в разделе Active Directory портала Azure.

3. Замените {application-id} только что записанным идентификатором, а затем создайте субъект-службу для приложения:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Настройте разрешение на использование приложения:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Действие 2. Создание проекта Visual Studio и установка библиотек

Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Вам потребуется установить библиотеку управления ресурсами Azure, библиотеку проверки подлинности Azure Active Directory и библиотеку поставщика ресурсов компьютера. Чтобы добавить эти библиотеки в Visual Studio, выполните указанные ниже действия.

1. Последовательно выберите пункты **Файл**, **Создать** и **Проект**.

2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

3. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.

4. В поле поиска введите текст *Active Directory*, нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.

5. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.Compute*, нажмите кнопку **Установить**, чтобы установить библиотеки Compute для .NET, а затем следуйте инструкциям по установке пакета.

6. В поле поиска введите *Microsoft.Azure.Management.Network*, нажмите кнопку **Установить**, чтобы установить библиотеки Network для .NET, а затем следуйте инструкциям по установке пакета.

7. В поле поиска введите *Microsoft.Azure.Management.Storage*, нажмите кнопку **Установить**, чтобы установить библиотеки Storage для .NET, а затем следуйте инструкциям по установке пакета.

8. В поле поиска введите *Microsoft.Azure.Management.Resources* и нажмите кнопку **Установить**, чтобы установить библиотеки управления ресурсами.

Теперь вы готовы использовать библиотеки для создания приложения.

## Действие 3. Создание учетных данных, используемых для проверки подлинности запросов

Теперь, когда создано приложение Azure Active Directory и установлена библиотека проверки подлинности, можно на основе сведений о приложении создать учетные данные, которые будут использоваться для проверки подлинности запросов к диспетчеру ресурсов Azure.

1. Откройте файл Program.cs для созданного вами проекта и добавьте в начало файла указанные ниже операторы using:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Azure.Management.Storage;
	using Microsoft.Azure.Management.Storage.Models;
	using Microsoft.Azure.Management.Network;
	using Microsoft.Azure.Management.Network.Models;
	using Microsoft.Azure.Management.Compute;
	using Microsoft.Azure.Management.Compute.Models;
	using Microsoft.Rest;
	```

2. Добавьте в класс Program этот метод, чтобы получить токен, необходимый для создания учетных данных:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Замените {application-id} записанным ранее идентификатором приложения, {password} — паролем, заданным вами для приложения AD, и {tenant-id} — идентификатором клиента для своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureSubscription.

3. Чтобы создать учетные данные, добавьте этот код в метод Main в файле Program.cs:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Сохраните файл Program.cs.

## Действие 4. Добавление кода для регистрации поставщиков и создания ресурсов

### Регистрация поставщиков и создание группы ресурсов

Все ресурсы должны содержаться в группе ресурсов. Прежде чем вы сможете добавлять ресурсы в группу, ваша подписка должна быть зарегистрирована в поставщике ресурсов.

1. Добавьте переменные в метод Main класса Program, чтобы указать имена, которые вы хотите использовать для ресурсов, расположение ресурсов (например West US), данные учетной записи администратора и идентификатор вашей подписки:

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   Замените все заполнители в скобках именами, которые хотите использовать. Чтобы узнать идентификатор подписки, выполните команду Get-AzureSubscription.

2. Чтобы создать группу ресурсов, добавьте этот метод в класс Program:

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. Для вызова только что добавленного метода добавьте этот код в метод Main:

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Создайте учетную запись хранения.

Для сохранения файла виртуального жесткого диска, созданного для виртуальной машины, необходима учетная запись хранения.

1. Чтобы создать учетную запись хранения, добавьте этот метод в класс Program:

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. Для вызова только что добавленного метода добавьте этот код в метод Main класса Program:

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Создание общедоступного IP-адреса

Общедоступный IP-адрес необходим для взаимодействия с виртуальной машиной.

1. Чтобы создать общедоступный IP-адрес, добавьте этот метод в класс Program:

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. Для вызова только что добавленного метода добавьте этот код в метод Main класса Program:

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Создание виртуальной сети

Виртуальные машины, созданные с помощью модели развертывания диспетчера ресурсов, должны размещаться в виртуальной сети.

1. Добавьте этот метод в класс Program, чтобы создать подсеть и виртуальную сеть:

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. Для вызова только что добавленного метода добавьте этот код в метод Main класса Program:

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### "Создать группу доступности"

Группы доступности упрощают обслуживание виртуальных машин, используемых приложением.

1. Чтобы создать группу доступности, добавьте этот метод в класс Program:

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. Для вызова только что добавленного метода добавьте этот код в метод Main класса Program:

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Создание виртуальной машины

Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

1. Чтобы создать виртуальную машину, добавьте этот метод в класс Program:

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
			groupName,
			vmName,
			new VirtualMachine
			{
				Location = location,
				AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
				{
					Id = avSet.Id
				},
				HardwareProfile = new HardwareProfile
				{
					VmSize = "Standard_A0"
				},
				OsProfile = new OSProfile
				{
					AdminUsername = adminName,
					AdminPassword = adminPassword,
					ComputerName = vmName,
					WindowsConfiguration = new WindowsConfiguration
					{
						ProvisionVMAgent = true
					}
				},
				NetworkProfile = new NetworkProfile
				{
					NetworkInterfaces = new List<NetworkInterfaceReference>
					{
						new NetworkInterfaceReference { Id = nic.Id }
					}
				},
				StorageProfile = new StorageProfile
				{
					ImageReference = new ImageReference
					{
						Publisher = "MicrosoftWindowsServer",
						Offer = "WindowsServer",
						Sku = "2012-R2-Datacenter",
						Version = "latest"
					},
					OsDisk = new OSDisk
					{
						Name = "mytestod1",
						CreateOption = "FromImage",
						Vhd = new VirtualHardDisk
						{
							Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
						}
					}
				}
			}
		);
		Console.WriteLine(vm.ProvisioningState);
	}
	```

	>[AZURE.NOTE] В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Navigate and select Linux virtual machine images in Azure with CLI or PowerShell](virtual-machines-linux-cli-ps-findimage.md).

2. Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##Действие 5. Добавление кода для удаления ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Если вы хотите удалить виртуальные машины и все вспомогательные ресурсы, достаточно удалить группу ресурсов.

1. Добавьте указанный ниже метод в класс Program, чтобы удалить группу ресурсов.

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## Действие 6. Запуск консольного приложения

1. Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

2. Для создания каждого ресурса нажимайте клавишу **ВВОД** после возврата каждого кода состояния. После создания виртуальной машины выполните указанное ниже действие, прежде чем нажать клавишу ВВОД и удалить все ресурсы.

	На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу "ВВОД" и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы.

3. Чтобы отобразить состояние ресурсов, на портале Azure откройте раздел журналов аудита.

	![Создание приложения AD](./media/virtual-machines-windows-csharp/crpportal.png)

<!---HONumber=AcomDC_0323_2016-->