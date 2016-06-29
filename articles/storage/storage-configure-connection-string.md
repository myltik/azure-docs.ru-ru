<properties 
	pageTitle="Настройка строки подключения к хранилищу Azure | Microsoft Azure"
	description="Настройка строки подключения к учетной записи хранения Azure. Строка подключения содержит сведения, необходимые для проверки подлинности при доступе к учетной записи хранения из приложения во время выполнения."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2016"
	ms.author="tamram"/>

# Настройка строк подключения службы хранилища Azure

## Обзор

Строка подключения содержит сведения для проверки подлинности, необходимые для доступа к данным в учетной записи хранения из приложения во время выполнения. Можно настроить строку подключения, чтобы сделать следующее.

- подключиться к эмулятору хранилища Azure;
- получить доступ к учетной записи хранения в Azure;
- получить доступ к указанным ресурсам Azure через подписанный URL-адрес (SAS).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## Хранение строки подключения

Приложению потребуется получать доступ к строке подключения во время выполнения, чтобы проверять подлинность запросов к службе хранилища Azure. Есть несколько вариантов для хранения строки подключения.

- Для приложения, работающего на ПК или на устройстве, можно хранить строки подключения в файле `app.config ` или `web.config`. Добавьте строку подключения в раздел **AppSettings**.
- Для приложения, работающего в облачной службе Azure, можно хранить строки подключения в [файле схемы конфигурации службы Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Добавьте строку подключения в раздел **ConfigurationSettings** файла конфигурации службы.
- Также можно использовать строку подключения непосредственно в коде. Однако в большинстве случаев рекомендуется хранить строки подключения в файле конфигурации.

Хранение строки подключения в файле конфигурации упрощает обновление строки подключения для переключения между эмулятором хранения и учетной записью хранения Azure в облаке. Вам потребуется только изменить строку подключения, чтобы указать целевую среду.

Вы можете использовать класс [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) для доступа к строке подключения во время выполнения независимо от того, где выполняется приложение.

## Создание строки подключения в эмуляторе хранения

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Дополнительные сведения об эмуляторе хранения см. в разделе [Использование эмулятора хранения Azure для разработки и тестирования](storage-use-emulator.md).

## Создание строки подключения к учетной записи хранения Azure

Чтобы создать строку подключения к учетной записи хранения Azure, используйте формат строки подключения, указанный ниже. Указывает, следует ли подключаться к учетной записи хранения через HTTPS (рекомендуется) или HTTP. Замените `myAccountName` на имя вашей учетной записи хранения и замените `myAccountKey` на ваш ключ доступа учетной записи:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Созданная строка подключения будет выглядеть так, как строка из следующего примера:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Служба хранилища Azure поддерживает как HTTP, так и HTTPS в строке подключения; однако настоятельно рекомендуется использовать HTTPS.

## Создание строки подключения с помощью подписанного URL-адреса

Если у вас есть подписанный URL-адрес (SAS), его можно использовать в строке подключения. Так как SAS содержит сведения URI, необходимые для проверки подлинности запроса, код URI SAS предоставляет протокол, конечную точку службы и необходимые учетные данные для доступа к ресурсу.

Чтобы создать строку подключения, содержащую подписанный URL-адрес, задайте ее в следующем формате:

    BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	SharedAccessSignature=sasToken

Несмотря на то что конечные точки службы необязательны, строка подключения должна содержать по крайней мере одну конечную точку.

По соображениям безопасности с SAS рекомендуется использовать протокол HTTPS. Дополнительные сведения о подписанном URL-адресе см. в разделе [Подписанный URL-адрес: общие сведения о модели SAS](storage-dotnet-shared-access-signature-part-1.md).

>[AZURE.NOTE] При указании SAS в строке подключения в файле конфигурации может потребоваться закодировать специальные символы в URL-адресе.

### Пример SAS службы

Ниже приведен пример строки подключения, которая включает подписанный URL-адрес службы для хранилища BLOB-объектов:

	BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

Еще один пример той же строки подключения с кодированием специальных символов:

	BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

### Пример SAS учетной записи

Ниже приведен пример строки подключения, которая включает подписанный URL-адрес учетной записи для хранилища BLOB-объектов и файлового хранилища: Обратите внимание, что указаны конечные точки для обеих служб.

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl

Еще один пример той же строки подключения с кодированием URL-адреса:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl

## Создание строки подключения к явной конечной точке хранилища

Вы можете явно указать конечные точки службы в строке подключения вместо конечных точек по умолчанию. Чтобы создать строку подключения, определяющую явную конечную точку, укажите полную конечную точку для каждой службы, включая спецификацию протокола (HTTPS (рекомендуется) или HTTP), в следующем формате:

	DefaultEndpointsProtocol=[http|https];
	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	AccountName=myAccountName;
	AccountKey=myAccountKey

Один из сценариев, когда может потребоваться указать явную конечную точку, — если конечная точка хранилища BLOB-объектов сопоставлена с личным доменом. В этом случае в строке подключения можно указать пользовательскую конечную точку хранилища BLOB-объектов и дополнительно указать конечные точки по умолчанию для других служб, если приложение их использует.

Ниже приведены примеры допустимых строк подключения, в которых указывается явная конечная точка для службы BLOB-объектов:

	# Blob endpoint only
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=account-key

	# All service endpoints
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	FileEndpoint=myaccount.file.core.windows.net;
	QueueEndpoint=myaccount.queue.core.windows.net;
	TableEndpoint=myaccount;
	AccountName=storagesample;
	AccountKey=account-key

Значение конечной точки из строки подключения применяется для создания универсальных кодов ресурса (URI) к службе BLOB-объектов, а также определяет форму любых универсальных кодов ресурса (URI), возвращаемых в код.

Обратите внимание, что, если вы решили не указывать конечную точку в строке подключения, вы не сможете получить доступ к данным в этой службе с помощью этой строки подключения.

### Создание строки подключения с суффиксом конечной точки

Чтобы создать строку подключения для службы хранилища в регионах или экземплярах с разными суффиксами конечных точек, например Azure China (Azure для Китая) или Azure Governance (Azure для правительственных организаций), используйте следующий формат строки подключения. Указывает, следует ли подключиться к учетной записи хранения через HTTP или HTTPS. Замените `myAccountName` на имя вашей учетной записи хранения, замените `myAccountKey` на ключ доступа учетной записи и замените `mySuffix` на суффикс URI:


	DefaultEndpointsProtocol=[http|https];
	AccountName=myAccountName;
	AccountKey=myAccountKey;
	EndpointSuffix=mySuffix;


Созданная строка подключения должна выглядеть так, как строка из следующего примера:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

## Анализ строки подключения

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## Дальнейшие действия

- [Использование эмулятора хранения Azure для разработки и тестирования](storage-use-emulator.md)
- [Обучающие ресурсы для хранилища Azure](storage-explorers.md)

<!---HONumber=AcomDC_0615_2016-->