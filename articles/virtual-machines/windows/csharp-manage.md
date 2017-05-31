---
title: "Управление виртуальными машинами с помощью Azure Resource Manager и языка C# | Документация Майкрософт"
description: "Управление виртуальными машинами с помощью Azure Resource Manager и языка C#"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93898bad-b861-4359-9a4b-348e1d491822
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 90708ff93a86f0b0883f069b45ed4e17c9667014
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Управление виртуальными машинами Azure с помощью Azure Resource Manager и языка C# #

Этой статье показаны задачи управления виртуальными машинами, в том числе как запуск, остановка и обновление.

## <a name="set-up-visual-studio"></a>Настройка Visual Studio

### <a name="create-a-project"></a>Создание проекта

Убедитесь, что среда Visual Studio установлена и создайте консольное приложение для управления виртуальными машинами.

1. Если вы этого еще не сделали, установите [Visual Studio](https://www.visualstudio.com/).
2. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
3. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение (.NET Framework)**, укажите имя и расположение проекта, а затем нажмите кнопку **OK**.

### <a name="install-libraries"></a>Установка библиотек

Для установки библиотек, необходимых для выполнения действий в этой статье, проще всего воспользоваться пакетами NuGet. Чтобы получить эти библиотеки в Visual Studio, выполните следующие действия.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**, а затем щелкните **Обзор**.
2. В поле поиска введите *Microsoft.IdentityModel.Clients.ActiveDirectory*, выберите проект, щелкните **Установить** и следуйте инструкциям по установке пакета.
3. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.Compute*, щелкните **Установить** и следуйте инструкциям по установке пакета.

Теперь эти библиотеки можно использовать для управления виртуальными машинами.

### <a name="create-credentials-and-add-variables"></a>Создание учетных данных и добавление переменных

Чтобы работать с Azure Resource Manager, убедитесь в наличии доступа к [субъекту-службе Active Directory](../../resource-group-authenticate-service-principal.md). Субъект-служба предоставляет маркер для аутентификации запросов к Azure Resource Manager.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла (к существующим операторам) следующие операторы using:

    ```   
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Добавьте в метод Main класса Program переменные для указания имени группы ресурсов и имени виртуальной машины, а также идентификатора подписки.

    ```   
    var groupName = "myResourceGroup";
    var vmName = "myVM";  
    var subscriptionId = "subsciptionId";
    ```

    Идентификатор подписки можно найти в колонке "Подписки" на портале Azure.    

3. Добавьте этот метод в класс Program, чтобы получить маркер, необходимый для создания учетных данных:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("client-id", "client-secret");
      var context = new AuthenticationContext("https://login.windows.net/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Замените следующие значения.
    
    - Замените *client-id* идентификатором приложения Azure Active Directory. Этот идентификатор можно найти в колонке "Свойства" приложения AD. Чтобы найти приложение AD на портале Azure, в меню ресурса щелкните **Azure Active Directory** и выберите **Регистрация приложения**.
    - Замените *client-secret* ключом доступа приложения AD. Этот идентификатор можно найти в колонке "Свойства" приложения AD.
    - Замените *tenant-id* идентификатором клиента своей подписки. Идентификатор клиента можно найти в колонке "Свойства" для Azure Active Directory на портале Azure. Он называется *идентификатором каталога*.

4. Чтобы вызвать добавленный ранее метод, добавьте следующий код в метод Main в файле Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

5. Сохраните файл Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Отображение информации о виртуальной машине

1. Добавьте следующий метод в класс Program в созданном ранее проекте:

    ```   
    public static async void GetVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Getting information about the virtual machine...");
   
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
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
      Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
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

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```   
    GetVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

3. Сохраните файл Program.cs.

4. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.
   
    Выполнив этот метод, вы получите примерно следующий результат:
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_D1_v2
   
        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite
   
          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True
   
          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1
   
          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM
   
          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM
   
          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus
   
          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Остановка виртуальной машины

Остановить виртуальную машину можно двумя способами. Вы можете остановить виртуальную машину, сохранив все ее настройки (при этом за нее будет продолжать взиматься плата) или остановить ее и отменить ее распределение. При этом освобождаются все ресурсы, связанные с ней, а также прекращается выставление счетов за эту виртуальную машину.

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

    ```   
    public static async void StopVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Stopping the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    Если вы хотите отменить распределение виртуальной машины, измените вызов PowerOff на следующий код:
    
    ```
    computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

3. Для вызова только что добавленного метода добавьте этот код в метод Main.
    
    ```
    StopVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.
   
    Вы должны увидеть, что состояние виртуальной машины изменилось на "Остановлена". Если вы выполнили метод для освобождения, виртуальная машина будет иметь состояние "Остановлена (освобождена)".

## <a name="start-a-virtual-machine"></a>запуск виртуальной машины

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

    ```   
    public static async void StartVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Starting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

3. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```   
    StartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.
   
    Вы должны увидеть, что состояние виртуальной машины изменилось на "Работает".

## <a name="restart-a-running-virtual-machine"></a>Перезапуск работающей виртуальной машины

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

    ```   
    public static async void RestartVirtualMachineAsync(
      TokenCredentials credential,
      string groupName,
      string vmName,
      string subscriptionId)
    {
      Console.WriteLine("Restarting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
    }
    ```

3. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```   
    RestartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

## <a name="resize-a-virtual-machine"></a>Изменение размера виртуальной машины

В этом примере показано, как изменить размер работающей виртуальной машины.

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

    ```   
    public static async void UpdateVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Updating the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_D2_v2";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

3. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```   
    UpdateVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.
   
    Вы должны увидеть, что размер виртуальной машины изменился на Standard_D2_v2.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Добавление диска данных в виртуальную машину

В этом примере показано, как добавить диск данных на запущенную виртуальную машину.

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

    ```   
    public static async void AddDataDiskAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
          {
            Lun = 0,
            Name = "mydatadisk1",
            Vhd = new VirtualHardDisk
              {
                Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
              },
            CreateOption = DiskCreateOptionTypes.Empty,
            DiskSizeGB = 2,
            Caching = CachingTypes.ReadWrite
          });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

    Замените mystorage1 именем учетной записи хранения, в которой хранятся диски для виртуальной машины.

3. Для вызова только что добавленного метода добавьте этот код в метод Main.
    
    ```
    AddDataDiskAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

## <a name="delete-a-virtual-machine"></a>удаление виртуальной машины

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:
   
    ```
    public static async void DeleteVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Deleting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
    }
    ```

3. Для вызова только что добавленного метода добавьте этот код в метод Main.
    
    ```
    DeleteVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

## <a name="next-steps"></a>Дальнейшие действия

- При наличии проблем с развертыванием ознакомьтесь со сведениями об [устранении распространенных ошибок при развертывании Azure с помощью Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Узнайте, как развернуть виртуальную машину и ее вспомогательные ресурсы, ознакомившись с разделом [Развертывание ресурсов Azure с помощью языка C#](csharp.md).
- Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



