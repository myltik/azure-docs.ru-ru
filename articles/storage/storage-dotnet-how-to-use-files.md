<properties
			pageTitle="Использование хранилища файлов Azure файла с помощью PowerShell и .NET | Microsoft Azure"
            description="Информация о том, как использовать хранилище файлов Azure для создания общих ресурсов файлов и управления содержанием файла. Хранилище файлов позволяет предприятиям переместить приложения, использующие общую папку с файлами SMB, в Azure. Ввод данных учетной записи хранения в виртуальной машине для повторного подключения к общей папке после перезагрузки."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="07/06/2015"
      ms.author="tamram" />

# Использование хранилища файлов Azure с помощью PowerShell и .NET

## Обзор

Служба файлов Azure предоставляет общие папки с помощью стандартного протокола SMB 2.1. Приложения, запущенные в Azure, теперь могут использовать его для общего доступа к файлам виртуальными машинами, использующими стандартные и знакомые API файловых систем, например ReadFile и WriteFile. Кроме того, к файлам можно получить одновременный доступ через интерфейс REST, открывающий различные гибридные сценарии. Наконец, служба файлов Azure основана на той же технологии, что и службы BLOB-объектов, таблиц и очередей, то есть она может использовать существующие средства обеспечения доступности, надежности, масштабируемости и геоизбыточности, встроенные в нашу платформу.

## О данном учебнике

В этом учебнике по началу работы продемонстрированы основы использования хранилища файлов Microsoft Azure. В этом учебнике мы выполним следующее:

- Воспользуемся PowerShell, чтобы создать новый файл Azure для общего доступа, добавить каталог, передать локальный файл для общего доступа и вывести список файлов в каталоге.
- Смонтируем файлы для общего доступа из виртуальной машины Azure, так же как и любой другой общий ресурс SMB.

Для пользователей, которым может понадобиться общий доступ к файлам из локального приложения, а также из виртуальной машины или облачной службы Azure, мы покажем, как использовать клиентскую библиотеку хранилища Azure для .NET для работы с общим доступом к файлам из классического приложения.

> [AZURE.NOTE]Для выполнения примеров кода .NET из этого руководства необходимо наличие клиентской библиотеки хранилища Azure для .NET версии 4.x или более поздней версии. Клиентская библиотека хранилища доступна через [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Создание учетной записи хранения Azure

В настоящее время хранилище файлов Azure находится на стадии предварительной версии. Чтобы получить доступ к предварительной версии, перейдите на страницу [предварительной версии Microsoft Azure](/services/preview/) и запросите доступ к **Файлам Azure**. После одобрения запроса вам придет уведомление о предоставлении доступа к предварительной версии хранилища файлов. Затем можно создать учетную запись хранения для доступа к хранилищу файлов.

> [AZURE.NOTE]Хранилище файлов в настоящее время доступно только для новых учетных записей хранения. После предоставления доступа подписки к хранилищу файлов создайте новую учетную запись хранения для использования в данном руководстве.
>
> Хранилище файлов Azure в настоящее время не поддерживает подписи общего доступа.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание общей папки с помощью PowerShell

Затем мы создадим общую папку с файлами с помощью Azure PowerShell. После создания общей папки необходимо подключить ее из любой файловой системы, поддерживающей SMB 2.1.

### Установите командлеты PowerShell для хранилища Azure

Для подготовки к использованию PowerShell загрузите и установите командлеты Azure PowerShell. См. раздел [Установка и настройка Azure PowerShell](../install-configure-powershell.md) в качестве начальной точки и инструкций по установке.

> [AZURE.NOTE]Командлеты PowerShell для службы файлов доступны только в последней версии модуля Azure PowerShell (версия 0.8.5 и выше). Рекомендуется загрузить и установить или обновить версию модуля Azure PowerShell.

Откройте окно Azure PowerShell. Для этого нажмите кнопку **Пуск** и введите **Azure PowerShell**. Окно Azure PowerShell выполнит загрузку модуля Azure PowerShell.

### Создание объекта контекста и ключа для учетной записи хранения

Создайте объект контекста учетной записи хранения. Контекст включает имя учетной записи хранения и ключ. Указания по копированию ключа учетной записи на портале Azure см. в разделе [Просмотр, копирование и повторное создание ключей доступа к хранилищу](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Замените `storage-account-name` и `storage-account-key` вашим именем учетной записи хранения и ключом, как показано в следующем примере:

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Создание нового ресурса совместно используемых файлов

Затем создайте новую общую папку с именем `logs` в этом примере:

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

У вас появится новый общий ресурс в хранилище файлов. Далее мы добавим каталог и файл.

> [AZURE.IMPORTANT]Имя общей папки должно состоять из символов в нижнем регистре. Дополнительную информацию о присвоении имен общим папкам и файлам см. в разделе [Именование общих ресурсов, каталогов, файлов и метаданных и ссылка на них](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Создание каталога в ресурсе совместно используемых файлов

Затем создайте каталог в ресурсе совместно используемых файлов. В данном примере используется каталог с именем `CustomLogs`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Отправка локального файла в каталог

Затем загрузите локальный файл в хранилище файлов. В данном примере файл передается из `C:\temp\Log1.txt`. Отредактируйте путь к файлу, чтобы он соответствовал пути к существующему файлу на локальном компьютере:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Просмотр списка файлов в каталоге

Чтобы просмотреть, какие файлы имеются в каталоге, можно вывести список файлов. Эта команда также выведет список подкаталогов, однако в данном примере они отсутствуют, и в списке будут перечислены только файлы.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Подключение общей папки из виртуальной машины Azure под управлением Windows

Для демонстрации подключения общей папки с файлами Azure создадим виртуальную машину Azure под управлением Windows и удаленно подключимся к ней для подключения общей папки.

1. Сначала создайте виртуальную машину Azure, выполнив действия, описанные в разделе[Создание виртуальной машины под управлением Windows Server](../virtual-machines-windows-tutorial.md).
2. Затем подключитесь к виртуальной машине, выполнив действия, описанные в разделе [Как войти в систему на виртуальной машине, работающей под управлением Windows Server](../virtual-machines-log-on-windows-server.md).
3. Откройте окно PowerShell на виртуальной машине.

### Ввод данных учетной записи хранения в виртуальной машине.

Перед монтированием общего ресурса введите данные учетной записи хранения в виртуальной машине. Это позволит Windows автоматически восстанавливать подключение к общему ресурсу файлов при перезагрузке виртуальной машины. Чтобы сохранить данные учетной записи, выполните команду `cmdkey` в окне PowerShell на виртуальной машине. Замените `<storage-account-name>` именем учетной записи хранения, а `<storage-account-key>` ключом доступа учетной записи:

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows автоматически восстановит подключение к общему ресурсу файлов при перезагрузке виртуальной машины. Вы можете проверить восстановление подключения к общему ресурсу, выполнив команду `net use` в окне PowerShell.

### Монтирование общего ресурса файлов с использованием введенных данных учетной записи

После установки удаленного подключения к виртуальной машине вы можете выполнить команду `net use`, чтобы подключить общий ресурс, используя следующий синтаксис. Замените `<storage-account-name>` именем учетной записи хранения, а `<share-name>` именем вашего ресурса хранилища файлов.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

> [AZURE.NOTE]Так как вы сохранили данные учетной записи хранения на предыдущем этапе, нет необходимости вводить их повторно с помощью команды `net use`. Если вы еще не сохранили данные учетной записи, добавьте их как параметр вызова команды `net use`.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Теперь можно работать с общим ресурсом хранилища файлов из виртуальной машины, как с обычным диском. Можно выполнять стандартные команды для работы с файлами из интерфейса командной строки, либо просматривать монтированный ресурс и его содержимое с помощью Проводника. Также вы можете запустить на виртуальной машине код, который получит доступ к общей папке с использованием стандартных интерфейсов API ввода-вывода Windows, так как они предоставляются [пространствами имен System.IO](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) в.NET Framework.

Также можно монтировать общий ресурс файлов из роли, запущенной в облачной службе Azure с помощью удаленного подключения к роли.

## Создание локального приложения для работы с хранилищем файлов

Вы можете подключить общую папку из виртуальной машины или облачной службы Azure, как это было показано выше. Однако это нельзя сделать из локального приложения. Для доступа к данным общего ресурса из локального приложения нужно пользоваться API хранилища файлов. В данном примере показано, как работать с общим ресурсом фалов через [клиентскую библиотеку.NET хранилища Azure ](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409).

Чтобы показать, как использовать API из локального приложения, создадим простое консольное приложение, работающее на настольном компьютере.

### Создание консольного приложения и получение сборки

Для создания нового консольного приложения в Visual Studio и установки пакета NuGet хранилища Azure:

1. В Visual Studio, выберите **Файл** -> **Новый проект**, выберите **Windows** -> **Консольное приложение** из списка шаблонов Visual C#.
2. Укажите имя консольного приложения и нажмите кнопку **ДАЛЕЕ**.
3. После создания проекта щелкните правой кнопкой мыши на проекте в обозревателе решений и выберите **Управление пакетами NuGet**. Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

### Сохранение данных учетной записи хранения в файле app.config.

Затем сохраните данные учетной записи хранения в файле app.config вашего проекта. Отредактируйте файл app.config, чтобы он был похож на следующий пример, заменив `myaccount` именем учетной записи хранения, а `mykey` — ключом учетной записи хранения:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]Последняя версия эмулятора хранения Azure не поддерживает хранилище файлов. Строка подключения должна указывать учетную запись хранения Azure облаке, для которой имеется доступ к предварительной версии хранилища файлов.


### Добавление объявлений пространств имен

Откройте в обозревателе решений файл program.cs и добавьте следующие объявления пространств в начало файла:

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### Получение строки подключения программным путем

Вы можете получить сохраненные данные учетной записи из файла app.config с помощью класса `Microsoft.WindowsAzure.CloudConfigurationManager` или `System.Configuration.ConfigurationManager `. В примере показано, как получить свои учетные данные с помощью класса `CloudConfigurationManager` и инкапсулировать их в класс `CloudStorageAccount`. В метод `Main()` в program.cs добавьте следующий код:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Доступ к общему ресурсу хранилища файлов программным путем

Затем добавьте в метод `Main()` следующий код после ранее указанного кода, чтобы получить строку подключения. Этот код получает ссылку на ранее созданный файл и выводит его содержимое в окне консоли.

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        //Ensure that the file exists.
	        if (file.Exists())
	        {
	            //Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Запустите консольное приложение и получите вывод.

## Подключение общей папки из виртуальной машины Azure под управлением Linux

При создании виртуальной машины Azure можно указать образ Ubuntu из коллекции образов дисков, чтобы обеспечить поддержку SMB 2.1. Тем не менее любой дистрибутив Linux, который поддерживает SMB 2.1, поддерживает также общие папки с файлами Azure.

Подключение общей папки Azure в Linux см. в демонстрации [Общее хранилище для Linux в предварительной версии файлов Azure. Часть 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) на сайте Channel 9.

## Дальнейшие действия

Дополнительную информацию о хранилище файлов Azure см. по этим ссылкам.

### Справочные материалы

- [Справочник по клиентской библиотеке хранилища для .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Справочник по REST API службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Записи блога

- [Введение в службы файлов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Сохраняемые подключения к файлам Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
 

<!---HONumber=July15_HO5-->