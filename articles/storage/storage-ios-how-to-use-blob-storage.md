<properties
    pageTitle="Использование хранилища BLOB-объектов Azure из iOS | Microsoft Azure"
    description="Узнайте, как отправлять, скачивать, перечислять и удалять содержимое больших двоичных объектов с помощью хранилища больших двоичных объектов Azure. Примеры написаны на Objective-C."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="10/07/2015"
    ms.author="micurd"/>

# Использование хранилища BLOB-объектов из iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этой статье показано, как реализовать типичные сценарии с использованием хранилища BLOB-объектов Microsoft Azure. Примеры написаны на Objective-C с использованием [библиотеки хранилища Azure для iOS](https://github.com/Azure/azure-storage-ios). Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** больших двоичных объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия](#next-steps). Можно также загрузить [пример приложения](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample), чтобы просмотреть варианты использования хранилища Azure в приложении iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Сборка файла платформы
Чтобы использовать библиотеку хранилища Azure для iOS, вам сначала необходимо выполнить сборку файла платформы.

1. Прежде всего загрузите или клонируйте [репозиторий azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Перейдите в *azure-storage-ios* -> *Lib* (Библиотеки) -> *Azure Storage Client Library* (Клиентская библиотека хранилища Azure) и откройте файл `Azure Storage Client Library.xcodeproj` в программе Xcode.

3. В левой верхней части окна Xcode измените активную схему с Azure Storage Client Library (Клиентская библиотека хранилища Azure) на Framework (Платформа).

4. Выполните сборку проекта (⌘ + B). На рабочем столе будет создан файл `Azure Storage Client Library.framework`.

## Импорт библиотеки хранилища Azure для iOS в приложение

Библиотеку хранилища Azure для iOS можно импортировать в приложение следующим образом.

1. Создайте новый проект или откройте существующий проект в Xcode.

2. Щелкните проект на левой панели навигации и перейдите на вкладку *General* (Общие) в верхней части редактора проекта.

3. В разделе *Linked Frameworks and Libraries* (Связанные платформы и библиотеки) нажмите кнопку добавления (+).

4. Щелкните *Add Other* (Добавить). Перейдите к только что созданному файлу `Azure Storage Client Library.framework` и добавьте его.

5. В разделе *Linked Frameworks and Libraries* (Связанные платформы и библиотеки) еще раз нажмите кнопку добавления (+).

6. В списке уже предоставленных библиотек найдите библиотеку `libxml2.2.dylib` и добавьте ее в проект.

##Оператор импорта
В файл, в котором будет вызываться API службы хранилища Azure, необходимо включить следующий оператор импорта.

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## Настройка приложения для доступа к хранилищу больших двоичных объектов

Существует два способа проверки подлинности приложения при получении доступа к службам хранения:

- Общий ключ — следует использовать только для тестирования.
- Подписанный URL-адрес (SAS) — используется для рабочих приложений.

###Общий ключ
Проверка подлинности с помощью общего ключа означает, что приложение будет использовать имя и ключ учетной записи для доступа к службам хранилища. Чтобы продемонстрировать возможность использования хранилища BLOB-объектов из iOS, в этом руководстве мы выполним проверку подлинности с помощью общего ключа.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ]Имя и ключ учетной записи, которые предоставляют полный доступ на чтение и запись к связанной учетной записи хранения, будут переданы каждому пользователю, который загружает ваше приложение. Такой подход **не** рекомендуется ввиду возможности компрометации ключа ненадежными клиентами.

При выполнении проверки подлинности с помощью общего ключа вам нужно создать строку подключения. Строка подключения состоит из следующих элементов:

- **DefaultEndpointsProtocol** — можно выбрать HTTP или HTTPS. Настоятельно рекомендуется использовать протокол HTTPS.
- **AccountName** — имя вашей учетной записи хранения.
- **AccountKey** — чтобы получить это значение, на [портале Azure](portal.azure.com) перейдите к вашей учетной записи хранения и щелкните значок **Ключи**. При использовании [классического портала Azure](manage.windowsazure.com) перейдите к вашей учетной записи хранения на портале и щелкните **Управление ключами доступа**. 

Вот как строка подключения будет выглядеть в приложении:

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###Подписанный URL-адрес (SAS)
В приложении iOS проверку подлинности запроса клиента к хранилищу BLOB-объектов рекомендуется выполнять с использованием подписанного URL-адреса (SAS). SAS позволяет предоставлять клиенту доступ к ресурсу на указанный период времени и с указанным набором разрешений. Как владельцу учетной записи хранилища вам понадобится создать SAS для использования клиентами iOS. Для этого вам, скорее всего, нужно будет написать отдельную службу, которая создаст SAS и распространит ее между клиентами. В целях тестирования вы также можете создать SAS с помощью Azure CLI. Обратите внимание, что для создания SAS используются учетные данные общего ключа. При этом клиенты смогут использовать SAS, обращаясь к данным для проверки подлинности, которые содержатся в URL-адресе SAS. При создании SAS можно указать не только интервал времени, в течение которого SAS будет действительным, но и разрешения, которые SAS предоставляет клиенту. Например, в случае с контейнером BLOB-объектов SAS может предоставлять разрешения не только на чтение, запись или удаление BLOB-объекта, содержащегося в контейнере, но и на вывод списка BLOB-объектов в контейнере.

В следующем примере показано, как использовать Azure CLI для создания маркера SAS, который предоставляет разрешения на чтение и запись для контейнера *sascontainer* до 12:00 (UTC) 5 сентября 2015 г.

1. Прежде всего ознакомьтесь с этим [руководством](../xplat-cli/#how-to-install-the-azure-cli), чтобы узнать, как установить Azure CLI и подключиться к подписке Azure.

2. Затем введите следующую команду в Azure CLI, чтобы получить строку подключения для своей учетной записи:

		azure storage account connectionstring show youraccountname

3. Создайте переменную среды с только что созданной строкой подключения:

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. Создайте URL-адрес SAS:

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. URL-адрес SAS должен выглядеть следующим образом:

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. Теперь вы можете получить ссылку на контейнер в приложении iOS, используя URL-адрес SAS следующим образом:

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

Как видите, при использовании маркера SAS в приложении iOS вам не нужно предоставлять имя и ключ учетной записи. Дополнительные сведения о SAS см. в [учебнике по подписанному URL-адресу](../storage-dotnet-shared-access-signature-part-1).

##Асинхронные операции
> [AZURE.NOTE]Все методы, которые выполняют запрос к службе, являются асинхронными операциями. Из образцов кода понятно, что у этих методов есть обработчик завершения. Код внутри обработчика завершения выполняется **после** завершения запроса. Код за пределами обработчика завершения (следующий за ним) выполняется **во время** выполнения запроса.

## Создание контейнера
Каждый BLOB-объект в хранилище Azure должен располагаться в контейнере. В следующем примере показано, как в учетной записи хранения создать контейнер с именем *newcontainer* (если такового еще нет). При выборе имени для контейнера следует учитывать правила именования, упомянутые выше.

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

Убедиться, что все работает, можно на [портале Azure](portal.azure.com) или в любом [обозревателе хранилища](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Кроме того, можно проверить, содержится ли *newcontainer* в списке контейнеров вашей учетной записи хранения.

## Назначение разрешений контейнера
Разрешения контейнера, настраиваемые по умолчанию, — это разрешения на **закрытый** доступ. При этом контейнеры предоставляют и другие возможности доступа.

- **Закрытый**: данные контейнера и BLOB-объекта могут быть прочитаны только владельцем учетной записи.

- **BLOB-объект**: хотя данные BLOB-объекта, содержащиеся в контейнере, могут быть прочитаны через анонимный запрос, данные самого контейнера недоступны. Клиенты не могут перечислять BLOB-объекты внутри с помощью анонимного запроса.

- **Контейнер**: данные контейнера и BLOB-объекта могут быть прочитаны через анонимный запрос. Клиенты могут перечислять BLOB-объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

В следующем примере показано, как создать контейнер с разрешениями на доступ к **контейнеру**, предоставляющими открытый доступ только на чтение всем пользователям Интернета:

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## Отправка BLOB-объекта в контейнер
Как упоминалось в разделе [Основные понятия службы BLOB-объектов](#blob-service-concepts), хранилище BLOB-объектов может содержать три разных типа BLOB-объектов: блочные BLOB-объекты, BLOB-объекты добавления и страничные BLOB-объекты. В настоящее время библиотека хранилища Azure для iOS поддерживает только блочные BLOB-объекты. В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

В следующем примере показано, как отправить блочный BLOB-объект из NSString. Если BLOB-объект с таким именем уже существует в этом контейнере, содержимое этого объекта будет перезаписано.

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

Убедиться, что все работает, можно на [портале Azure](portal.azure.com) или в любом [обозревателе хранилища](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Кроме того, можно проверить, что контейнер *containerpublic* содержит BLOB-объект *sampleblob*. В этом примере мы использовали открытый контейнер, поэтому проверку также можно выполнить, перейдя к URI BLOB-объекта:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Кроме отправки блочного BLOB-объекта из NSString существуют аналогичные методы для NSData, NSInputStream или локального файла.

## Перечисление BLOB-объектов в контейнере
В следующем примере показано, как перечислить все BLOB-объекты в контейнере. При выполнении этой операции необходимо учитывать следующие параметры.

- **continuationToken** — маркер продолжения, который задает начало перечисления. Если маркер не указан, BLOB-объекты будут перечислены с самого начала. Можно перечислить любое количество BLOB-объектов — от нуля до заданного максимума. Даже если этот метод возвращает нулевые результаты, если `results.continuationToken` не равно нулю, это значит, что в службе могут быть дополнительные BLOB-объекты, которые не были перечислены.
- **prefix** — можно указать префикс, который будет использоваться при перечислении BLOB-объектов. Перечислены будут только BLOB-объекты, начинающиеся с этого префикса.
- **useFlatBlobListing** — как упоминалось в разделе [Присвоение имен контейнерам и BLOB-объектам, а также создание ссылок на них](#naming-and-referencing-containers-and-blobs), хотя служба BLOB-объектов представляет собой плоскую схему хранилища, вы можете создать виртуальную иерархию, присваивая BLOB-объектам имена с информацией о пути. Однако неплоское перечисление в настоящее время не поддерживается; эта функция станет доступной в ближайшее время. Сейчас же это значение должно быть `YES`.
- **blobListingDetails** — вы можете указать, какие элементы будут включены при перечислении BLOB-объектов.
	- `AZSBlobListingDetailsNone`: перечисление только зафиксированных BLOB-объектов без возвращения их метаданных.
	- `AZSBlobListingDetailsSnapshots`: перечисление зафиксированных BLOB-объектов и их моментальных снимков.
	- `AZSBlobListingDetailsMetadata`: извлечение метаданных каждого BLOB-объекта, возвращаемого при перечислении.
	- `AZSBlobListingDetailsUncommittedBlobs`: перечисление зафиксированных и незафиксированных BLOB-объектов.
	- `AZSBlobListingDetailsCopy`: включение свойств копий при перечислении.
	- `AZSBlobListingDetailsAll`: перечисление всех доступных зафиксированных BLOB-объектов, незафиксированных BLOB-объектов и моментальных снимков, а также возврат всех метаданных и статуса копий этих объектов.
- **maxResults** — максимальное количество результатов, возвращаемых этой операцией. Значение -1 используется для снятия ограничения.
- **completionHandler** — блок кода, выполняемого с использованием результатов операции перечисления.

В этом примере используется вспомогательный метод для рекурсивного вызова метода перечисления BLOB-объектов при каждом возврате маркера продолжения.

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## Загрузка BLOB-объектов

В следующем примере показано, как загрузить BLOB-объект в объект NSString.

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## Удаление большого двоичного объекта

В следующем примере показано, как удалить BLOB-объект.

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## Удаление контейнера blob-объектов

В следующем примере показано, как удалить контейнер.

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## Дальнейшие действия

Вы изучили основную информацию о хранилище больших двоичных объектов. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- [Библиотека хранилища Azure для iOS]
- [REST API службы хранилища Azure]
- [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy)
- [Блог рабочей группы службы хранилища Azure]

Если у вас есть вопросы по данной библиотеке, вы можете опубликовать их на нашем [форуме MSDN по Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) или на сайте [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Если у вас есть предложения по функциям службы хранилища Azure, вы можете опубликовать их на сайте [отзывов о службе хранилища Azure](http://feedback.azure.com/forums/217298-storage).

[Библиотека хранилища Azure для iOS]: https://github.com/azure/azure-storage-ios
[REST API службы хранилища Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage

<!---HONumber=AcomDC_1217_2015-->