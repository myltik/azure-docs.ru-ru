<properties
   pageTitle="Пакет SDK .Net для диспетчера ресурсов Azure| Microsoft Azure"
   description="Обзор примеров проверки подлинности и использования пакета SDK .Net для диспетчера ресурсов"
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
   ms.date="05/17/2016"
   ms.author="navale;tomfitz;"/>

# Пакет SDK .Net для диспетчера ресурсов Azure  
Пакеты SDK предварительной версии для диспетчера ресурсов Azure (ARM) доступны для нескольких языков и платформ. Каждая из этих языковых реализаций доступна в экосистеме диспетчеров пакетов и на сайте GitHub.

Код в каждом из этих пакетов SDK генерируется из [спецификаций RESTful API Azure](https://github.com/azure/azure-rest-api-specs). Эти спецификации с открытым исходным кодом основаны на спецификации Swagger v2. Код пакета SDK создается в проекте с открытым исходным кодом, который называется [AutoRest](https://github.com/azure/autorest). AutoRest преобразует эти спецификации RESTful API в клиентские библиотеки на нескольких языках. Если требуется изменить какие-либо аспекты созданного кода в пакетах SDK, можно воспользоваться открытым, бесплатным, поддерживающим распространенный формат спецификации API набором средств для создания пакетов SDK.

Пакет SDK Azure для .NET предоставляется как набор пакетов NuGet, который позволяет вызывать большинство API-интерфейсов, предлагаемых диспетчером ресурсов Azure. Если в пакете SDK отсутствуют необходимые функциональные возможности, пакет SDK можно легко объединить с обычными вызовами REST API ARM в фоновом режиме.

В этом документе отсутствует описание всех аспектов, связанных с пакетом SDK Azure для .NET, API-интерфейсами ARM Azure или Visual Studio. Он создавался как руководство по быстрому началу работы.

Полный доступный для скачивания пример проекта, из которого были взяты все приведенные ниже фрагменты кода, можно найти [здесь](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Аутентификация
Проверку подлинности для ARM обрабатывает Azure Active Directory (AD). Чтобы подключиться к любому API, сначала необходимо пройти проверку подлинности в Azure AD для получения маркера проверки подлинности, который можно передать в каждый запрос. Чтобы получить этот маркер, нужно создать приложение Azure AD и субъект-службу, которая будет использоваться для входа. Пошаговые инструкции см. в разделе [Создание приложения Azure AD и субъекта-службы](resource-group-create-service-principal-portal.md).

После создания субъекта-службы у вас должны быть указанные ниже значения:
* Идентификатор клиента (GUID)
* Секрет клиента (строка)
* Идентификатор клиента (GUID) или имя домена (строка)

### Получение маркера доступа из кода
Маркер проверки подлинности можно легко получить с помощью приведенных ниже строк кода, передавая только идентификатор клиента Azure AD, код клиента приложения Azure AD и секрет клиента приложения Azure AD. Сохраните маркер для нескольких запросов, поскольку по умолчанию он является действительным в течение 1 часа.

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Выполнение запросов к подпискам Azure, присоединенным к прошедшим проверку подлинности приложению
Одним из первых действий, которые вы можете сделать, является выполнение запроса о том, какие подписки Azure связаны с приложением, прошедшим проверку подлинности. В каждый вызов API обязательно потребуется передать идентификатор подписки для целевой подписки.

В приведенном ниже примере кода выполняется прямой запрос к API-интерфейсам Azure с помощью REST API, т. е. без использования функций из пакета SDK Azure для .NET.

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

Обратите внимание, что мы получаем из Azure ответ JSON, из которого затем извлекаются идентификаторы подписок с целью возврата списка идентификаторов. Все последующие вызовы к API-интерфейсам Azure ARM в этой документации используют только один идентификатор подписки Azure, поэтому если ваше приложение связано с несколькими подписками, просто выберите нужную из них и передавайте в качестве параметра.

С этого момента каждый вызов к API-интерфейсам Azure будет использовать пакет SDK Azure SDK для .NET, поэтому код будет немного отличаться.

### Заключение маркера в оболочку в качестве объекта TokenCredentials
Для всех следующих вызовов API потребуется маркер, полученный из Azure AD, в формате объекта TokenCredentials. Такой объект легко создать, просто передав необработанный маркер в качестве параметра в конструктор класса.

```csharp
var credentials = new TokenCredentials(token);
```

## Создание группы ресурсов
Все действия в Azure сконцентрированы вокруг группы ресурсов, поэтому давайте начнем с создания такой группы. Общие ресурсы и группы ресурсов обрабатывает *ResourceManagementClient*, и как и для любого из следующих более специализированных клиентов управления, которые мы будем использовать, необходимо указать учетные данные, а также идентификатор подписки, чтобы определить необходимую для работы подписку.

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

## Создание ресурсов вручную или с помощью шаблонов
Существует несколько способов взаимодействия с API-интерфейсами диспетчера ресурсов Azure. Особо следует выделить два из них:

* вручную, путем вызова конкретных поставщиков ресурсов вручную или
* с помощью шаблона диспетчера ресурсов Azure (шаблона ARM).

Использование шаблонов ARM имеет следующие преимущества:

* вы декларативно указываете, как должен выглядеть конечный результат, а не задаете способ его достижения;
* не нужно вручную обрабатывать параллельное выполнение развертываний — ARM сделает это за вас;
* не нужно изучать C# или другие языки, чтобы развернуть шаблон ARM, несмотря на то, что начать развертывание шаблона можно с помощью любого языка;
* язык DSL, который используется в шаблонах, построенных с помощью JSON, достаточно легко понять тому, кто работал с JSON.

Даже располагая всеми преимуществами шаблонов, начнем с вызовов API вручную.

### Постепенное создание виртуальной машины
Итак, теперь у нас есть подписка и группа ресурсов. Чтобы развернуть виртуальную машину, нужно понять, из чего состоит виртуальная машина. Оказывается, в ней довольно много компонентов:

* 1 или несколько учетных записей хранения для хранения постоянных дисков;
* 1 или несколько общедоступных IP-адресов (PIP) для получения доступа через Интернет;
* 1 или несколько виртуальных сетей (VNET) для внутреннего взаимодействия ресурсов;
* 1 или несколько сетевых адаптеров (NIC) для обеспечения взаимодействия виртуальной машины;
* 1 или несколько виртуальных машин для запуска программного обеспечения.

Еще один интересный момент касается параллельного создания некоторых из этих ресурсов. Например:

* Сетевые адаптеры зависят от PIP и виртуальной сети
* Виртуальные машины зависят от сетевых адаптеров и учетных записей хранения

Перед созданием необходимых зависимостей не требуется создавать ресурсы. В полном [примере](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) в этом документе показано, как можно эффективно создать ресурсы в параллельном режиме с отслеживанием уже созданных компонентов.

#### Создание учетной записи хранения
Учетная запись хранения требуется для хранения виртуальных жестких дисков для виртуальной машины. Существующую учетную запись хранения можно использовать для нескольких виртуальных машин, но не забудьте распределить нагрузку между несколькими учетными записями хранения, чтобы не превысить ограничения. Помните, что тип учетной записи хранения и ее расположение могут ограничивать размер виртуальной машины, так как в определенных регионах и (или) для определенных типов учетных записей хранения доступны только определенные размеры виртуальных машин.

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

#### Создание общедоступного IP-адреса (PIP)
Благодаря общедоступному IP-адресу ресурсы в Azure доступны из Интернета. Вместе с IP-адресом также назначается полное доменное имя (FQDN), которое можно использовать для упрощения доступа.

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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Создание виртуальной сети (VNET)
Каждая виртуальная машина, созданная с помощью API-интерфейсов ARM, должна быть частью виртуальной сети, даже если кроме нее в сети больше ничего нет. Виртуальная сеть должна содержать по меньшей мере одну подсеть. Однако можно разделить несколько сетей, которые будут защищать ресурсы в нескольких подсетях.

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

#### Создание сетевого адаптера (NIC)
Сетевой адаптер используется для подключения виртуальной машины к виртуальной сети, в которой она находится. У виртуальной машины может быть несколько сетевых адаптеров, поэтому она может быть связана с несколькими виртуальными сетями. В этом примере предполагается, что вы подключаете виртуальные машины к одной виртуальной сети.

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
Наконец, пора создать фактическую виртуальную машину. Виртуальная машина напрямую или косвенно зависит от всех созданных выше ресурсов, поэтому прежде чем приступать к подготовке виртуальной машины, необходимо получить все упомянутые компоненты. Подготовка виртуальной машины является самым длительным процессом, поэтому приложение должно дождаться его завершения.

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

### Использование развертывания на основе шаблона
Прочтите и выполните подробные инструкции о развертывании шаблона из учебника [Развертывание ресурсов Azure с использованием библиотек .NET и шаблона](./virtual-machines/virtual-machines-windows-csharp-template.md).

Если кратко, развертывание шаблона гораздо проще подготовки ресурсов вручную. В примере кода ниже показано, как это сделать путем указания на URI, где находится шаблон и файл параметров.

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


 
   

<!---HONumber=AcomDC_0518_2016-->