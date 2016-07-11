<properties
   pageTitle="Пакет SDK Resource Manager для .NET| Microsoft Azure"
   description="Обзор примеров проверки подлинности и использования пакета SDK .NET для Resource Manager"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# Пакет SDK Azure Resource Manager для .NET  
Пакеты SDK для Azure Resource Manager доступны для нескольких языков и платформ. Каждая из этих языковых реализаций доступна в диспетчере пакетов экосистемы и на сайте GitHub.

Код в каждом из этих пакетов SDK генерируется из [спецификаций RESTful API Azure](https://github.com/azure/azure-rest-api-specs). Эти спецификации с открытым исходным кодом основаны на спецификации Swagger 2.0. Код пакета SDK создается в проекте с открытым исходным кодом под названием [AutoRest](https://github.com/azure/autorest). AutoRest преобразует эти спецификации RESTful API в клиентские библиотеки на нескольких языках. Если требуется изменить какие-либо аспекты созданного кода в пакетах SDK, можно воспользоваться открытым, бесплатным, поддерживающим распространенный формат спецификации API набором средств для создания пакетов SDK.

[Пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/) — это набор пакетов NuGet, который позволяет вызывать большинство API-интерфейсов Azure Resource Manager. Если в пакете SDK отсутствуют необходимые функциональные возможности, его можно легко объединить с обычными вызовами REST API Resource Manager в фоновом режиме.

В этой статье описаны не все аспекты пакета Azure SDK для .NET, API-интерфейсов Azure Resource Manager или Visual Studio. В ней предоставляется руководство по быстрому началу работы.

Все приведенные ниже фрагменты кода взяты из [загружаемого примера проекта](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Установка пакетов Nuget

Для выполнения примеров, приведенных в этой статье, требуются два пакета NuGet (в дополнение к пакету Azure SDK для .NET). Чтобы их установить, сделайте следующее.

1. В Visual Studio щелкните правой кнопкой мыши проект и выберите пункт **Управление пакетами NuGet**.
2. Найдите пакет **Microsoft.IdentityModel.Clients.ActiveDirectory** и установите его последнюю стабильную версию.
3. Найдите пакет **Microsoft.Azure.Management.ResourceManager** и выберите **Включить предварительные выпуски**. Установите последнюю предварительную версию (например, 1.1.2-preview).

## Проверка подлинности
Проверка подлинности Resource Manager осуществляется с помощью Azure Active Directory (Azure AD). Чтобы подключиться к любому API, сначала необходимо пройти проверку подлинности в Azure AD для получения маркера доступа, который можно передать в каждый запрос. Чтобы получить этот маркер, нужно создать приложение Azure AD и субъект-службу, которая будет использоваться для входа. Пошаговые указания см. в одной из следующих статей:

- [Создание приложения Active Directory для доступа к ресурсам с помощью Azure PowerShell](resource-group-authenticate-service-principal.md)
- [Создание приложения Active Directory для доступа к ресурсам с помощью Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [Создание приложения Active Directory с доступом к ресурсам с помощью портала](resource-group-create-service-principal-portal.md)

После создания субъекта-службы у вас должны быть указанные ниже значения:

- идентификатор клиента или приложения (GUID);
- секрет клиента или пароль (строка);
- идентификатор клиента (GUID) или имя домена (строка).

### Получение маркера доступа из кода
Маркер доступа можно получить с помощью приведенных ниже строк кода, передавая только идентификатор клиента Azure AD, код клиента приложения Azure AD и секрет клиента приложения Azure AD. Сохраните маркер для нескольких запросов, так как по умолчанию он действителен в течение 1 часа.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

Вместо идентификатора клиента для входа можно использовать домен Azure AD, как показано в следующем коде. При таком подходе требуется изменить подпись метода и добавить в нее доменное имя вместо идентификатора клиента.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

Вы можете получить маркер доступа для приложения Azure AD, использующего сертификат для проверки подлинности, с помощью следующего кода:

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Выполнение запросов к подпискам Azure, присоединенным к прошедшему проверку подлинности приложению
Может возникнуть необходимость выполнить запрос к подпискам Azure, связанным с приложением, прошедшим проверку подлинности. В каждый вызов API обязательно потребуется передать идентификатор подписки для целевой подписки.

В следующем примере кода запрос отправляется непосредственно в API-интерфейсы Azure при помощи REST API. То есть в нем не используются какие-либо функции пакета Azure SDK для .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Обратите внимание, что из службы Azure вы получаете ответ JSON. Затем из этого ответа необходимо извлечь идентификаторы подписки, чтобы возвратить список идентификаторов. В этой статье для всех последующих вызовов к API-интерфейсам Azure Resource Manager используется один идентификатор подписки Azure. Поэтому в дальнейшем, если приложение связано с несколькими подписками, просто выберите нужную из них и передайте ее в качестве параметра.

С этого момента в каждом вызове к API-интерфейсам Azure будет использоваться пакет Azure SDK для .NET. Следовательно код будет немного отличаться.

### Заключение маркера в оболочку в качестве объекта TokenCredentials
Для всех следующих вызовов API потребуется токен, полученный из Azure AD, в формате объекта TokenCredentials. Такой объект можно создать, передав необработанный токен в качестве параметра в конструктор класса.

```csharp
var credentials = new TokenCredentials(token);
```

Если у вас установлена более ранняя версия пакета NuGet Resource Manager (с именем Microsoft.Azure.Management.Resources), необходимо использовать следующий код:

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## Создание группы ресурсов
Все действия в Azure сконцентрированы вокруг группы ресурсов, поэтому давайте создадим такую группу. *ResourceManagementClient* обрабатывает общие ресурсы и группы ресурсов. Чтобы определить необходимую для работы подписку, необходимо, как и для любого из следующих более специализированных клиентов управления, которые будут использоваться, указать учетные данные и идентификатор подписки.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Создание ресурсов вручную или с использованием шаблонов
Есть несколько способов взаимодействия с API-интерфейсами Azure Resource Manager. Особо следует выделить два из них:

* вручную, путем вызова конкретных поставщиков ресурсов;
* с использованием шаблона Azure Resource Manager.

Использование шаблона Resource Manager дает следующие преимущества.

* Вы декларативно указываете, как должен выглядеть результат, а не задаете способ его достижения.
* Не нужно вручную обрабатывать параллельное выполнение развертываний. Resource Manager сделает это за вас.
* Не нужно изучать C# или другие языки, чтобы развернуть шаблон Resource Manager, несмотря на то, что начать развертывание шаблона можно с использованием любого языка.
* Доменный язык (DSL), который используется в шаблонах, создается с помощью JSON. Каждый, кто работал с JSON, сразу это поймет.

Даже располагая всеми преимуществами шаблонов, начнем с вызовов API вручную.

### Постепенное создание виртуальной машины
У вас есть подписка и группа ресурсов. Для развертывания виртуальной машины, необходимо знать из чего она состоит:

* одна или несколько учетных записей хранения для хранения постоянных дисков;
* один или несколько общедоступных IP-адресов для доступа к ресурсам в Azure из Интернета (включает DNS-имя);
* одна или несколько виртуальных сетей для внутреннего взаимодействия ресурсов;
* одна или несколько сетевых карт для обеспечения взаимодействия виртуальных машин;
* одна или несколько виртуальных машин для запуска программного обеспечения.

Одни из этих ресурсов можно создавать параллельно, а другие — нельзя. Например:

* сетевые карты зависят от общедоступных IP-адресов и виртуальных сетей;
* виртуальные машины зависят от сетевых карт и учетных записей хранения.

Перед созданием необходимых зависимостей не требуется создавать экземпляры ресурсов. В полном [примере](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) показано, как можно эффективно создавать ресурсы в параллельном режиме, все еще отслеживая уже созданные компоненты.

#### Создание учетной записи хранения
Учетная запись хранения требуется для хранения виртуальных жестких дисков для виртуальной машины. Имеющуюся учетную запись хранения можно использовать для нескольких виртуальных машин. Но не забудьте распределить нагрузку между несколькими учетными записями хранения, чтобы не превысить ограничения. Кроме того, помните, что тип учетной записи хранения и ее расположение могут ограничивать размер виртуальной машины, так как в определенных регионах и для определенных типов учетных записей хранения доступны не все размеры виртуальных машин.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Создание общедоступного IP-адреса
Благодаря общедоступному IP-адресу ресурсы в Azure доступны из Интернета. Вместе с IP-адресом назначается полное доменное имя (FQDN), которое можно использовать для упрощения доступа.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### Создание виртуальной сети
Каждая виртуальная машина, созданная с помощью API-интерфейсов Resource Manager, должна быть частью виртуальной сети, даже если кроме нее в сети больше ничего нет. Виртуальная сеть должна содержать по меньшей мере одну подсеть. Однако можно использовать несколько подсетей, которые будут разделять ресурсы и способствовать их защите.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Создание сетевой карты
Сетевая карта используется для подключения виртуальной машины к виртуальной сети, в которой она располагается. У виртуальной машины может быть несколько сетевых карт, поэтому она может быть связана с несколькими виртуальными сетями. В этом примере виртуальные машины подключены только к одной виртуальной сети.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Создание виртуальной машины
Наконец, пора создать виртуальную машину. Виртуальная машина зависит (напрямую или косвенно) от всех раннее созданных ресурсов, поэтому прежде чем приступать к подготовке виртуальной машины, необходимо получить все ресурсы. Подготовка виртуальной машины длится дольше, чем создание ресурсов, поэтому приложение должно дождаться ее завершения.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Развертывание шаблона
Подробные указания по развертыванию шаблона прочтите в руководстве о [развертывании ресурсов Azure с использованием библиотек .NET и шаблона](./virtual-machines/virtual-machines-windows-csharp-template.md).

В нескольких словах можно сказать, что развертывание шаблона намного проще, чем подготовка ресурсов вручную. В приведенном ниже коде показано, как выполнить это, указав универсальные коды ресурсов, в которых содержатся шаблон и файл параметров.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```

<!---HONumber=AcomDC_0629_2016-->