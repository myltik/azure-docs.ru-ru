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
ms.date: 06/20/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: f04783896eb36e9a8c3e5cc8422a1fb054b36dd2
ms.contentlocale: ru-ru
ms.lasthandoff: 06/22/2017

---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Развертывание виртуальной машины Azure с помощью C# #

[Виртуальной машине Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) требуется несколько вспомогательных ресурсов Azure. В этой статье описывается создание, управление и удаление ресурсов виртуальной машины, с помощью C#. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание проекта Visual Studio
> * Установка библиотек
> * Создание учетных данных.
> * Создание ресурсов.
> * Выполнение задач управления.
> * Удаление ресурсов.
> * Выполнение приложения

На выполнение этих действий требуется примерно 20 минут.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Если вы этого еще не сделали, установите [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). На странице рабочих нагрузок выберите **Разработка классических приложений .NET** и нажмите кнопку **Установить**. В сводке вы увидите, что **средства разработки .NET Framework 4–4.6** выберутся автоматически. Если вы уже установили Visual Studio, можно добавить рабочую нагрузку .NET с помощью средства запуска Visual Studio.
2. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
3. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение (.NET Framework)**, укажите имя *myDotnetProject* и расположение проекта, а затем нажмите кнопку **OK**.

## <a name="install-libraries"></a>Установка библиотек

Самый простой способ установить библиотеки, необходимые для выполнения этих инструкций, — это использовать пакеты NuGet. Чтобы получить эти библиотеки в Visual Studio, выполните следующие действия.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта *myDotnetProject* и выберите пункт **Управление пакетами NuGet**, а затем щелкните **Обзор**.
2. В поле поиска введите *Microsoft.IdentityModel.Clients.ActiveDirectory*, щелкните **Установить** и следуйте инструкциям по установке пакета.
3. В поле поиска введите *Microsoft.Azure.Management.Compute*, щелкните **Установить** и следуйте инструкциям по установке пакета.
4. В поле поиска введите *Microsoft.Azure.Management.Network*, щелкните **Установить** и следуйте инструкциям по установке пакета.
5. В поле поиска введите *Microsoft.Azure.Management.ResourceManager*, щелкните **Установить** и следуйте инструкциям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

## <a name="create-credentials"></a>Создание учетных данных.

Прежде чем выполнить этот шаг, убедитесь в наличии доступа к [субъекту-службе Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Кроме того, необходимо записать идентификатор приложения, ключ проверки подлинности и идентификатор клиента, которые понадобятся позже.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла (к существующим операторам) следующие операторы using:
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Затем в методе Main файла Program.cs добавьте переменные, чтобы указать общие значения, используемые в коде:

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciption-id";
    var location = "westus";
    var vmName = "myVM";
    ```

    Замените **subscription-id** идентификатором вашей подписки.

3. Чтобы создать учетные данные Active Directory, которые необходимы для выполнения запросов, добавьте этот метод в класс Program:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("application-id", "authentication-key");
      var context = new AuthenticationContext("https://login.microsoftonline.com/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Замените **application-id**, **authentication-key** и **tenant-id** значениями, записанными при создании субъекта-службы Azure Active Directory.

4. Чтобы вызвать добавленный ранее метод, добавьте следующий код в метод Main в файле Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

## <a name="create-resources"></a>Создание ресурсов.

### <a name="initialize-management-clients"></a>Создание клиентов управления

Клиенты управления необходимы для создания и управления ресурсами с помощью пакета SDK .NET в Azure. Чтобы создать клиентов управления, добавьте этот код в метод Main в файле Program.cs.

```
var resourceManagementClient = new ResourceManagementClient(credential)
    { SubscriptionId = subscriptionId };
var networkManagementClient = new NetworkManagementClient(credential)
    { SubscriptionId = subscriptionId };
var computeManagementClient = new ComputeManagementClient(credential)
    { SubscriptionId = subscriptionId };
```

### <a name="create-the-vm-and-supporting-resources"></a>Создание виртуальной машины и ресурсов поддержки

Все ресурсы должны содержаться в [группе ресурсов](../../azure-resource-manager/resource-group-overview.md).

1. Чтобы создать группу ресурсов, добавьте приведенный ниже метод в класс Program.
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName,
      string location)
    {   
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      resourceManagementClient,
      groupName,
      location);
    Console.WriteLine("Resource group creation: " + 
      rgResult.Result.Properties.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

[Группы доступности](tutorial-availability-sets.md) упрощают обслуживание виртуальных машин, используемых приложением.

1. Чтобы создать группу доступности, добавьте этот метод в класс Program:

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        "myAVSet",
        new AvailabilitySet()
        { 
          Sku = new Microsoft.Azure.Management.Compute.Models.Sku("Aligned"),
          PlatformFaultDomainCount = 3,
          Location = location 
        });
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Availability set created. Press enter to continue...");
    Console.ReadLine();
    ```

[Общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) необходим для взаимодействия с виртуальной машиной.

1. Чтобы создать общедоступный IP-адрес виртуальной машины, добавьте в класс Program следующий метод.
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync( 
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        "myIpAddress",
        new PublicIPAddress
        {
          Location = location,
          PublicIPAllocationMethod = "Dynamic"
        });
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("IP address creation: " + 
      ipResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Виртуальная машина должна быть в подсети [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Чтобы создать подсеть и виртуальную сеть, добавьте этот метод в класс Program:

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = new Subnet
      {
        Name = "mySubnet",
        AddressPrefix = "10.0.0.0/24"
      };
      var address = new AddressSpace 
      {
        AddressPrefixes = new List<string> { "10.0.0.0/16" }
      };
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        "myVNet",
        new VirtualNetwork
        {
          Location = location,
          AddressSpace = address,
          Subnets = new List<Subnet> { subnet }
        });
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual network creation: " + 
      vnResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Для обмена данными в виртуальной сети виртуальной машине нужен сетевой интерфейс.

1. Чтобы создать сетевой интерфейс, добавьте этот метод в класс Program:

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        "myVNet",
        "mySubnet");
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        "myIPaddress");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        "myNic",
        new NetworkInterface
        {
          Location = location,
          IpConfigurations = new List<NetworkInterfaceIPConfiguration>
          {
            new NetworkInterfaceIPConfiguration
            {
              Name = "myNic",
              PublicIPAddress = publicIP,
              Subnet = subnet
            }
          }
        });
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Network interface creation: " + 
      ncResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

1. Чтобы создать виртуальную машину, добавьте этот метод в класс Program:

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      NetworkManagementClient networkManagementClient,
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
 
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        "myNic");
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        "myAVSet");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        "myVM",
        new VirtualMachine
        {
          Location = location,
          AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
          {
            Id = avSet.Id
          },
          HardwareProfile = new HardwareProfile
          {
            VmSize = "Standard_DS1"
          },
          OsProfile = new OSProfile
          {
            AdminUsername = "azureuser",
            AdminPassword = "Azure12345678",
            ComputerName = "myVM",
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
            }
          }
        });
    }
    ```

    > [!NOTE]
    > В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      networkManagementClient,
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual machine creation: " + 
      vmResult.Result.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

## <a name="perform-management-tasks"></a>Выполнение задач управления.

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать код для автоматизации повторяющихся или сложных задач.

### <a name="get-information-about-the-vm"></a>Получение информации о виртуальной машине

1. Чтобы получить сведения о виртуальной машине, добавьте этот метод в класс Program:

    ```
    public static async void GetVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Getting information about the virtual machine...");

      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
        groupName,
        vmName,
        InstanceViewTypes.InstanceView);

      Console.WriteLine("hardwareProfile");
      Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

      Console.WriteLine("\nstorageProfile");
      Console.WriteLine("  imageReference");
      Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
      Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
      Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
      Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
      Console.WriteLine("  osDisk");
      Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
      Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
      Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
      Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
      Console.WriteLine("\nosProfile");
      Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
      Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
      Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
      Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

      Console.WriteLine("\nnetworkProfile");
      foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
      {
        Console.WriteLine("  networkInterface id: " + nic.Id);
      }

      Console.WriteLine("\nvmAgent");
      Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
      Console.WriteLine("    statuses");
      foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
      {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    message: " + stat.Message);
        Console.WriteLine("    time: " + stat.Time);
      }

      Console.WriteLine("\ndisks");
      foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
      {
        Console.WriteLine("  name: " + idisk.Name);
        Console.WriteLine("  statuses");
        foreach (InstanceViewStatus istat in idisk.Statuses)
        {
          Console.WriteLine("    code: " + istat.Code);
          Console.WriteLine("    level: " + istat.Level);
          Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
          Console.WriteLine("    time: " + istat.Time);
        }
      }

      Console.WriteLine("\nVM general status");
      Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
      Console.WriteLine("  id: " + vmResult.Id);
      Console.WriteLine("  name: " + vmResult.Name);
      Console.WriteLine("  type: " + vmResult.Type);
      Console.WriteLine("  location: " + vmResult.Location);
      Console.WriteLine("\nVM instance status");
      foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
      {
        Console.WriteLine("\n  code: " + istat.Code);
        Console.WriteLine("  level: " + istat.Level);
        Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
      }
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:

    ```
    GetVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="stop-the-vm"></a>Остановка виртуальной машины

Вы можете остановить виртуальную машину, сохранив все ее настройки (при этом за нее будет продолжать взиматься плата) или остановить ее и отменить ее распределение. При этом освобождаются все ресурсы, связанные с ней, а также прекращается выставление счетов за эту виртуальную машину.

1. Чтобы остановить виртуальную машину без ее освобождения, добавьте этот метод в класс Program:

    ```
    public static async void StopVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Stopping the virtual machine...");
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    Если вы хотите отменить распределение виртуальной машины, измените вызов PowerOff на следующий код:

    ```
    await computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    StopVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="start-the-vm"></a>Запуск виртуальной машины

1. Чтобы запустить виртуальную машину, добавьте этот метод в класс Program:

    ```
    public static async void StartVirtualMachineAsync(
      string groupName, 
      string vmName, 
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Starting the virtual machine...");
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    StartVirtualMachineAsync(
      groupName,
      vmName,
      ComputeManagementClient computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="resize-the-vm"></a>Изменение размера виртуальной машины

При выборе размера виртуальной машины необходимо учесть многие аспекты развертывания. Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](sizes.md).  

1. Чтобы изменить размер виртуальной машины, добавьте этот метод в класс Program:

    ```
    public static async void UpdateVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Updating the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_DS3";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    UpdateVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Добавление диска данных в виртуальную машину

1. Чтобы добавить диск с данными к виртуальной машине, добавьте этот метод в класс Program:

    ```
    public static async void AddDataDiskAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
        {
          Lun = 0,
          Name = "myDataDisk1",
          CreateOption = DiskCreateOptionTypes.Empty,
          DiskSizeGB = 2,
          Caching = CachingTypes.ReadWrite
        });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    AddDataDiskAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

## <a name="delete-resources"></a>Удаление ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Если вы хотите удалить виртуальные машины и все вспомогательные ресурсы, достаточно удалить группу ресурсов.

1. Чтобы удалить группу ресурсов, добавьте этот метод в класс Program:
   
    ```
    public static async void DeleteResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName)
    {
      Console.WriteLine("Deleting resource group...");
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. Чтобы вызвать добавленный ранее метод, добавьте этот код в метод Main:
   
    ```   
    DeleteResourceGroupAsync(
      resourceManagementClient,
      groupName);
    Console.ReadLine();
    ```

3. Сохраните проект.

## <a name="run-the-application"></a>Выполнение приложения

1. Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

2. Нажмите клавишу **ВВОД** после возвращения сведений о состоянии каждого ресурса. В сведениях о состоянии должно отобразиться состояние подготовки **Успешно**. После создания виртуальной машины вы можете удалить все созданные ресурсы. Прежде чем нажать клавишу **ВВОД** и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы. Если у вас открыт портал Azure, может потребоваться обновить колонку, чтобы увидеть новые ресурсы.  

    На полное выполнение этого консольного приложения потребуется примерно 5 минут. После закрытия приложения на удаление ресурсов и групп ресурсов может потребоваться несколько минут.

## <a name="next-steps"></a>Дальнейшие действия
* Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Дополнительные сведения об использовании [библиотек Azure для .NET](https://docs.microsoft.com/dotnet/azure/index?view=azure-dotnet).


