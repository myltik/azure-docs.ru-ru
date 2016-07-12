<properties
	pageTitle="Управление виртуальными машинами с помощью Azure Resource Manager и языка C# | Microsoft Azure"
	description="Управление виртуальными машинами с помощью Azure Resource Manager и языка C#"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="davidmu"/>

# Управление виртуальными машинами Azure с помощью Azure Resource Manager и языка C#  

Этой статье показаны задачи управления виртуальными машинами, в том числе как запуск, остановка и обновление.

Для выполнения задач из этой статьи необходимо следующее:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Токен проверки подлинности](../resource-group-authenticate-service-principal.md)

## Создание проекта Visual Studio и установка пакетов

Для установки библиотек, которые необходимы для выполнения задач в этой статье, проще всего воспользоваться пакетами NuGet. Вам необходимо установить библиотеку проверки подлинности Azure Active Directory и библиотеку поставщика вычислительных ресурсов. Чтобы добавить эти библиотеки в Visual Studio, выполните следующие действия.

1. Последовательно выберите пункты **Файл**, **Создать** и **Проект**.

2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

3. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.

4. В поле поиска введите текст *Active Directory*, нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.

5. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.Compute*, нажмите кнопку **Установить**, чтобы установить библиотеки Compute для .NET, а затем следуйте инструкциям по установке пакета.

Теперь эти библиотеки можно использовать для управления виртуальными машинами.

## Настройка проекта

Теперь, когда создано приложение Azure Active Directory и установлена библиотека проверки подлинности, можно на основе сведений о приложении создать учетные данные, которые будут использоваться для проверки подлинности запросов к диспетчеру ресурсов Azure.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла следующие операторы using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Добавьте в метод Main класса Program переменные для указания имен ресурсов, которыми вы хотите управлять, расположения ресурсов (например "Центральная часть США") и идентификатора подписки:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";

    Замените все значения переменных именами и идентификаторами, которые вы хотите использовать. Чтобы узнать идентификатор подписки, выполните команду Get-AzureRmSubscription.
    
3. Добавьте этот метод в класс Program, чтобы получить маркер, необходимый для создания учетных данных.

	    private static async Task<AuthenticationResult> GetAccessTokenAsync()
	    {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }
	
    Замените {client-id} идентификатором приложения Azure Active Directory, {client-secret} — ключом доступа приложения AD, а {tenant-id} — кодом клиента своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureRmSubscription. Ключ доступа можно узнать на портале Azure.
    
4. Чтобы создать учетные данные, добавьте следующий код в метод Main в файле Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Сохраните файл Program.cs.

## Отображение информации о виртуальной машине

1. Добавьте следующий метод в класс Program в созданном ранее проекте:

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

2. Для вызова только что добавленного метода добавьте этот код в метод Main:

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Сохраните файл Program.cs.

4. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

	При выполнении этого метода вы увидите результат, подобный следующему:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

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

## запуск виртуальной машины

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

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

3. Для вызова только что добавленного метода добавьте этот код в метод Main:

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

	Вы должны увидеть, что состояние виртуальной машины изменилось на "Работает".

## Остановка виртуальной машины

Остановить виртуальную машину можно двумя способами. Можно остановить виртуальную машину, сохранив все ее настройки (при этом за нее будет продолжать взиматься плата) или освободив ее и все связанные с ней ресурсы (при этом выставление счетов за эту виртуальную машину прекращается).

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

        public static void StopVirtualMachineAsync(
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

	Если вы хотите освободить виртуальную машину, измените вызов PowerOff на следующий:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Для вызова только что добавленного метода добавьте этот код в метод Main:

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

    Вы должны увидеть, что состояние виртуальной машины изменилось на "Остановлена". Если вы выполнили метод для освобождения, виртуальная машина будет иметь состояние "Остановлена (освобождена)".

## Перезапуск работающей виртуальной машины

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

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

3. Для вызова только что добавленного метода добавьте этот код в метод Main:

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

## удаление виртуальной машины

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

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

3. Для вызова только что добавленного метода добавьте этот код в метод Main:

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

## Обновление виртуальной машины

В этом примере показано, как изменить размер работающей виртуальной машины.

1. Закомментируйте код, ранее добавленный в метод Main, за исключением кода для получения учетных данных.

2. Добавьте следующий метод в класс Program:

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
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Для вызова только что добавленного метода добавьте этот код в метод Main:

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Сохраните файл Program.cs.

5. Нажмите кнопку **Запуск** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

    Вы должны увидеть, что размер виртуальной машины изменился на Standard\_A1.
    
## Дальнейшие действия

При наличии проблем с развертыванием ознакомьтесь с [устранением неполадок развертывания групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0629_2016-->