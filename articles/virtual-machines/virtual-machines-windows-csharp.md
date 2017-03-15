---
title: "Развертывание виртуальной машины Azure с помощью C# | Документация Майкрософт"
description: "Использование C# и Azure Resource Manager для развертывания виртуальной машины и всех ее вспомогательных ресурсов."
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
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: a03a33ca275d0d81f8968ed19b1cc2fb8907cb1c
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>Развертывание виртуальной машины Azure с помощью C## #

В этой статье показано, как использовать язык C# для создания виртуальной машины Azure и ее вспомогательных ресурсов.

На выполнение этих действий требуется примерно 20 минут.

## <a name="step-1-create-a-visual-studio-project"></a>Шаг 1. Создание проекта Visual Studio

На этом шаге нужно убедиться, что экземпляр Visual Studio установлен, и создать консольное приложение, используемое для создания ресурсов.

1. Если вы этого еще не сделали, установите [Visual Studio](https://www.visualstudio.com/).
2. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
3. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

## <a name="step-2-install-libraries"></a>Шаг 2. Установка библиотек

Самый простой способ установить библиотеки, необходимые для выполнения этих инструкций, — это использовать пакеты NuGet. Чтобы получить эти библиотеки в Visual Studio, выполните следующие действия.


1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**, затем щелкните **Обзор**.
2. В поле поиска введите *Microsoft.IdentityModel.Clients.ActiveDirectory*, щелкните **Установить** и следуйте инструкциям по установке пакета.
3. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.Compute*, щелкните **Установить** и следуйте инструкциям по установке пакета.
4. В поле поиска введите *Microsoft.Azure.Management.Network*, щелкните **Установить** и следуйте инструкциям по установке пакета.
5. В поле поиска введите *Microsoft.Azure.Management.Storage*, щелкните **Установить** и следуйте инструкциям по установке пакета.
6. В поле поиска введите *Microsoft.Azure.Management.ResourceManager*, щелкните **Установить** и следуйте инструкциям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>Шаг 3. Создание учетных данных, используемых для аутентификации запросов

Прежде чем выполнить этот шаг, убедитесь в наличии доступа к [субъекту-службе Active Directory](../azure-resource-manager/resource-group-authenticate-service-principal.md). Субъект-служба предоставляет маркер для аутентификации запросов к Azure Resource Manager.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла следующие операторы using:
   
    ```
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
    ```

2. Чтобы получить маркер, необходимый для создания учетных данных, добавьте в класс Program следующий метод.
   
    ```    
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
    ```

    Замените следующие значения.
    
    - Замените *{client-id}* идентификатором приложения Azure Active Directory. Этот идентификатор можно найти в колонке "Свойства" приложения AD. Чтобы найти приложение AD на портале Azure, в меню ресурса щелкните **Azure Active Directory** и выберите **Регистрация приложения**.
    - Замените *{client-secret}* ключом доступа приложения AD. Этот идентификатор можно найти в колонке "Свойства" приложения AD.
    - Замените *{tenant-id}* идентификатором клиента своей подписки. Идентификатор клиента можно найти в колонке "Свойства" для Azure Active Directory на портале Azure. Он называется *идентификатором каталога*.

3. Чтобы вызвать добавленный ранее метод, добавьте следующий код в метод Main в файле Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Сохраните файл Program.cs.

## <a name="step-3-create-the-resources"></a>Шаг 3. Создание ресурсов

### <a name="register-the-providers-and-create-a-resource-group"></a>Регистрация поставщиков и создание группы ресурсов

Все ресурсы должны содержаться в группе ресурсов. Прежде чем вы сможете добавлять ресурсы в группу, ваша подписка должна быть зарегистрирована в поставщике ресурсов.

1. Добавьте переменные в метод Main класса Program, чтобы указать имена, которые вы хотите использовать для ресурсов:

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    Замените следующие значения.

    - Замените все имена ресурсов, которые начинаются с *my*, осмысленными именами для своей среды.
    - Замените *subscriptionId* идентификатором подписки. Идентификатор подписки можно найти в колонке "Подписки" на портале Azure.
    - Замените *location* [регионом Azure](https://azure.microsoft.com/regions/), в котором необходимо создать ресурсы.
    - Замените *adminName* именем учетной записи администратора на виртуальной машине.
    - Замените *adminPassword* паролем учетной записи администратора.

2. Чтобы создать группу ресурсов и зарегистрировать поставщики, добавьте следующий метод в класс Program:
   
    ```
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
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

В случае использования неуправляемых дисков для хранения файла виртуального жесткого диска, созданного для виртуальной машины, необходима [учетная запись хранения](../storage/storage-create-storage-account.md).

1. Чтобы создать учетную запись хранения, добавьте этот метод в класс Program:

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

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
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Общедоступный IP-адрес необходим для взаимодействия с виртуальной машиной.

1. Чтобы создать общедоступный IP-адрес виртуальной машины, добавьте в класс Program следующий метод.
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

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
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Виртуальные машины, созданные с помощью модели развертывания диспетчера ресурсов, должны размещаться в виртуальной сети.

1. Чтобы создать подсеть и виртуальную сеть, добавьте этот метод в класс Program:

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
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
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>Создание сетевого интерфейса

Для обмена данными в виртуальной сети виртуальной машине нужен сетевой интерфейс.

1. Чтобы создать сетевой интерфейс, добавьте этот метод в класс Program:

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
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
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
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
    ```

### <a name="create-an-availability-set"></a>"Создать группу доступности"

Группы доступности упрощают обслуживание виртуальных машин, используемых приложением.

1. Чтобы создать группу доступности, добавьте этот метод в класс Program:

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

1. Чтобы создать виртуальную машину, добавьте этот метод в класс Program:

    ```   
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
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
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
    ```

    > [!NOTE]
    > В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
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
    ```

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

2. После появления состояния **Успешно** нажмите клавишу *ВВОД*. 
   
3. После создания виртуальной машины, прежде чем нажать клавишу **ВВОД** и начать удаление ресурсов, потратьте несколько минут и проверьте ресурсы на портале Azure.

## <a name="next-steps"></a>Дальнейшие действия
* Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Узнайте, как управлять созданной виртуальной машиной, ознакомившись с разделом [Управление виртуальными машинами Azure с помощью Azure Resource Manager и языка C#](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


