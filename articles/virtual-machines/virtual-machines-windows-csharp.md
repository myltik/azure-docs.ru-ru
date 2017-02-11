---
title: "Развертывание ресурсов Azure с помощью C# | Документация Майкрософт"
description: "Сведения о том, как использовать язык C# и Azure Resource Manager для создания ресурсов Microsoft Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 37ab64cb40def50342eb31c240aa301e9d751055
ms.openlocfilehash: 6fa33715484a4bb371148410b43c5ac606744160


---
# <a name="deploy-azure-resources-using-c"></a>Развертывание ресурсов Azure с помощью языка C# #
В этой статье показано, как создавать ресурсы Azure с помощью C#.

Сначала необходимо выполнить следующие задачи.

* Установите [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Проверьте, установлен ли компонент [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
* Получите [маркер аутентификации](../azure-resource-manager/resource-group-authenticate-service-principal.md)

На выполнение этих действий требуется примерно 30 минут.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Действие 1. Создание проекта Visual Studio и установка библиотек
Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Чтобы получить эти библиотеки в Visual Studio, выполните следующие действия.

1. Последовательно выберите пункты **Файл** > **Создать** > **Проект**.
2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.
3. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
4. В поле поиска введите текст *Active Directory* , нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.
5. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.Compute* , нажмите кнопку **Установить** , чтобы установить библиотеки Compute для .NET, а затем следуйте инструкциям по установке пакета.
6. В поле поиска введите *Microsoft.Azure.Management.Network* , нажмите кнопку **Установить** , чтобы установить библиотеки Network для .NET, а затем следуйте инструкциям по установке пакета.
7. В поле поиска введите *Microsoft.Azure.Management.Storage* , нажмите кнопку **Установить** , чтобы установить библиотеки Storage для .NET, а затем следуйте инструкциям по установке пакета.
8. В поле поиска введите *Microsoft.Azure.Management.ResourceManager* и нажмите кнопку **Установить** , чтобы установить библиотеки управления ресурсами.

Теперь вы готовы использовать библиотеки для создания приложения.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Шаг 2. Создание учетных данных, используемых для аутентификации запросов
Теперь на основе сведений о ранее построенном приложении можно создать учетные данные, которые будут использоваться для проверки подлинности запросов к Azure Resource Manager.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла следующие операторы using:
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
2. Чтобы создать необходимый маркер, добавьте этот метод в класс Program:
   
        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }
   
    Замените {client-id} идентификатором приложения Azure Active Directory, {client-secret} — ключом доступа приложения AD, а {tenant-id} — кодом клиента своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureRmSubscription. Ключ доступа можно узнать на портале Azure.
3. Чтобы вызвать добавленный ранее метод, добавьте следующий код в метод Main в файле Program.cs:
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Сохраните файл Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Шаг 3. Регистрация поставщиков ресурсов и создание ресурсов
### <a name="register-the-providers-and-create-a-resource-group"></a>Регистрация поставщиков и создание группы ресурсов
Все ресурсы должны содержаться в группе ресурсов. Прежде чем вы сможете добавлять ресурсы в группу, ваша подписка должна быть зарегистрирована в поставщике ресурсов.

1. Добавьте переменные в метод Main класса Program, чтобы указать имена, которые вы хотите использовать для ресурсов:
   
        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
   
    Замените все значения переменных именами и идентификаторами, которые вы хотите использовать. Чтобы узнать идентификатор подписки, выполните команду Get-AzureRmSubscription.
2. Чтобы создать группу ресурсов и зарегистрировать поставщики, добавьте следующий метод в класс Program:
   
        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
   
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
   
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }
3. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Для хранения файла виртуального жесткого диска, созданного для виртуальной машины, необходима [учетная запись хранения](../storage/storage-create-storage-account.md) .

1. Чтобы создать учетную запись хранения, добавьте этот метод в класс Program:
   
        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }
2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main класса Program:
   
        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса
Общедоступный IP-адрес необходим для взаимодействия с виртуальной машиной.

1. Чтобы создать общедоступный IP-адрес виртуальной машины, добавьте этот метод в класс Program:
   
        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }
2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main класса Program:
   
        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Создание виртуальной сети
Виртуальные машины, созданные с помощью модели развертывания диспетчера ресурсов, должны размещаться в виртуальной сети.

1. Чтобы создать подсеть и виртуальную сеть, добавьте этот метод в класс Program:
   
        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
   
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
   
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
   
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main класса Program:
   
        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-network-interface"></a>Создание сетевого интерфейса
Для обмена данными в виртуальной сети виртуальной машине нужен сетевой интерфейс.

1. Чтобы создать сетевой интерфейс, добавьте этот метод в класс Program:
   
        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);
   
          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }
2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main класса Program:
   
        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>"Создать группу доступности"
Группы доступности упрощают обслуживание виртуальных машин, используемых приложением.

1. Чтобы создать группу доступности, добавьте этот метод в класс Program:
   
        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }
2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main класса Program:
   
        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

1. Чтобы создать виртуальную машину, добавьте этот метод в класс Program:
   
        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);
   
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);
   
          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
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
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }
   
   > [!NOTE]
   > В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   > 
   > 
2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

## <a name="step-4-delete-the-resources"></a>Шаг 4. Удаление ресурсов
Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Если вы хотите удалить виртуальные машины и все вспомогательные ресурсы, достаточно удалить группу ресурсов.

1. Чтобы удалить группу ресурсов, добавьте этот метод в класс Program:
   
   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
   ```   
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-5-run-the-console-application"></a>Шаг 5. Запуск консольного приложения
1. Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.
2. Для создания каждого ресурса нажимайте клавишу **ВВОД** после возврата каждого кода состояния. После создания виртуальной машины выполните указанное ниже действие, прежде чем нажать клавишу ВВОД и удалить все ресурсы.
   
    На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу "ВВОД" и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы.
3. Чтобы отобразить состояние ресурсов, на портале Azure откройте раздел журналов аудита.
   
    ![Поиск журналов аудита на портале Azure](./media/virtual-machines-windows-csharp/crpportal.png)

## <a name="next-steps"></a>Дальнейшие действия
* Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Узнайте, как управлять созданной виртуальной машиной, прочитав статью [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Feb17_HO2-->


