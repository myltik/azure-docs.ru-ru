---
title: "Как использовать управляемую службу кэша Azure"
description: "Узнайте, как повысить производительность приложений с помощью управляемой службы кэша Azure."
services: cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 80552353-6682-4b96-a4cc-4e975a25980c
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/20/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: ec9e12667bc82043fbdc0b3f113533cf518f39be
ms.openlocfilehash: 66907ccb97dedc176c5fe76826abd3b31f4ccdb9


---
# <a name="how-to-use-azure-managed-cache-service"></a>Как использовать управляемую службу кэша Azure
> [!IMPORTANT]
> Согласно прошлогоднему [объявлению](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), использование управляемой службы кэша Azure и кэша роли Azure **было прекращено** 30 ноября 2016 года. Мы рекомендуем использовать [кэш Redis для Azure](https://azure.microsoft.com/services/cache/). Дополнительные сведения о переходе на новую службу см. в разделе [Перенос из управляемой службы кэша в кэш Redis для Azure](../redis-cache/cache-migrate-to-redis.md).
> 
> 

В этом руководстве показано, как приступить к использованию **управляемой службы кэша Azure**. Примеры написаны на языке C\# и используют интерфейс API .NET. Рассматриваются разнообразные сценарии, в том числе **создание и настройка кэша**, **настройка клиентов кэша**, **добавление и удаление объектов из кэша, хранение состояния сеанса ASP.NET в кэше** и **включение кэширования вывода страниц ASP.NET с помощью кэша**. Дополнительную информацию об использовании кэша Azure см. в разделе [Дальнейшие действия][Next Steps].



<a name="what-is"></a>

## <a name="what-is-azure-managed-cache-service"></a>Что такое управляемая служба кэша Azure?
Управляемая служба кэша Azure — это распределенное, находящееся в памяти масштабируемое решение, которое позволяет создавать быстрые приложения с высокой степенью масштабируемости благодаря тому, что обеспечивается сверхбыстрый доступ к данным.

Вот какими возможностями обладает управляемая служба кэша Azure.

* Готовые поставщики ASP.NET для кэширования состояний сеанса и выводимых данных страниц, ускоряющие работу веб-приложений без изменения их кода.
* Кэширование любого сериализуемого управляемого объекта, например объектов среды CLR, строк, XML, двоичных данных.
* Согласованная модель разработки для Azure и Windows Server AppFabric.

Управляемая служба кэша предоставляет доступ к безопасному выделенному кэшу, управляемому корпорацией Майкрософт. Кэш, созданный с помощью управляемой службы кэша, доступен из приложений в Azure, работающих на веб-сайтах Azure, в веб-ролях и рабочих ролях, а также на виртуальных машинах.

Управляемая служба кэша доступна на трех уровнях:

* Basic — кэш размером от 128 МБ до 1 ГБ.
* Standard — кэш размером от 1 до 10 ГБ.
* Premium — кэш размером от 5 до 150 ГБ.

Каждый уровень отличается доступными возможностями и ценой. Конкретные возможности рассматриваются ниже в этом руководстве, а дополнительные сведения о ценах доступны на странице [Сведения о ценах — кэш][Cache Pricing Details].

Это руководство содержит обзор, посвященный началу работы с управляемой службой кэша. За дополнительной информацией о компонентах, которые не вошли в это руководство, обратитесь к разделу [Сведения о кэше роли для кэша Azure][Overview of Azure Managed Cache Service].

<a name="getting-started-cache-service"></a>

## <a name="getting-started-with-cache-service"></a>Приступая к работе со службой кэша
Приступить к работе с управляемой службой кэша легко. Чтобы приступить к работе, подготовьте и настройте кэш. Затем следует настроить клиенты кэша, чтобы они могли обращаться к кэшу. Когда клиенты кэша настроены, с ними можно работать.

* [Создание кэша][Create the cache]
* [Настройка кэша][Configure the cache]
* [Настройка клиентов кэша][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Создание кэша
Экземпляры кэша в управляемой службе кэша создаются с помощью командлетов PowerShell. 

> После того как экземпляр службы управляемого кэша создан с помощью командлета PowerShell, его можно просмотреть и настроить на [классическом портале Azure][Azure Classic Portal].
> 
> 

Чтобы создать экземпляр службы управляемого кэша, откройте окно Azure PowerShell.

> Инструкции по установке и использованию Azure PowerShell см. в разделе [Установка и настройка Microsoft Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

Вызовите командлет [Add-AzureAccount][Add-AzureAccount] и введите электронный адрес и пароль, связанные с вашей учетной записью. Подписка выбирается по умолчанию и отображается после вызова командлета [Add-AzureAccount][Add-AzureAccount]. Чтобы сменить подписку, вызовите командлет [Select-AzureSubscription][Select-AzureSubscription].

> Если вы настроили Azure PowerShell сертификатом для своей учетной записи, этот шаг можно пропустить. Дополнительные сведения о подключении Azure PowerShell к своей учетной записи Azure см. в разделе [Установка и настройка Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

Будет показана выбранная по умолчанию подписка. Чтобы сменить подписку, вызовите командлет [Select-AzureSubscription][Select-AzureSubscription].

Вызовите командлет [New-AzureManagedCache][New-AzureManagedCache] и укажите имя, регион, предложение кэша и размер кэша.

В **Name**введите имя поддомена для конечной точки кэша. Конечная точка должна быть строкой длиной от шести до двадцати символов, содержать только строчные буквы и цифры, а также должна начинаться с буквы.

В поле **Область**укажите регион для кэша. Для обеспечения оптимальной производительности создайте кэш в той же области, где находится приложение клиента кэша.

Поля **Sku** и **Память** определяют размер кэша. Служба управляемого кэша предоставляется на трех следующих уровнях.

* Basic — кэш размером от 128 МБ до 1 ГБ с шагом 128 МБ, один именованный кэш по умолчанию.
* Standard — кэш размером от 1 до 10 ГБ с шагом 1 ГБ, поддержка уведомлений и до десяти именованных кэшей.
* Premium — кэш размером от 5 до 150 ГБ с шагом 5 ГБ, поддержка уведомлений, высокой доступности и до десяти именованных кэшей.

Выберите значения **Sku** и **Память**, удовлетворяющие требованиям вашего приложения. Обратите внимание, что некоторые возможности кэша, например уведомления и высокая доступность, предоставляются только в некоторых предложениях. Дополнительную информацию о выборе предложения кэша и его размера, которые лучше всего подходят для вашего приложения, см. в разделе [Предложения кэша][Cache offerings].

 В следующем примере создается базовый 128 МБ кэш с именем contosocache в Южно-Центральном географическом регионе США.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Полный список параметров и значений, которые могут использоваться при создании кэша, доступны в документации по командлету [New-AzureManagedCache][New-AzureManagedCache].
> 
> 

После вызова командлета PowerShell cоздание кэша может занять несколько минут. После создания новый кэш находится в состоянии `Running`, он готов к использованию с параметрами по умолчанию, и его можно просмотреть и настроить на [классическом портале Azure][Azure Classic Portal]. Сведения о настройке конфигурации кэша см. в следующем разделе [Настройка кэша][Configure the cache].

Ход выполнения операции создания можно отслеживать в окне Azure PowerShell. Когда кэш будет готов к использованию, выполнив командлет [New-AzureManagedCache][New-AzureManagedCache], можно посмотреть сведения о кэше, как показано в следующем примере.

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>




<a name="enable-caching"></a>

## <a name="configure-the-cache"></a>Настройка кэша
Для настройки параметров кэша используется вкладка **Настройка** в области "Кэш" на классическом портале Azure. Каждый кэш имеет именованный кэш **по умолчанию** , а предложения кэша Standard и Premium поддерживают до девяти дополнительных именованных кэшей, что в сумме дает десять таких кэшей. Каждый именованный кэш имеет собственный набор параметров, позволяющий осуществлять очень гибкую настройку кэша.

![NamedCaches][NamedCaches]

Для создания именованного кэша введите имя нового кэша в поле **Имя**, задайте необходимые параметры, щелкните **Сохранить** и нажмите кнопку **Да** для подтверждения. Чтобы отменить изменения, щелкните **Отменить**.

## <a name="expiry-policy-and-time-min"></a>Параметры "Политика срока действия" и "Время (мин)"
Параметры **Политика срока действия** и **Время (мин)** используются совместно для определения окончания срока действия кэшированных элементов. Существует три типа значений параметра "Политика окончания срока действия": **Абсолютный**, **Sliding** (Скользящий) и **Никогда**. 

При выборе значения **Абсолютный** интервал срока действия, заданный параметром **Время (мин)**, начинается с момента добавления элемента в кэш. По истечении интервала времени, заданного параметром **Время (мин)** , срок действия элемента заканчивается. 

При выборе значения **Sliding** (Скользящий) интервал срока действия, заданный параметром **Время (мин)**, сбрасывается при каждом обращении к элементу в кэше. Срок действия элемента не истекает до тех пор, пока не закончится заданный параметром **Время (мин)** интервал с момента последнего доступа к элементу.

При выборе значения **Никогда** для параметра **Время (мин)** нужно установить значение **0**, чтобы элементы не имели срока действия.

По умолчанию используется вариант **Абсолютный**, а для параметра **Время (мин)** установлено значение 10 минут. Политика срока действия фиксируется для всех элементов в именованном кэше, но параметр **Время (мин)** можно настроить для каждого элемента с помощью перегрузок **Add** и **Put**, принимающих параметр времени ожидания.

Дополнительные сведения о политиках удаления данных и срока действия см. в разделе [Срок действия и удаление данных][Expiration and Eviction].

## <a name="notifications"></a>Уведомления
Уведомления кэша позволяют приложениям получать асинхронные уведомления при выполнении различных операций кэша в кластере кэша. Уведомления кэша также обеспечивают автоматическое аннулирование локально кэшированных объектов. Дополнительные сведения см. в разделе [Уведомления][Notifications].

> Уведомления доступны только в предложениях кэша Standard и Premium, в предложении кэша Basic они отсутствуют. Дополнительные сведения см. в разделе [Предложения кэша][Cache offerings].
> 
> 

## <a name="high-availability"></a>Высокая доступность
При использовании функции высокой доступности создается резервная копия каждого элемента, добавляемого в кэш. Если в основной копии элемента возникает непредвиденная ошибка, доступ к резервной копии сохраняется.

При обеспечении высокой доступности алгоритмом предусмотрено потребление памяти в двойном объеме для каждого кэшированного элемента. Учитывайте эту особенность при планировании загрузки. Дополнительные сведения см. в разделе [Высокая доступность][High Availability].

> Функция высокой доступности присутствует только в предложении кэша Premium, в предложениях кэша Basic и Standard она отсутствует. Дополнительные сведения см. в разделе [Предложения кэша][Cache offerings].
> 
> 

## <a name="eviction"></a>Удаление данных
Чтобы поддерживать объем памяти в кэше, поддерживается удаление наиболее давно использовавшихся данных. Когда потребление памяти превышает допустимое пороговое значение, объекты удаляются из памяти независимо от того, истек ли срок их действия, чтобы устранить нехватку памяти.
Функция удаления данных включена по умолчанию. Если удаление данных отключено, элементы не будут удаляться из кэша при достижении полной емкости, вместо этого будет происходить сбой операций Put и Add.

Дополнительные сведения о политиках удаления данных и срока действия см. в разделе [Срок действия и удаление данных][Expiration and Eviction].

После настройки кэша можно настроить клиенты кэша, чтобы обеспечить им доступ к кэшу.

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Настройка клиентов кэша
Кэш, созданный с помощью управляемой службы кэша, доступен из приложений в Azure, работающих на веб-сайтах, в веб-ролях и рабочих ролях, а также на виртуальных машинах Azure. Предоставляется пакет NuGet, упрощающий настройку приложений клиентов кэша. 

Для настройки клиентского приложения с помощью пакета NuGet для кэша щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**. 

![NuGetPackageMenu][NuGetPackageMenu]

Введите **WindowsAzure.Caching** в текстовом поле **Поиск в Интернете** и выберите **Windows**  
**Azure** **Cache** в результатах. Щелкните **Установить**, затем щелкните **Я принимаю**.

![NuGetPackage][NuGetPackage]

Пакет NuGet выполняет несколько задач — он добавляет требуемую конфигурацию в файл конфигурации приложения, а также добавляет необходимые ссылки на сборки. Для проектов облачных служб он также добавляет параметр уровня диагностики клиента кэша в файл ServiceConfiguration.cscfg облачной службы.

> Для веб-проектов ASP.NET пакет кэша NuGet также добавляет два закомментированных раздела в файл web.config. Первый раздел позволяет сохранить состояние сеанса в кэше, а второй раздел включает кэширование вывода страниц ASP.NET. Дополнительную информацию см. в разделах [Практическое руководство. Хранение состояния сеанса ASP.NET в кэше] и [Практическое руководство. Хранение кэширования выводимых данных страниц ASP.NET в кэше][How To: Store ASP.NET Page Output Caching in the Cache].
> 
> 

Пакет NuGet добавляет следующие элементы конфигурации в файлы web.config или app.config вашего приложения. Разделы **dataCacheClients** и **cacheDiagnostics** добавляются в элемент **configSections**. При отсутствии элемента **configSections** он создается как дочерний объект элемента **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


Эти новые разделы содержат ссылки на элемент **dataCacheClients**, который также добавляется в элемент **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


После добавления конфигурации замените два следующих элемента в новой конфигурации.

1. Замените **[Имя роли кэша или конечная точка службы]** на конечную точку, которая отображается в области панели мониторинга на классическом портале Azure.
   
    ![Конечная точка][Endpoint]
2. Раскомментируйте раздел securityProperties и замените **[Authentication Key]** ключом аутентификации, который можно найти на классическом портале Azure, щелкнув **Управление ключами** на панели мониторинга кэша.
   
    ![AccessKeys][AccessKeys]

> Эти параметры следует настраивать правильно, иначе клиенты не смогут получить доступ к кэшу.
> 
> 

Для проектов облачных служб пакет NuGet также добавляет параметр **ClientDiagnosticLevel** в разделе **ConfigurationSettings** роли клиента кэша в файле ServiceConfiguration.cscfg. Ниже приведен пример раздела **WebRole1** из файла ServiceConfiguration.cscfg с **ClientDiagnosticLevel**, равным 1, что соответствует значению **ClientDiagnosticLevel** по умолчанию.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Уровень диагностики клиента задает уровень кэширования диагностических данных, собранных для клиентов кэша. Дополнительные сведения см. в разделе [Диагностика и устранение неполадок][Troubleshooting and Diagnostics].
> 
> 

Пакет NuGet также добавляет ссылки на следующие сборки:

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Если проект является веб-проектом, добавляется следующая ссылка сборки:

* Microsoft.Web.DistributedCache.dll.

После настройки проекта клиента для кэширования можно использовать методы, описанные в следующих разделах, для работы с кэшем.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Работа с кэшами
Действия, приведенные в этом разделе, описывают способы выполнения типовых задач при работе с кэшем.

* [Практическое руководство. Создание объекта DataCache][How To: Create a DataCache Object]
* [Практическое руководство. Добавление и извлечение объекта из кэша][How To: Add and Retrieve an Object from the Cache]
* [Практическое руководство. Указание срока действия объекта в кэше][How To: Specify the Expiration of an Object in the Cache]
* [Практическое руководство. Хранение состояния сеанса ASP.NET в кэше][Практическое руководство. Хранение состояния сеанса ASP.NET в кэше]
* [Практическое руководство. Хранение кэширования выводимых данных страниц ASP.NET в кэше][How To: Store ASP.NET Page Output Caching in the Cache]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>Практическое руководство. Создание объекта DataCache
Чтобы работать с кэшем программными средствами, требуется ссылка на кэш. Добавьте следующий код в начало любого файла, из которого вы хотите использовать кэш Azure:

    using Microsoft.ApplicationServer.Caching;

> Если Visual Studio не распознает типы в операторе using даже после установки пакета NuGet для кэша, который добавляет необходимые ссылки, убедитесь, что целевым профилем для проекта является платформа .NET Framework 4 или более поздней версии, и не забудьте выбрать один из профилей, в котором не указан **клиентский профиль**. Инструкции по настройке клиентов кэша см. в разделе [Настройка клиентов кэша][Configure the cache clients].
> 
> 

Существует два способа создать объект **DataCache** . Первый способ — просто создайте **DataCache**, передав имя нужного кэша.

    DataCache cache = new DataCache("default");

После создания экземпляра **DataCache** его можно использовать для взаимодействия с кэшем, как описано в следующих разделах.

Чтобы использовать второй способ, создайте в своем приложении новый объект **DataCacheFactory** с помощью конструктора по умолчанию. В этом случае клиент кэша будет использовать параметры в файле конфигурации. Вызовите метод **GetDefaultCache** нового экземпляра **DataCacheFactory**, который возвращает объект **DataCache**, или вызовите метод **GetCache** и передайте имя своего кэша. Эти методы возвращают объект **DataCache** , который затем можно использовать для программного доступа к кэшу.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>Практическое руководство. Добавление и извлечение объекта из кэша
Чтобы добавить элемент в кэш, можно использовать метод **Add** или **Put**. Метод **Add** добавляет указанный объект в кэш, ключом которого является значение параметра ключа.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Если объект с тем же ключом уже находится в кэше, порождается исключение **DataCacheException** со следующим сообщением:

> ErrorCode:SubStatus. Предпринята попытка создать объект с ключом, который уже существует в кэше. Кэш принимает только уникальные значения ключа для объектов.
> 
> 

Чтобы извлечь объект с указанным ключом, можно воспользоваться методом **Get** . Если объект существует, он возвращается, а если нет, возвращается значение null.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

Метод **Put** добавляет объект с указанным ключом в кэш, если он не существует, или заменяет существующий объект.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>Практическое руководство. Указание срока действия объекта в кэше
По умолчанию срок действия элементов в кэше истекает через 10 минут после их помещения в кэш. Это можно настроить в параметре **Время (мин)** на вкладке "Настройка" для кэша на классическом портале Azure.

![NamedCaches][NamedCaches]

Существует три типа значений параметра **Политика окончания срока действия**: **Никогда**, **Абсолютный** и **Sliding** (Скользящий). Эти значения определяют применение параметра **Время (мин)** для задания срока действия. По умолчанию для параметра **Тип срока действия** задано значение **Абсолютный**. Это означает, что таймер обратного отсчета срока действия элемента запускается в момент помещения элемента в кэш. После истечения указанного периода времени срок действия элемента заканчивается. Если указано значение **Скользящий** , отсчет срока действия элемента сбрасывается при каждом обращении к элементу в кэше, и срок действия элемента истекает только по прошествии указанного периода с момента последнего обращения к элементу. Если указано значение **Никогда**, то для параметра **Время (мин)** следует задать значение **0**, в результате чего элементы будут оставаться действительными все время, пока они находятся в кэше.

Если требуется использовать более длинный или короткий интервал ожидания по сравнению с настроенным в свойствах кэша, можно задать конкретную длительность при добавлении или обновлении элемента в кэше, применив перегрузку **Add** и **Put**, принимающую параметр **TimeSpan**. В следующем примере строка **value** добавляется в кэш с ключом **item** и временем ожидания 30 минут.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Чтобы просмотреть оставшееся время ожидания для элемента в кэше, можно использовать метод **GetCacheItem** для получения объекта **DataCacheItem**, содержащего данные об элементе в кэше, в том числе об оставшемся времени ожидания.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>Практическое руководство. Хранение состояния сеанса ASP.NET в кэше
Поставщик состояний сеансов для кэша Azure представляет собой механизм хранения вне процесса для приложений ASP.NET. Этот поставщик позволяет хранить состояние сеанса в кэше Azure, а не в памяти или в базе данных SQL Server. Чтобы воспользоваться поставщиком состояний сеансов кэширования, сначала настройте свой кэш, а затем настройте приложение ASP.NET для кэша с помощью пакета NuGet для кэша, как описано в разделе [Приступая к работе со службой кэша][Getting Started with Managed Cache Service]. При установке пакета кэша NuGet он добавляет в файл закомментированный раздел с требуемой конфигурацией, позволяющей приложению ASP.NET использовать поставщик состояний сеансов для кэша Azure.

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Если ваш файл web.config не содержит этот закомментированный раздел после установки пакета NuGet для кэша, убедитесь, что установлена самая последняя версия диспетчера пакетов NuGet [отсюда][NuGet Package Manager Installation], после чего удалите и переустановите этот пакет.
> 
> 

Чтобы включить поставщик состояний сеансов для кэша Azure, раскомментируйте указанный раздел. Кэш по умолчанию указан в представленном фрагменте кода. Чтобы использовать другой кэш, укажите его в атрибуте **cacheName** .

Дополнительную информацию об использовании поставщика состояний сеансов для управляемой службы кэша см. в разделе [Поставщик состояний сеансов для кэша Azure][Session State Provider for Azure Cache].

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>Практическое руководство. Хранение кэширования выводимых данных страниц ASP.NET в кэше
Поставщик кэша вывода для кэша Azure представляет собой механизм внепроцессного хранения для выходных данных кэширования. Эти данные предназначены специально для полных HTTP-ответов (кэширование вывода страниц). Поставщик подключается к новой точке расширения поставщика вывода кэша, которая появилась в ASP.NET 4. Чтобы воспользоваться поставщиком вывода кэша, сначала настройте свой кластер кэша, а затем настройте приложение ASP.NET для кэширования с помощью пакета NuGet для кэша, как описано в разделе [Приступая к работе со службой кэша][Getting Started with Managed Cache Service]. При установке пакета кэширования NuGet он добавляет в файл web.config следующий закомментированный раздел с требуемой конфигурацией, позволяющей приложению ASP.NET использовать поставщик кэша вывода для Azure Caching.

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Если ваш файл web.config не содержит этот закомментированный раздел после установки пакета NuGet для кэша, убедитесь, что установлена самая последняя версия диспетчера пакетов NuGet [отсюда][NuGet Package Manager Installation], после чего удалите и переустановите этот пакет.
> 
> 

Чтобы включить поставщик кэша вывода для кэша Azure, раскомментируйте указанный раздел. Кэш по умолчанию указан в представленном фрагменте кода. Чтобы использовать другой кэш, укажите его в атрибуте **cacheName** .

Добавьте директиву **OutputCache** для каждой страницы, для которой требуется кэшировать выходные данные.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

В этом примере кэшированные данные страницы будут оставаться в кэше в течение 60 секунд, а для каждой комбинации параметров будет кэшироваться другая версия страницы. Дополнительные сведения о доступных параметрах см. в разделе [Директива OutputCache][OutputCache Directive].

Дополнительную информацию об использовании поставщика кэша вывода для кэша Azure см. в разделе [Поставщик кэша вывода для кэша Azure][Output Cache Provider for Azure Cache].

<a name="next-steps"></a>

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы ознакомились с основами управляемой службы кэша, перейдите по следующим ссылкам, чтобы перейти к более сложным задачам кэширования.

* Ознакомьтесь со справочником MSDN: [Документация по кэшу Redis][Managed Cache Service].
* Узнайте, как перейти на управляемую службу кэша: [Переход на управляемую службу кэша][Migrate to Managed Cache Service].
* Ознакомьтесь с примерами: [Примеры управляемой службы кэша Azure][Managed Cache Service Samples].

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is Azure Managed Cache Service?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with Managed Cache Service]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[Практическое руководство. Хранение состояния сеанса ASP.NET в кэше]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net

<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg


<!-- LINKS -->
[Azure Classic Portal]: https://manage.windowsazure.com/
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Overview of Azure Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Troubleshooting and Diagnostics]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Cache offerings]: http://go.microsoft.com/fwlink/?LinkId=317277
[Capacity planning]: http://go.microsoft.com/fwlink/?LinkId=320167
[Expiration and Eviction]: http://go.microsoft.com/fwlink/?LinkId=317278
[High Availability]: http://go.microsoft.com/fwlink/?LinkId=317329
[Notifications]: http://go.microsoft.com/fwlink/?LinkId=317276
[Migrate to Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=317347
[Managed Cache Service Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure Managed Cache Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=398555
[How to install and configure Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me




<!--HONumber=Jan17_HO3-->


