<properties 
	pageTitle="Использование Azure PowerShell со службой хранилища Azure" 
	description="Узнайте, как использовать Azure PowerShell со службой хранилища Azure" 
	services="storage" 
	documentationCenter="na" 
	authors="Selcin" 
	manager="adinah"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="selcint"/>

# Использование Azure PowerShell со службой хранилища Azure 

Добро пожаловать в руководство Azure PowerShell для службы хранилища Azure!

Для выполнения различных задач в службе хранилища Azure можно использовать несколько средств разработки и администрирования, таких как [портал управления Azure](http://manage.windowsazure.com/), [клиентская библиотека хранилища](http://msdn.microsoft.com/library/azure/dn261237.aspx), [REST API](http://msdn.microsoft.com/library/azure/dd179355.aspx) и [Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx). 

В этом руководстве мы изучим, как использовать Azure PowerShell для выполнения типовых задач в службе хранилища Azure. Azure PowerShell - это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Это оболочка командной строки на основе задач и язык сценариев, разработанные для администрирования системы. С помощью PowerShell можно легко контролировать и автоматизировать администрирование служб и приложений Azure. Например, можно использовать командлеты для выполнения задач, которые можно запускать на портале управления Azure. 

Предполагается, что у вас есть опыт работы со службой [хранилища Azure](http://azure.microsoft.com/documentation/services/storage/) и [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Руководство предоставляет несколько сценариев для демонстрации использования PowerShell с службой хранилища Azure. Перед выполнением каждого сценария необходимо обновить переменные сценария в зависимости от конфигурации.

В первом разделе этого руководства содержится краткий обзор службы хранилища Azure и PowerShell. Для получения подробных сведений и инструкций начните с раздела [Предварительные требования для использования Azure PowerShell со службой хранилища Azure](#pre).

## Оглавление
  
 - [Приступая к работе со службой хранилища Azure и PowerShell в течение 5 минут][]
 - [Предварительные требования для использования Azure PowerShell со службой хранилища Azure][] 
 - [Управление учетными записями хранения в Azure][]
	 - [Как задать значение по умолчанию для подписки Azure][]
	 - [Создание новой учетной записи хранения Azure][]
	 - [Как задать учетную запись хранения Azure по умолчанию][]
	 - [Получение списка всех учетных записей хранения Azure в подписке][]
	 - [Создание контекста службы хранилища Azure][]
 - [Управление BLOB-объектами Azure и моментальными снимками BLOB-объектов][]
	 - [Создание контейнера][]
	 - [Передача BLOB-объекта в контейнер][]
	 - [Скачивание BLOB-объектов из контейнера][]
	 - [Копирование BLOB-объектов из одного контейнера хранилища в другой][]
	 - [Удаление BLOB-объекта][]
	 - [Управление моментальными снимками BLOB-объектов Azure][]
	 	- [Создание моментального снимка BLOB-объекта][]
	 	- [Получение списка моментальных снимков BLOB-объектов][]
	 	- [Копирование моментального снимка BLOB-объекта][]
 - [Управление таблицами и сущности таблицы Azure][]
	 - [Создание таблицы][]
	 - [Получение таблицы][]
	 - [Удаление таблицы][]
	 - [Управление сущностями таблицы][]
	 	- [Добавление сущностей таблицы][]
	 	- [Запрос сущностей таблицы][]
	 	- [Удаление сущностей таблицы][]
 - [Управление очередями Azure и очередями сообщений][]
	 - [Создание очереди][]
	 - [Получение очереди][]
	 - [Удаление очереди][]
	 - [Управление очередями сообщений][]
	 	- [Вставка сообщения в очередь][]
	 	- [Удаление следующего сообщения из очереди][]
 - [Управление общими папками и файлами Azure][]
 - [Установка и запросы к аналитике хранилища][]
 - [Как управлять подписанными URL-адресами (SAS) и хранимыми политиками доступа][]
 - [Использование службы хранилища Azure для правительства США и Azure в Китае][]
 - [Дальнейшие действия][]
 
## <a name="getstart"></a>Приступая к работе со службой хранилища Azure и PowerShell в течение 5 минут
В этом разделе показано, как получить доступ к хранилищу Azure через PowerShell в течение 5 минут. 

**Новое в Azure:** Получение подписки Microsoft Azure и учетной записи Майкрософт, связанной с этой подпиской. Информация о вариантах приобретения Azure см. в разделах [Бесплатная пробная версия](http://azure.microsoft.com/pricing/free-trial/), [Варианты приобретения](http://azure.microsoft.com/pricing/purchase-options/) и [предложения для участников](http://azure.microsoft.com/pricing/member-offers/) (для подписчиков MSDN, участников Microsoft Partner Network, BizSpark и других программ корпорации Майкрософт).

**При возникновении путаницы насчет учетных записей Microsoft Azure и подписок:** см. раздел [Управление учетными записями, подписками и административными ролями](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**После создания подписки Microsoft Azure и учетной записи:** 

1.	Скачайте и установите [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) на локальном компьютере.
2.	Запустите интегрированную среду сценариев (ISE) Windows PowerShell: На локальном компьютере перейдите в меню **Пуск**. Введите **Администрирование** и щелкните, чтобы запустить оснастку. В окне **Администрирование** щелкните правой кнопкой мыши **Windows PowerShell ISE** и выберите **Запуск от имени администратора**. 
3.	В окне **Windows PowerShell ISE** щелкните **Файл** > **Создать**, чтобы создать новый сценарий. 
4.	Теперь мы предоставим вам простой сценарий, который показывает основные команды PowerShell для доступа к хранилищу Azure. Сначала сценарий запросит учетные данные учетной записи Azure для ее добавления в локальной среде PowerShell. Затем сценарий установит подписку Azure по умолчанию и создаст новую учетную запись хранения в Azure. Затем сценарий создает новый контейнер в этой новой учетной записи хранения и передает существующий файл образа (BLOB) в этот контейнер. После этого сценарий выводит список всех BLOB-объектов в этом контейнере, создает новый каталог назначения на локальном компьютере и загружает файл образа.
5.	В следующем разделе кода, выберите сценарий между комментариями **#begin** и **#end**. Нажмите клавиши CTRL+C, чтобы скопировать его в буфер обмена. 

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"
    
    	# Give a name to your new storage account. It must be lowercase! 
    	$StorageAccountName="yourstorageaccountname"
    
    	# Choose "West US" as an example.
    	$Location = "West US"
    
    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"
    
    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"
    
    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"
    
    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount
    
    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default
    
    	# Create a new storage account.
    	New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $Location
    
    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    
    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off
    
    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload 
    
    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName
    
    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName
    
    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  
    
    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder
    	#end
    
5.	В **Windows PowerShell ISE**, нажмите CTRL + V, чтобы скопировать сценарий. Щелкните **Файл** > **Сохранить**. В диалоговом окне **Сохранить как** введите имя файла сценария, например "mystoragescript". Щелкните **Сохранить**.

6.	Теперь необходимо обновить переменные сценария на основе заданных параметров конфигурации. Необходимо обновить переменную **$SubscriptionName** данными собственной подписки. Можно сохранить значения других переменных, как указано в сценарии или обновлять их по своему усмотрению.

	- **$SubscriptionName:** необходимо обновить эту переменную именем собственной подписки. Воспользуйтесь одним из следующих трех способов поиска имени подписки.
	
		а. В окне **Windows PowerShell ISE** щелкните **Файл** > **Создать**, чтобы создать новый сценарий. Скопируйте следующий скрипт в новый файл сценария и нажмите кнопку **Отладка** > **Выполнить**. Следующий сценарий сначала запросит учетные данные учетной записи Azure для добавления в учетной записи Azure в локальную среду PowerShell, и отобразит все подписки, которые подключены к локальному сеансу PowerShell. Запишите имя подписки, которая будет использоваться при выполнении этого учебника: 
	 
    		Add-AzureAccount 
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName 


		б. В настоящее время Azure поддерживает два портала: текущий [портал управления Azure](https://manage.windowsazure.com/) и [предварительную версию портала Azure](https://portal.azure.com/). После входа на текущий [портал управления Azure](https://portal.azure.com/) прокрутите окно вниз и выберите пункт **Параметры** в левой части портала. Щелкните **Подписки**. Скопируйте имя подписки, которая будет использоваться при выполнении сценариев, указанных в этом руководстве. В качестве примера см. следующий снимок экрана.

		![Azure Management Portal][Image1]

		в. Если выполнен вход в [предварительной версии портала Azure](https://portal.azure.com/), в меню "Концентратор" в левой части окна выберите **Обзор**. Щелкните **Все**, щелкните **Подписки**. Скопируйте имя подписки, которая будет использоваться при выполнении сценариев в этом руководстве. В качестве примера см. следующий снимок экрана.

		![Azure Preview Portal][Image2]
 

	- **$StorageAccountName:** используйте заданное имя в сценарии или введите новое имя для вашей учетной записи. **Внимание!** Имя учетной записи хранения должно быть уникальным в Azure. Оно также должно быть записано в нижнем регистре!
	 
	- **$Location:** используйте заданное в сценарии значение "Запад США" или выберите другое расположение Azure, например "Восток США", "Северная Европа" и т. д.
	  
	- **$ContainerName:** используйте заданное в сценарии имя или введите новое имя для своего контейнера.
	
	- **$ImageToUpload:** введите путь к изображению на локальном компьютере, например: "C:\Images\HelloWorld.png".
	
	- **$DestinationFolder:** введите путь к локальному каталогу для хранения файлов, загруженных из службы хранилища Azure, например: "C:\DownloadImages".

7.	После обновления переменных сценария в файле "mystoragescript.ps1", щелкните **Файл** > **Сохранить**. Щелкните **Отладка** > **Выполнить** или нажмите клавишу **F5** для выполнения сценария.  

После выполнения сценария появится локальная целевая папка, которая содержит загруженный файл образа. На следующем снимке экрана показан пример вывода:

![Download Blobs][Image3]


> [AZURE.NOTE] В разделе "Приступая к работе со службой хранилища Azure и PowerShell в течение 5 минут" предоставляется краткое введение по использованию Azure PowerShell со службой хранилища Azure. С подробными сведениями и инструкциями мы рекомендуем вам ознакомиться в следующих разделах.

## <a name="pre"></a>Предварительные требования для использования Azure PowerShell со службой хранилища Azure
Требуется подписка Azure и учетная запись для запуска командлетов PowerShell, приведенных в этом руководстве. Список параметров, доступных для подписки Azure, см. в разделе [Приступая к работе с Azure](http://azure.microsoft.com/pricing/free-trial/).

Azure PowerShell - это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Информация об установке и настройке Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Рекомендуется загрузить и установить (или обновить до последней версии) модуль Azure PowerShell перед использованием в этом руководстве. 

Командлеты можно выполнять в консоли Azure PowerShell, стандартной консоли Windows PowerShell или в интегрированной среде сценариев Windows PowerShell (ISE). Например, чтобы открыть **консоль Azure PowerShell**, перейдите в меню "Пуск", введите "Microsoft Azure PowerShell", щелкните нужный пункт правой кнопкой мыши и выберите "Запуск от имени администратора". Чтобы открыть **Windows PowerShell ISE**, перейдите в меню "Пуск" введите "Администрирование" и щелкните оснастку, чтобы запустить. В окне "Администрирование" щелкните правой кнопкой мыши Windows PowerShell ISE и выберите "Запуск от имени администратора". Сведения о детальной настройке и параметрах конфигурации см. в разделе "Установка Azure PowerShell" учебника [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

## <a name="manageaccount"></a>Управление учетными записями хранения в Azure
### <a name="setdefsub"></a>Как задать значение по умолчанию для подписки Azure
Для управления службой хранилища Azure с помощью Azure PowerShell необходимо выполнить проверку подлинности Azure в клиентской среде с помощью проверки подлинности Azure Active Directory или проверки подлинности на основе сертификатов. Дополнительные сведения см. в разделе [Практическое руководство. Подключение к подписке](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect) учебника [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). В этом руководстве используется проверка подлинности Azure Active Directory.

1.	В консоли Azure PowerShell или Windows PowerShell ISE введите следующую команду, чтобы добавить учетную запись Azure в локальную среду PowerShell:

    `Add-AzureAccount`

2.	В окне "Вход в Microsoft Azure" введите электронный адрес и пароль, связанный с вашей учетной записью. Azure выполняет проверку подлинности и сохраняет учетные данные, а затем закрывает окно.

3.	Затем выполните следующую команду, чтобы просмотреть учетные записи Azure в локальной среде PowerShell и убедитесь, что ваша учетная запись имеется в списке:
 
	`Get-AzureAccount`
  
4.	Затем выполните следующий командлет, чтобы просмотреть все подписки, которые подключены к локальному сеансу PowerShell и убедитесь, что подписка есть в списке:

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
 
5.	Чтобы задать подписку Azure по умолчанию, выполните командлет Select-AzureSubscription.
 
	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default

6.	Проверьте имя подписки по умолчанию, выполнив командлет Get-AzureSubscription:

	`Get-AzureSubscription -Default`

7.	Чтобы просмотреть все доступные командлеты PowerShell для службы хранилища Azure, выполните следующую команду:

	`Get-Command -Module Azure -Noun *Storage*`

### <a name="createaccount"></a>Создание новой учетной записи хранения Azure
Для использования службы хранилища Azure потребуется учетная запись хранения. После настройки компьютера для подключения к подписке можно создать новую учетную запись хранения Azure. 

1.	Выполните командлет Get-AzureLocation, чтобы найти все доступные расположения:

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	Затем выполните командлет New-AzureStorageAccount для создания новой учетной записи хранения. Этот пример создает новую учетную запись хранения в центре обработки данных "Запад США".
  
    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $location

Дополнительную информацию см. в разделе [Об учетных записях хранения Azure](http://azure.microsoft.com/documentation/articles/storage-whatis-account/).

> [AZURE.ВНИМАНИЕ!] Имя для учетной записи хранения является уникальным в пределах Azure и должно содержать только строчные буквы. Соглашения об именах и ограничениях см. в разделе [Об учетных записях хранения Azure](http://azure.microsoft.com/documentation/articles/storage-whatis-account/), [Именование и ссылки на контейнеры, BLOB-объекты и метаданные](http://msdn.microsoft.com/library/azure/dd135715.aspx) и [Создание учетной записи хранения](http://msdn.microsoft.com/library/azure/hh264518.aspx).

### <a name="defaultaccount"></a>Как задать учетную запись хранения Azure по умолчанию
В подписке может иметься несколько учетных записей хранения. Можно выбрать одну из них и установить ее в качестве учетной записи хранения по умолчанию для всех команд хранилища в одном сеансе PowerShell. Это позволяет выполнять команды службы хранилища Azure PowerShell без явного указания контекста хранилища. 

1.	Чтобы задать учетную запись по умолчанию для подписки, можно запустить командлет Set-AzureSubscription. 

		$SubscriptionName = "Your subscription name" 
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 

2.	Затем выполните командлет Get-AzureSubscription и убедитесь, что учетная запись хранения связана с вашей учетной записью в подписке по умолчанию. Эта команда возвращает свойства подписки для текущей подписки, включая ее текущую учетную запись хранения.

	    Get-AzureSubscription -Current

### <a name="listaccounts"></a>Получение списка всех учетных записей хранения Azure в подписке
Каждая подписка Azure может включать до 100 учетных записей хранения. Самые последние сведения об ограничениях см. в разделе [Подписка Azure и ограничения служб, квоты и ограничения](http://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).

Выполните следующий командлет, чтобы узнать имена и состояние учетных записей хранения в текущей подписке.

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="createctx"></a>Создание контекста службы хранилища Azure
Контекст службы хранилища Azure - это объект в PowerShell для инкапсуляции учетных данных хранения. Использование контекста хранилища при запуске любого последующего командлета позволяет проводить проверку подлинности запроса без явного указания учетной записи хранения и ключа доступа. Контекст хранилища можно создать несколькими способами, например с помощью имени и ключа доступа учетной записи хранения, токена подписи общего доступа, строки подключения или анонимно. Дополнительную информацию см. в разделе [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Используйте один из следующих трех способов создания контекста хранилища:

- Запустите командлет [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx), чтобы найти основной ключ доступа к хранилищу для вашей учетной записи хранения Azure. Затем вызовите командлет [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) для создания контекста хранилища:

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generate a shared access signature token for an Azure storage container and use it to create a storage context:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	Дополнительные сведения см. в разделе [Создание AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) и [Подписи общего доступа, часть 1: Основные сведения о модели SAS](http://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

- В некоторых случаях может потребоваться указать конечные точки службы при создании нового контекста хранилища. Это может потребоваться при регистрации имени пользовательского домена для учетной записи службы BLOB-объектов или использовании подписанного URL-адреса для доступа к ресурсам хранилища. Установите конечные точки службы в строке соединения и используйте ее для создания нового контекста хранилища, как показано ниже:

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Дополнительную информацию о том, как настроить строку подключения хранилища, см. в разделе [Настройка строк подключения](http://msdn.microsoft.com/library/azure/ee758697.aspx).

Вы настроили компьютер и узнали, как управлять подписками и учетными записями хранения с помощью Azure PowerShell. Перейдите к следующему разделу, чтобы узнать, как управлять BLOB-объектами Azure и моментальными снимками BLOB-объектов.

## <a name="manageblobs"></a>Управление BLOB-объектами Azure и моментальными снимками BLOB-объектов
Хранилище BLOB-объектов Azure - это служба хранения большого количества неструктурированных данных, таких как текстовые или бинарные файлы, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. В этом разделе предполагается, что вы уже знакомы с понятиями службы хранилища BLOB-объектов. Дополнительную информацию см. в разделе [Использование хранилища больших двоичных объектов из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs) и [Основные понятия службы BLOB-объектов](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="container"></a>Создание контейнера
Каждый BLOB-объект в хранилище Azure должен находиться в контейнере. Можно создать закрытый контейнер, используя командлет New-AzureStorageContainer:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Существует три уровня анонимного доступа для чтения: **Отключено**, **BLOB-объект** и **контейнер**. Чтобы запретить анонимный доступ к BLOB-объекту, присвойте параметру разрешение **Отключено**. По умолчанию новый контейнер является закрытым, доступ к нему может осуществляться только владельцем учетной записи. Чтобы разрешить анонимный общий доступ на чтение к BLOB-объектам, но не к метаданным контейнера или списку BLOB-объектов в контейнере, присвойте параметру разрешение **BLOB-объект**. Чтобы разрешить полный общий доступ на чтение к ресурсам BLOB-объектов, метаданным контейнера и списку BLOB-объектов в контейнере, присвойте параметру разрешение **контейнер**. Дополнительные сведения см. в разделе [BlobContainerPublicAccessType](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storageclient.blobcontainerpublicaccesstype(v=azure.95).aspx) перечисления.

### <a name="uploadblob"></a>Передача BLOB-объекта в контейнер
Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты. Дополнительную информацию см. в разделе [Основные сведения о блочных и страничных BLOB-объектах](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Можно использовать командлет [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx), чтобы загрузить BLOB-объекты в контейнер. По умолчанию эта команда отправляет локальные файлы в BLOB-объект. Чтобы указать тип BLOB-объекта можно использовать параметр -BlobType. 

В следующем примере выполняется [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) для получения всех файлов в указанной папке и передачи их в следующий командлет с помощью оператора конвейера. Командлет [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) загружает локальные файлы в контейнер:

    Get-ChildItem -Path C:\Images* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="downblob"></a>Скачивание BLOB-объектов из контейнера
Следующий пример демонстрирует загрузку BLOB-объектов из контейнера. Сначала пример устанавливает соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее первичный ключ доступа. Затем в примере извлекается ссылка на BLOB-объект с помощью командлета [Get AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx). Затем в примере используется командлет [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx), чтобы загрузить BLOB-объекты в локальную целевую папку. 

    #Define the variables.
	ContainerName = "yourcontainername" 
    $DestinationFolder = "C:\DownloadImages" 
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Download blobs from a container. 
    New-Item -Path $DestinationFolder -ItemType Directory -Force 
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="copyblob"></a>Копирование BLOB-объектов из одного контейнера хранилища в другой
Можно асинхронно копировать BLOB-объекты между учетными записями хранения и областями. Следующий пример демонстрирует копирование BLOB-объектов из одного контейнера хранилища в другой в двух разных учетных записях хранения. В примере сначала задаются переменные источника и назначения учетных записей хранения и затем создается контекст хранилища для каждой учетной записи. Далее в примере копируются BLOB-объекты из контейнера-источника в целевой контейнер с помощью командлета [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx). В примере предполагается, что учетные записи хранения и контейнеры источника и назначения уже существуют. 

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey
    
    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey
    
    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext
    
    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Обратите внимание, что этот пример выполняет асинхронное копирование. Отслеживать состояние каждой копии можно, запустив командлет [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx).

### <a name="deleteblob"></a>Удаление BLOB-объекта
Чтобы удалить BLOB-объект, сначала нужно получить ссылку на него, а затем вызвать командлет Remove-AzureStorageBlob с этой ссылкой. Следующий пример удаляет все BLOB-объекты в данном контейнере. В примере сначала устанавливаются переменные для учетной записи хранения, затем создается контекст хранилища. Далее в примере извлекается ссылка на BLOB-объект с помощью командлета [Get AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) и выполняется командлет [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx), чтобы удалить BLOB-объекты из контейнера в хранилище Azure. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob 

## <a name="manageshots"></a>Управление моментальными снимками BLOB-объектов Azure
Azure позволяет создать моментальный снимок BLOB-объекта. Моментальный снимок - это версия BLOB-объекта только для чтения, сделанная в определенный момент времени. После создания моментального снимка его можно читать, копировать или удалять, но нельзя изменять. Моментальные снимки обеспечивают способ резервного копирования BLOB-объекта в том виде, в котором он находится в данный момент времени. Дополнительную информацию см. в разделе [Создание моментального снимка BLOB-объекта](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="createshot"></a>Создание моментального снимка BLOB-объекта
Для создания моментального снимка BLOB-объекта сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод ICloudBlob.CreateSnapshot с этой ссылкой. В следующем примере сначала устанавливаются переменные для учетной записи хранения и затем создается контекст хранилища. Далее в примере извлекается ссылка на BLOB-объект с помощью командлета [Get AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) и выполняется метод [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) для создания моментального снимка. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName
    
    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 

### <a name="listshot"></a>Получение списка моментальных снимков BLOB-объектов
Можно создать любое количество моментальных снимков для BLOB-объекта. Можно вывести список моментальных снимков, связанных с BLOB-объектом, для отслеживания текущих моментальных снимков. В следующем примере используется стандартный BLOB-объект и вызывается командлет [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) для вывода списка моментальных снимков BLOB-объекта.  

    #Define the blob name.
    $BlobName = "yourblobname"
    
    #List the snapshots of a blob.
    Get-AzureStorageBlob -Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="copyshot"></a>Копирование моментального снимка BLOB-объекта
Для восстановления моментального снимка BLOB-объекта можно скопировать моментальный снимок. Подробные сведения и ограничения см. в разделе [Создание моментального снимка BLOB-объекта](http://msdn.microsoft.com/library/azure/hh488361.aspx). В следующем примере сначала устанавливаются переменные для учетной записи хранения и затем создается контекст хранилища. Далее в примере определяются переменные имен контейнера и BLOB-объекта. В примере извлекается ссылка на BLOB-объект с помощью командлета [Get AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) и выполняется метод [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) для создания моментального снимка. Затем в примере выполняется командлет [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) для копирования моментального снимка BLOB-объекта, при этом используется объект ICloudBlob в качестве исходного BLOB-объекта. Не забудьте обновить переменные в зависимости от конфигурации до запуска примера. Обратите внимание, что в следующем примере предполагается, что контейнеры источника и назначения и BLOB-объект источника уже существуют. 

    #Определение учетной записи хранения и контекста.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName
    
    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 
    
    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy -Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Теперь, когда вы узнали, как управлять BLOB-объектами Azure и моментальными снимками BLOB-объектов с помощью Azure PowerShell. Перейдите к следующему разделу, чтобы узнать, как управлять таблицами, очередями и файлами.

## <a name="managetables"></a>Управление таблицами и сущности таблицы Azure
Служба хранилища таблиц Azure - хранилище данных NoSQL, которое можно использовать для хранения и запросов огромных наборов структурированных нереляционных данных. Основными компонентами службы являются таблицы, сущности и свойства. Таблица представляет собой коллекцию сущностей. Сущность - это набор свойств. Каждая сущность может иметь до 252 свойств, которые все являются парами "имя-значение". В этом разделе предполагается, что вы уже знакомы с понятиями хранилища таблиц Azure. Дополнительную информацию см. в разделе [Основные сведения о модели данных службы таблиц](http://msdn.microsoft.com/library/azure/dd179338.aspx) и [Использование табличного хранилища из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

В следующих подразделах рассматривается управление службой хранилища таблиц Azure с помощью Azure PowerShell. Сценарии включают **создание**, **удаление** и **извлечение** **таблиц**, а также **добавление**, **запрос** и **удаление объектов таблиц**.

### <a name="createtable"></a>Создание таблицы
Каждая таблица должна находиться в учетной записи хранения Azure. Следующий пример демонстрирует создание таблицы в хранилище Azure. Сначала в примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем используется командлет [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx), чтобы создать таблицу в хранилище Azure. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey 
    
    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="gettable"></a>Получение таблицы
Можно запрашивать и получить одну или все таблицы в учетной записи хранения. Следующий пример демонстрирует извлечение указанной таблицы с помощью командлета [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx).

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable -Name $tabName -Context $Ctx 

При вызове командлета Get-AzureStorageTable без параметров он получает все таблицы хранилища для учетной записи хранения.

### <a name="remtable"></a>Удаление таблицы
Можно удалить таблицу из учетной записи хранения с помощью командлета [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx).  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="mngentity"></a>Управление сущностями таблицы
В настоящее время Azure PowerShell предоставляет командлеты для непосредственного управления сущностями таблицы. Для выполнения операций над сущностями в таблице, можно использовать классы в [клиентской библиотеке службы хранилища Azure для .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx). 

#### <a name="addentity"></a>Добавление сущностей таблицы
Чтобы добавить сущность в таблицу, необходимо сначала создать объект, который определяет свойства сущности. Сущность может иметь до 255 свойств, включая 3 системных свойства: **PartitionKey**, **RowKey** и **Timestamp**. Вы отвечаете за вставку и обновление значений **PartitionKey** и **RowKey**. Сервер управляет значением **Timestamp**, которое не может быть изменено. Вместе **PartitionKey** и **RowKey** однозначно идентифицируют каждую сущность в пределах таблицы. 

-	**PartitionKey** - определяет раздел, в котором хранится сущность.
-	**RowKey** - однозначно определяет сущности в разделе.

Можно определить до 252 свойств для сущности. Дополнительную информацию см. в разделе [Основные сведения о модели данных службы таблиц](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Следующий пример демонстрирует добавление сущности в таблицу. В примере показано получение таблицы "employee" и добавление в нее нескольких сущностей. Во-первых, устанавливается соединение со службой хранилища Azure с помощью контекста учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем заданная таблица извлекается с помощью командлета [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Если таблица не существует, то используется командлет [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) для создания таблицы в хранилище Azure. Далее в примере определяется пользовательская функция Add-Entity для добавления сущности в таблицу путем указания раздела и ключа строки каждой сущности. Функция Add-Entity вызывает командлет [New-Object](http://technet.microsoft.com/library/hh849885.aspx) с классом [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) для создания объекта сущности. Позже в примере вызывается метод [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) этого объекта сущности для добавления в таблицу. 

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table, 
       [String]$partitionKey, 
       [String]$rowKey,
       [String]$name, 
       [Int]$id
    )
    
      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)
    
      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore
    
    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable -Name $TableName -Context $Ctx
    }
    
    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1 
    Add-Entity $table "Partition1" "Row2" "Jessie" 2 
    Add-Entity $table "Partition2" "Row1" "Christine" 3 
    Add-Entity $table "Partition2" "Row2" "Steven" 4 


#### <a name="queryentity"></a>Запрос сущностей таблицы
Для запроса к таблице используется класс [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx). В следующем примере предполагается, что уже был запущен сценарий, приведенный в разделе о добавлении раздела сущностей данного руководства. Сначала пример устанавливает соединение со службой хранилища Azure, используя контекст хранилища, который включает имя учетной записи хранения и ее ключ доступа. Затем предпринимается попытка получить ранее созданную таблицу "employee" с помощью командлета[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Вызов [New-Object](http://technet.microsoft.com/library/hh849885.aspx) в классе Microsoft.WindowsAzure.Storage.Table.TableQuery создает новый объект запроса. В примере выполняется поиск сущностей, которые содержат столбец "ID", значение которого равно 1, как указано в фильтре строк. Подробные сведения см. в разделе "Построение строк фильтра" в [Запросы таблиц и сущностей](http://msdn.microsoft.com/library/azure/dd894031.aspx). При выполнении этого запроса он возвращает все сущности, которые соответствуют условиям фильтра.    

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Get a reference to a table.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx
    
    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery
    
    #Define columns to select. 
    $list = New-Object System.Collections.Generic.List[string] 
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")
    
    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20
    
    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)
    
    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize 

#### <a name="deleteentity"></a>Удаление сущностей таблицы
Сущность можно удалить с помощью ее ключей раздела и строки. В следующем примере предполагается, что уже был запущен сценарий, приведенный в разделе о добавлении раздела сущностей данного руководства. Сначала пример устанавливает соединение со службой хранилища Azure, используя контекст хранилища, который включает имя учетной записи хранения и ее ключ доступа. Затем предпринимается попытка получить ранее созданную таблицу "employee" с помощью командлета[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Если таблица существует, в примере вызывается метод [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) для получения сущности, основываясь на значениях ключа раздела и строки. Затем передайте сущность в метод [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) для удаления. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null) 
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="managequeues"></a>Управление очередями Azure и очередями сообщений
Хранилище очередей Azure - это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. В этом разделе предполагается, что вы уже знакомы с понятиями службы хранилища очередей Azure. Дополнительную информацию см. в разделе [Использование хранилища очередей из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/) и [Основные понятия службы очередей](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/#concepts).

В этом разделе показано, как управлять службой хранилища очередей Azure с помощью Azure PowerShell. Сценарии использования включают **вставку** и **удаление** сообщений очереди, а также **создание**, **удаление** и **получение очередей**.
 
### <a name="createqueue"></a>Создание очереди
Сначала в этом примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем он вызывает командлет [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx), чтобы создать очередь с именем "queuename". 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue -Name $QueueName -Context $Ctx 

Информация о соглашениях об именах для службы очередей Azure см. в разделе [Именование очередей и метаданных](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="getqueue"></a>Получение очереди
Можно запрашивать и получать указанную очередь или список всех очередей в учетной записи хранения. Следующий пример демонстрирует извлечение указанной очереди с помощью командлета [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx).

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $Ctx 

При вызове командлета [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) без параметров возвращается список всех очередей.

### <a name="remqueue"></a>Удаление очереди
Для удаления очереди и всех сообщений, содержащихся в ней, вызовите командлет Remove-AzureStorageQueue. В следующем примере показан способ удаления указанной очереди, используя командлет Remove-AzureStorageQueue. 

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue -Name $QueueName -Context $Ctx 

### <a name="mngqueuemsg"></a>Управление очередями сообщений
В настоящее время Azure PowerShell предоставляет командлеты для непосредственного управления очередью сообщений. Для выполнения операций в очереди сообщений можно использовать классы [клиентской библиотеки службы хранилища Azure для .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx). 

#### <a name="addqueuemsg"></a>Вставка сообщения в очередь
Чтобы вставить сообщение в существующую очередь, сначала необходимо создать новый экземпляр класса [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Затем вызовите метод [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Для создания CloudQueueMessage можно использовать либо строку (в формате UTF-8), либо массив байтов.
 
Следующий пример демонстрирует добавление сообщений в очередь. Сначала в примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем извлекается указанная очередь с помощью командлета [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx). Если очередь существует, командлет [New-Object](http://technet.microsoft.com/library/hh849885.aspx) используется для создания экземпляра класса [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Позже, в примере вызывается метод [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) этого объекта сообщения, чтобы добавить его в очередь. Ниже приведен код, который извлекает очередь и вставляет сообщение 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx 
    
    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"
    
       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    } 


#### <a name="dequeuemsg"></a>Удаление следующего сообщения из очереди
Код удаляет сообщение из очереди в два этапа. При вызове метода [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessage**, становится невидимым для другого кода, считывающего сообщения из этой очереди. Чтобы завершить удаление сообщения из очереди, необходимо вызвать метод [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки сообщения.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx
    
    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)
    
    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage) 

## <a name="files"></a>Управление общими папками и файлами Azure
Хранилище файлов Azure предоставляет общее хранилище для приложений с помощью стандартного протокола SMB 2.1. Виртуальные машины Microsoft Azure и облачные службы могут совместно использовать файл данных между компонентами приложения через подключенные общие ресурсы, локальные приложения могут обращаться к данным файлов в общей папке через API хранилища файлов или Azure PowerShell.

Подробные сведения о хранилище файлов Azure см. в разделе [Использование хранилища файлов Azure ](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/) и [REST API службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx).

Дополнительную информацию об управлении хранилищем файлов Azure с помощью Azure PowerShell см. в разделе [Создание общей папки с помощью PowerShell](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#use-cmdlets).

## <a name="stganalytics"></a>Установка и запросы к аналитике хранилища
[Аналитика службы хранилища Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) позволяет собирать показатели (метрики хранилища) из учетных записей хранения Azure и журналов (регистрация хранилища) данные о запросах, отправленных в вашу учетную запись хранения. Метрики хранилища можно использовать для наблюдения за работоспособностью учетной записи хранения и ведения журнала хранилища для диагностики и устранения проблем, связанных с вашей учетной записью хранения.
По умолчанию для служб хранилища метрики хранилища не включены. Вы можете включить наблюдение с помощью портала управления Azure, Windows PowerShell или программно с помощью интерфейса API хранилища. Ведение журналов хранилища производится на стороне сервера и позволяет записывать сведения об успешных и неудачных запросах в вашей учетной записи хранения. Эти журналы позволяют просмотреть подробные сведения об операциях чтения, записи и удаления для таблиц, очередей и BLOB-объектов, а также причины неудачных запросов.

Чтобы узнать, как включить и просмотреть метрики хранилища данных с помощью PowerShell, см. раздел [Включение метрики хранилища с помощью PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Чтобы узнать, как включить и получить журнал хранилища данных с помощью PowerShell, см. 
[Включение ведения журнала хранилища с помощью PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) и [Поиск журнала хранилища данных](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Подробные сведения об использовании метрик хранилища и ведения журнала для устранения неполадок хранилища см. в разделе [Мониторинг, диагностирование и устранение неполадок хранилища Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).

## <a name="sas"></a>Как управлять подписанными URL-адресами (SAS) и хранимыми политиками доступа
Подписанные URL-адреса являются важной частью модели обеспечения безопасности для любого приложения, использующего хранилище Azure. Их удобно применять в целях предоставления ограниченных разрешений для вашей учетной записи хранения тем клиентам, которые нельзя передавать ключ учетной записи. По умолчанию только владелец учетной записи хранения может обращаться к большим двоичным объектам, таблицам и очередям в пределах этой учетной записи. Если вашей службе или приложению нужно сделать эти ресурсы доступными другим клиентам без совместного использования ключа доступа, вы можете воспользоваться следующими тремя вариантами:

- Задайте такие разрешения контейнера, которые предоставляют анонимный доступ для чтения контейнера и его больших двоичных объектов. Такие разрешения нельзя задать для таблиц и очередей.
- Используйте подписанный URL-адрес, обеспечивающий ограниченные права доступа к контейнерам, большим двоичным объектам, очередям и таблицам в заданный интервал времени.
- Используйте хранимую политику доступа, чтобы получить дополнительный уровень контроля над подписанными URL-адресами для контейнера, его больших двоичных объектов, очереди или таблицы. С помощью хранимой политики доступа можно изменить время начала, срок действия и разрешения для подписи, а также отозвать подпись после ее выдачи.

Подписанный URL-адрес может быть в одной из двух следующих форм.

- **Одноранговый подписанный URL-адрес** При создании однорангового подписанного URL-адреса время начала его действия, время окончания его действия и разрешения указываются в универсальном коде ресурса подписанного URL-адреса. Этот тип подписанного URL-адреса можно создать для контейнера, BLOB-объекта, таблицы или очереди, и, помимо этого, отозвать его нельзя.
- **Подписанный URL-адрес с хранимой политикой доступа** Хранимая политика доступа определяется в контейнере ресурсов - контейнере больших двоичных объектов, таблиц или очередей - и может использоваться для управления ограничениями одного или нескольких подписанных URL-адресов. При сопоставлении подписи общего доступа с хранимой политикой доступа эта подпись наследует ограничения (время начала, время окончания и разрешения), определенные для данной хранимой политики доступа. Подписанный URL-адрес этого типа можно отозвать.

Дополнительную информацию см. в учебнике [Подписанные URL-адреса](storage-dotnet-shared-access-signature-part-1.md), [Часть 1. Общие сведения о модели SAS](storage-dotnet-shared-access-signature-part-1.md), и [Управление доступом к ресурсам службы хранилища Azure](http://msdn.microsoft.com/library/azure/ee393343.aspx).

В следующих разделах вы узнаете, как создать маркер подписанного URL-адреса и хранимую политику доступа для таблиц Azure. Azure PowerShell предоставляет одинаковые командлеты для контейнеров, больших двоичных объектов и очередей. Для выполнения сценариев в этом разделе скачайте [Azure PowerShell версии 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) или более поздней версии.

### <a name="sub1"></a>Как создать маркер подписанного URL-адреса на основе политики
Используйте командлет New-AzureStorageTableStoredAccessPolicy для создания новой хранимой политики доступа. Затем вызовите командлет [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx), чтобы создать новый маркер подписанного URL-адреса на основе политики для таблицы хранилища Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="sub2"></a>Как создать маркер однорангового (неотзываемого) подписанного URL-адреса
Затем вызовите командлет [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx), чтобы создать новый одноранговый (неотзываемый) маркер подписанного URL-адреса для таблицы хранилища Azure.

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="sub3"></a>Как создать хранимую политику доступа
Используйте командлет New-AzureStorageTableStoredAccessPolicy для создания новой хранимой политики доступа для таблицы хранения Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="sub4"></a>Как обновить хранимую политику доступа
Используйте командлет Set-AzureStorageTableStoredAccessPolicy для обновления существующей хранимой политики доступа для таблицы хранения Azure.

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="sub5"></a>Как удалить хранимую политику доступа
Используйте командлет Remove-AzureStorageTableStoredAccessPolicy для удаления хранимой политики доступа в таблице хранения Azure.

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="gov"></a>Использование службы хранилища Azure для правительства США и Azure в Китае
Среда Azure является независимым развертыванием Microsoft Azure, таким как [Azure Government для правительства США](http://azure.microsoft.com/features/gov/), [AzureCloud для глобального Azure](https://manage.windowsazure.com) и [AzureChinaCloud для Azure под управлением 21Vianet в Китае](http://www.windowsazure.cn/). Можно выполнить развертывание новых сред Azure для правительства США и Китая. 

Для использования службы хранилища Azure с AzureChinaCloud необходимо создать контекст хранилища, связанный с AzureChinaCloud. Выполните следующие действия, чтобы приступить к работе.

1.	Запустите командлет [Get AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx), чтобы просмотреть доступные среды Azure:

    `Get-AzureEnvironment`

2.	Добавьте учетную запись Azure Китай в Windows PowerShell:

    `Add-AzureAccount -Environment AzureChinaCloud`

3.	Создаете контекст хранилища для учетной записи AzureChinaCloud:

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Для использования службы хранилища Azure с [Azure для правительства США](http://azure.microsoft.com/features/gov/) следует определить новую среду и затем создать новый контекст хранилища с данной средой:

1. Вызовите командлет [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx), чтобы создать новую среду Azure для частного центра обработки данных. 

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. Запустите командлет [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx), чтобы создать новый контекст хранилища для этой новой среды, как показано ниже. 
   
	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

Дополнительные сведения см. в разделе:

- [Руководство для разработчиков Microsoft Azure Government](../azure-government-developer-guide.md). 
- [Различия между AzureCloud для глобального Azure и AzureChinaCloud для Azure под управлением 21Vianet в Китае](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next"></a>Дальнейшие действия
В этом руководстве вы узнали, как управлять службой хранилища Azure с помощью Azure PowerShell. Ниже приведены некоторые связанные статьи и ресурсы для их изучения.

- [Документация по службе хранилища Azure](http://azure.microsoft.com/documentation/services/storage/)
- [Справочник MSDN по службе хранилища Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Командлеты PowerShell службы хранилища Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Справочник по Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Приступая к работе со службой хранилища Azure и PowerShell в течение 5 минут]: #getstart
[Предварительные требования для использования Azure PowerShell со службой хранилища Azure]: #pre
[Управление учетными записями хранения в Azure]: #manageaccount
[Как задать значение по умолчанию для подписки Azure]: #setdefsub
[Создание новой учетной записи хранения Azure]: #createaccount
[Как задать учетную запись хранения Azure по умолчанию]: #defaultaccount
[Получение списка всех учетных записей хранения Azure в подписке]: #listaccounts
[Создание контекста службы хранилища Azure]: #createctx
[Управление BLOB-объектами Azure и моментальными снимками BLOB-объектов]: #manageblobs
[Создание контейнера]: #container
[Передача BLOB-объекта в контейнер]: #uploadblob
[Скачивание BLOB-объектов из контейнера]: #downblob
[Копирование BLOB-объектов из одного контейнера хранилища в другой]: #copyblob
[Удаление BLOB-объекта]: #deleteblob
[Управление моментальными снимками BLOB-объектов Azure]: #manageshots
[Создание моментального снимка BLOB-объекта]: #createshot
[Получение списка моментальных снимков BLOB-объектов]: #listshot
[Копирование моментального снимка BLOB-объекта]: #copyshot
[Управление таблицами и сущности таблицы Azure]: #managetables
[Создание таблицы]: #createtable
[Получение таблицы]: #gettable
[Удаление таблицы]: #remtable
[Управление сущностями таблицы]: #mngentity
[Добавление сущностей таблицы]: #addentity
[Запрос сущностей таблицы]: #queryentity
[Удаление сущностей таблицы]: #deleteentity
[Управление очередями Azure и очередями сообщений]: #managequeues
[Создание очереди]: #createqueue
[Получение очереди]: #getqueue
[Удаление очереди]: #remqueue
[Управление очередями сообщений]: #mngqueuemsg
[Вставка сообщения в очередь]: #addqueuemsg
[Удаление следующего сообщения из очереди]: #dequeuemsg
[Управление общими папками и файлами Azure]: #files
[Установка и запросы к аналитике хранилища]: #stganalytics
[Как управлять подписанными URL-адресами (SAS) и хранимыми политиками доступа]: #sas
[Использование службы хранилища Azure для правительства США и Azure в Китае]: #gov
[Дальнейшие действия]: #next

<!--HONumber=47-->
 