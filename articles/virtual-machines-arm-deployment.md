<properties 
	pageTitle="Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET" 
	description="Узнайте, как использовать некоторые из доступных клиентов в библиотеках компонентов Compute, Storage и Network для .NET, чтобы создавать и удалять ресурсы в Microsoft Azure." 
	services="virtual-machines,virtual-network,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="davidmu"/>

# Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET

В этом учебнике рассказывается, как использовать некоторые из доступных клиентов в библиотеках компонентов Compute, Network и Storage для .NET, чтобы создавать и удалять ресурсы в Microsoft Azure. Кроме того, в нем имеются сведения о том, как проверить подлинность запросов к диспетчеру ресурсов Azure с помощью Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

Для работы с этим учебником необходимы указанные ниже компоненты.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Учетная запись хранения Azure](storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/ru-ru/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/ru-ru/download/details.aspx?id=40855)
- [Azure PowerShell](install-configure-powershell.md)

На выполнение этих действий требуется примерно 30 минут.

## Действие 1. Добавление приложения в Azure AD и настройка разрешений

Чтобы использовать Azure AD для проверки подлинности запросов к диспетчеру ресурсов Azure, необходимо добавить приложение в каталог по умолчанию. Чтобы добавить приложение, выполните указанные ниже действия.

1. Откройте командную строку Azure PowerShell и выполните следующую команду:

        Switch-AzureMode –Name AzureResourceManager

2. Настройте учетную запись Azure, которую вы собираетесь использовать для работы с данным учебником. Выполните следующую команду и при появлении соответствующего запроса введите учетные данные для вашей подписки:

	    Add-AzureAccount

3. В следующей команде замените слово "{password}" паролем, который вы будете использовать, а затем выполните ее, чтобы создать приложение:

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Запишите значение идентификатора приложения, полученное в результате выполнения предыдущего действия. Оно потребуется при дальнейшей работе с данным учебником:

	![Создание приложения AD](./media/virtual-machines-arm-deployment/azureapplicationid.png)

	>[AZURE.NOTE]Кроме того, идентификатор приложения отображается в поле идентификатора клиента приложения на портале управления.

5. Замените выражение {application-id} только что записанным идентификатором, а затем создайте субъект-службу для приложения:

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. Настройте разрешение на использование приложения:

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Действие 2. Создание проекта Visual Studio и установка библиотек

Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Вам потребуется установить библиотеку управления ресурсами Azure, библиотеку проверки подлинности Azure Active Directory и библиотеку поставщика ресурсов компьютера. Чтобы добавить эти библиотеки в Visual Studio, выполните указанные ниже действия.

1. Последовательно выберите пункты **Файл**, **Создать** и **Проект**.

2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

3. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.

4. В поле поиска введите текст *Active Directory*, нажмите кнопку **Установить** для пакета библиотеки проверки подлинности Active Directory, а затем следуйте инструкциям по установке пакета.

5. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите текст *Azure Compute*, нажмите кнопку **Установить**, чтобы установить библиотеки Compute для .NET, а затем следуйте инструкциям по установке пакета.

6. В поле поиска введите текст *Azure Network*, нажмите кнопку **Установить**, чтобы установить библиотеки Network для .NET, а затем следуйте инструкциям по установке пакета.

7. В поле поиска введите текст *Azure Storage*, нажмите кнопку **Установить**, чтобы установить библиотеки Storage для .NET, а затем следуйте инструкциям по установке пакета.

8. В поле поиска введите текст *Управление ресурсами Azure* и нажмите кнопку **Установить** для библиотек управления ресурсами.

Теперь вы готовы использовать библиотеки для создания приложения.

## Действие 3. Создание учетных данных, используемых для проверки подлинности запросов

Теперь, когда создано приложение Azure Active Directory и установлена библиотека проверки подлинности, можно на основе сведений о приложении создать учетные данные, которые будут использоваться для проверки подлинности запросов к диспетчеру ресурсов Azure. Выполните следующие действия.

1.	Откройте файл Program.cs для созданного вами проекта и добавьте в начало файла указанные ниже операторы.

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


2. Добавьте в класс Program указанный ниже метод, чтобы получить маркер, необходимый для создания учетных данных.

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Замените текст {application-id} записанным ранее идентификатором приложения, текст {password} — паролем, заданным вами для приложения AD, и текст {tenant-id} — идентификатором клиента для вашей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureSubscription.

3.	Чтобы создать учетные данные, добавьте указанный ниже код в метод Main в файле Program.cs.

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	Замените текст {subscription-id} идентификатором вашей подписки.

4.	Сохраните файл Program.cs.

## Действие 4. Добавление кода для регистрации поставщиков и создания ресурсов

### Регистрация поставщиков и создание группы ресурсов

Ресурсы всегда развертываются в группу ресурсов. Чтобы создать группу ресурсов, в которую будут развернуты ресурсы, используйте классы [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) и [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1.	Чтобы создать группу ресурсов, добавьте указанный ниже метод в класс Program.

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  
          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

		CreateResourceGroup(credential);
		Console.ReadLine();

###Создайте учетную запись хранения.

Для сохранения файла виртуального жесткого диска, созданного для виртуальной машины, необходима учетная запись хранения.

1.	Добавьте указанный ниже метод в класс Program, чтобы создать учетную запись хранения.

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");
          
          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.
		
		CreateStorageAccount(credential);
		Console.ReadLine();

###Создать виртуальную сеть

Чтобы виртуальная машина работала максимально эффективно, добавьте ее в виртуальную сеть.

1.	Добавьте указанный ниже метод в класс Program, чтобы создать подсеть, общедоступный IP-адрес и виртуальную сеть.

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

		CreateNetwork(credential);
		Console.ReadLine();

###Создание виртуальной машины

Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

1.	Добавьте указанный ниже метод в класс Program, чтобы создать виртуальную машину.

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);
                
            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk 
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]Имена образов виртуальных жестких дисков в коллекции образов регулярно изменяются, поэтому для развертывания виртуальной машины необходимо получить текущее имя образа. В разделе [Управление образами с помощью Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx) прочитайте сведения о том, как сделать это, и замените текст {source-image-name} именем файла виртуального жесткого диска, который необходимо использовать. Имя может иметь следующий вид: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd.

	Замените текст {subscription-id} идентификатором вашей подписки.

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

		CreateVirtualMachine(credential);
        Console.ReadLine();

##Действие 5. Добавление кода для удаления ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Если вы хотите удалить виртуальные машины и все вспомогательные ресурсы, достаточно удалить группу ресурсов.

1.	Добавьте указанный ниже метод в класс Program, чтобы удалить группу ресурсов.

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

		DeleteResourceGroup(credential);
        Console.ReadLine();

##Действие 6. Запуск консольного приложения

1.	Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

2.	Для создания каждого ресурса нажимайте клавишу **ВВОД** после возврата каждого кода состояния. После создания виртуальной машины выполните указанное ниже действие, прежде чем нажать клавишу ВВОД и удалить все ресурсы.

	На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу ВВОД и начать удаление ресурсов, потратьте несколько минут и проверьте на портале предварительной версии Azure, созданы ли эти ресурсы.

3. Чтобы отобразить состояние ресурсов, на портале предварительной версии Azure откройте раздел журналов аудита.

	![Создание приложения AD](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=58-->