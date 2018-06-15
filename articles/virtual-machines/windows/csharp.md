---
title: Создание виртуальной машины Azure с помощью C# и управление ею | Документация Майкрософт
description: Использование C# и Azure Resource Manager для развертывания виртуальной машины и всех ее вспомогательных ресурсов.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 99c8e71e0a31f6ef2f4fb2087fb8678c68a052a7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525945"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Развертывание виртуальной машины Azure с помощью C# #

[Виртуальной машине Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) требуется несколько вспомогательных ресурсов Azure. В этой статье описывается создание, управление и удаление ресурсов виртуальной машины, с помощью C#. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание проекта Visual Studio
> * Установка пакета
> * Создание учетных данных
> * Создание ресурсов.
> * Выполнение задач управления.
> * Удаление ресурсов.
> * Выполнение приложения

На выполнение этих действий требуется примерно 20 минут.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Если вы этого еще не сделали, установите [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). На странице рабочих нагрузок выберите **Разработка классических приложений .NET** и нажмите кнопку **Установить**. В сводке вы увидите, что **средства разработки .NET Framework 4–4.6** выберутся автоматически. Если вы уже установили Visual Studio, можно добавить рабочую нагрузку .NET с помощью средства запуска Visual Studio.
2. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
3. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение (.NET Framework)**, укажите имя *myDotnetProject* и расположение проекта, а затем нажмите кнопку **OK**.

## <a name="install-the-package"></a>Установка пакета

Самый простой способ установить библиотеки, необходимые для выполнения этих инструкций, — это использовать пакеты NuGet. Чтобы получить эти библиотеки в Visual Studio, выполните следующие действия.

1. Щелкните **Сервис** > **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**.
2. Введите следующую команду в консоли:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Создание учетных данных

Прежде чем выполнить этот шаг, убедитесь в наличии доступа к [субъекту-службе Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Кроме того, необходимо записать идентификатор приложения, ключ проверки подлинности и идентификатор клиента, которые понадобятся позже.

### <a name="create-the-authorization-file"></a>Создание файла авторизации

1. В обозревателе решений щелкните правой кнопкой мыши *myDotnetProject* > **Добавить** > **Новый элемент** и в списке **Элементы Visual C#** выберите *Текстовый файл*. Назовите файл *azureauth.properties* и щелкните **Добавить**.
2. Добавьте следующие свойства авторизации:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Замените **&lt;subscription-id&gt;** своим идентификатором подписки, **&lt;application-id&gt;** — идентификатором приложения Active Directory, **&lt;authentication-key&gt;** — ключом приложения, а **&lt;tenant-id&gt;** — идентификатором клиента.

3. Сохраните файл azureauth.properties. 
4. Задайте переменную среды в Windows с именем AZURE_AUTH_LOCATION с полным путем к файлу авторизации, созданному вами. Например, можно использовать следующую команду PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Создание клиента управления

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла (к существующим операторам) следующие операторы using:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Чтобы создать клиент управления, добавьте следующий код в метод Main:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Создание ресурсов

### <a name="create-the-resource-group"></a>Создание группы ресурсов

Все ресурсы должны содержаться в [группе ресурсов](../../azure-resource-manager/resource-group-overview.md).

Чтобы определить значения для приложения и создать группу ресурсов, добавьте следующий код в метод Main:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Создание группы доступности

[Группы доступности](tutorial-availability-sets.md) упрощают обслуживание виртуальных машин, используемых приложением.

Чтобы создать группу доступности, добавьте следующий код в метод Main:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса

[Общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) необходим для взаимодействия с виртуальной машиной.

Чтобы создать общедоступный IP-адрес виртуальной машины, добавьте следующий код в метод Main:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

Виртуальная машина должна быть в подсети [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

Чтобы создать подсеть и виртуальную сеть, добавьте следующий код в метод Main:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Создание сетевого интерфейса

Для обмена данными в виртуальной сети виртуальной машине нужен сетевой интерфейс.

Чтобы создать сетевой интерфейс, добавьте следующий код в метод Main:

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Создание виртуальной машины

Теперь, когда вы создали все вспомогательные ресурсы, можно создать виртуальную машину.

Чтобы создать виртуальную машину, добавьте следующий код в метод Main:

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Если вы хотите использовать имеющийся диск вместо образа Marketplace, используйте следующий код:

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Выполнение задач управления

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать код для автоматизации повторяющихся или сложных задач.

Если необходимо выполнить любые действия с виртуальной машиной, необходимо получить ее экземпляр:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Получение информации о виртуальной машине

Чтобы получить сведения о виртуальной машине, добавьте следующий код в метод Main:

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Остановка виртуальной машины

Вы можете остановить виртуальную машину, сохранив все ее настройки (при этом за нее будет продолжать взиматься плата) или остановить ее и отменить ее распределение. При этом освобождаются все ресурсы, связанные с ней, а также прекращается выставление счетов за эту виртуальную машину.

Чтобы остановить виртуальную машину без ее освобождения, добавьте следующий код в метод Main:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Если вы хотите отменить распределение виртуальной машины, измените вызов PowerOff на следующий код:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Запуск виртуальной машины

Чтобы запустить виртуальную машину, добавьте следующий код в метод Main:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Изменение размера виртуальной машины

При выборе размера виртуальной машины необходимо учесть многие аспекты развертывания. Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](sizes.md).  

Чтобы изменить размер виртуальной машины, добавьте следующий код в метод Main:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Добавление диска данных в виртуальную машину

Чтобы добавить диск данных в виртуальную машину, добавьте следующий код в метод Main. При этом будет добавлен диск данных размером 2 ГБ, имеющий логический номер устройства (LUN) 0 и тип кэширования ReadWrite:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Удаление ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Если вы хотите удалить виртуальные машины и все вспомогательные ресурсы, достаточно удалить группу ресурсов.

Чтобы удалить группу ресурсов, добавьте следующий код в метод Main:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Выполнение приложения

На полное выполнение этого консольного приложения потребуется примерно 5 минут. 

1. Чтобы запустить консольное приложение, щелкните **Запустить**.

2. Прежде чем нажать клавишу **ВВОД** и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы. Щелкните состояние развертывания, чтобы просмотреть сведения о развертывании.

## <a name="next-steps"></a>Дополнительная информация
* Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Дополнительные сведения об использовании [библиотек Azure для .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

