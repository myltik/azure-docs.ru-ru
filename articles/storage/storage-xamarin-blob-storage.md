<properties
	pageTitle="Использование хранилища BLOB-объектов из Xamarin (Предварительная версия) | Microsoft Azure"
	description="Клиентская библиотека хранилища Azure для просмотра Xamarin позволяет разработчикам создавать приложения для магазина Windows, iOS и Android из их собственных пользовательских интерфейсов. В этом учебном материале показано, как использовать Xamarin для создания приложения Android, которое использует хранилище BLOB-объектов Azure."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="micurd"/>

# Использование хранилища BLOB-объектов из Xamarin (Предварительная версия)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Обзор

Xamarin позволяет разработчикам использовать общий язык C# для создания приложений для магазина Windows, iOS и Android из их собственных пользовательских интерфейсов. Клиентская библиотека хранилища Azure для Xamarin — это библиотека предварительного просмотра; Обратите внимание, что он может измениться в будущем.

В этом учебном материале показано, как использовать хранилище BLOB-объектов Azure с приложением Xamarin Android. Прежде чем приступить к рассмотрению кода, обратитесь к разделу [Дальнейшие действия](#next-steps) в конце этого документа для получения дополнительных сведений о хранилище Azure.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание подписи общего доступа

При разработке клиентской библиотеки хранилища Azure для Xamarin, вы не может подтвердить подлинность доступа к учетной записи хранилища Azure, используя ключи доступа к вашей учетной записи. Это позволяет предотвратить попадание данных вашей учетной записи пользователям, которые могут скачать приложение. Вместо этого рекомендуется использование подписи коллективного доступа (SAS), которая скрывает данные вашей учетной записи.

В этом руководстве мы будем использовать Azure PowerShell для создания маркера SAS. Затем мы создадим приложение Xamarin, которое использует созданную SAS.

Прежде всего необходимо установить Azure PowerShell. Для получения инструкций по установке обратитесь к статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md#Install).

Откройте сеанс Azure PowerShell и выполните следующие команды. Не забудьте заменить `ACCOUNT_NAME` и `ACCOUNT_KEY== ` на учетные данные вашей учетной записи хранения. Замените `CONTAINER_NAME` на имя по вашему выбору.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

URI подписи коллективного доступа для нового контейнера должен быть аналогичен следующему:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

Созданная для контейнера подпись коллективного доступа будет действительна в течение следующего дня. Подписанный URL-адрес предоставляет полные права (*т.е.* на чтение, запись, удаление и создание списка) для больших двоичных объектов в контейнере.

Дополнительные сведения о подписанных URL-адресах см. в разделе [Подписанные URL-адреса: создание и использование подписанного URL-адреса в службе BLOB-объектов](storage-dotnet-shared-access-signature-part-2.md).

## Создание нового приложения Xamarin

В этом учебном материале мы создадим приложение Xamarin в Visual Studio. Выполните следующие действия, чтобы создать приложение:

1. Запустите [установщик Visual Studio 2015](https://www.visualstudio.com/), выбрав **пользовательскую** установку и установив флажок в разделе **Разработка кроссплатформенных мобильных приложений > C#/.NET (Xamarin)**. Если вы уже установили Visual Studio, скачайте и установите [Xamarin](http://xamarin.com/platform). Инструкции для Visual Studio и Xamarin см. в статье об [установке и настройке](https://msdn.microsoft.com/library/mt613162.aspx) на сайте MSDN.
3. Откройте Visual Studio и выберите **Файл > Создать > Проект > Android > Пустой App(Android)**.
4. В обозревателе решений щелкните правой кнопкой мыши и выберите **Управление пакетами NuGet**. Найдите **Хранилища Azure** и установите **Хранилище Azure 4.4.0-preview**.

Теперь у вас имеется приложение, которое позволяет вам нажать на кнопку и увеличить значение счетчика.

## Использование подписи коллективного доступа для выполнения операций с контейнером

Далее, добавьте код для выполнения ряда операций с контейнером, используя созданный вами SAS URI.

Прежде всего добавьте следующие инструкции **using**:

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


Добавьте строку для маркера доступа SAS. Замените `"SAS_URI"` строкой с URI SAS, созданного в Azure PowerShell. Затем добавьте строку для вызова метода `UseContainerSAS`, который мы создадим ниже. Обратите внимание, что ключевое слово **async** было добавлено до delegate.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

Добавьте новый метод `UseContainerSAS` в методе `OnCreate`.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## Выполнение приложения

Теперь можно запустить это приложение в симуляторе или на устройстве Android.

Хотя данное руководство ориентировано на Android, метод `UseContainerSAS` также можно использовать и в iOS, а также для приложений магазина Windows. Xamarin также позволяет разработчикам создавать приложения для Windows Phone; однако наша библиотека пока еще не поддерживает это.

## Дальнейшие действия

В этом учебном материале было рассмотрено, как использовать хранилище BLOB-объектов Azure и SAS с приложением Xamarin. В качестве дополнительного упражнения аналогичный шаблон может применяться для создания маркера доступа SAS для таблицы или очереди Azure.

Дополнительные сведения о BLOB-объектах, таблицах и очередях вы можете получить перейдя по следующим ссылкам:

- [Введение в службу хранилища Microsoft Azure](storage-introduction.md)
- [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](storage-dotnet-how-to-use-blobs.md)
- [Приступая к работе с хранилищем таблиц Azure с помощью .NET](storage-dotnet-how-to-use-tables.md)
- [Приступая к работе с хранилищем очередей Azure с помощью .NET](storage-dotnet-how-to-use-queues.md)
- [Приступая к работе с хранилищем файлов Azure в Windows](storage-dotnet-how-to-use-files.md)
- [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0727_2016-->