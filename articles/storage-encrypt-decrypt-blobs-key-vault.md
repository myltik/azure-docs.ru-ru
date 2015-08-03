<properties
   pageTitle="Шифрование и расшифровка BLOB-объектов в хранилище Microsoft Azure с помощью хранилища ключей Azure"
   description="В этом учебнике описан пошаговый процесс шифрования и расшифровки большого двоичного объекта с помощью шифрования на стороне клиента для хранилища Microsoft Azure с хранилищем ключей Azure "
   services="storage"
   documentationCenter=""
   authors="adhurwit"
   manager=""
   editor=""/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/17/2015"
   ms.author="adhurwit"/>

# Шифрование и расшифровка BLOB-объектов в хранилище Microsoft Azure с помощью хранилища ключей Azure

## Введение
 
В этом учебнике описывается, как использовать шифрование хранилища на стороне клиента — сейчас в предварительной версии — с помощью хранилища ключей Azure — сейчас также в предварительной версии. Учебник включает пошаговое руководство по шифрованию и расшифровке большого двоичного объекта в консольном приложении с помощью этих технологий.

**Предполагаемое время выполнения:** 20 минут

Общую информацию о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault/key-vault-whatis.md)

Общие сведения о шифровании на стороне клиента для хранилища Azure см. в разделе [Шифрование на стороне клиента для хранилища Microsoft Azure — предварительная версия](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx)


## Предварительные требования

Для работы с этим учебником требуется:

- Учетная запись хранения Azure
- Visual Studio 2013 или более поздней версии
- Azure PowerShell 


## Обзор процесса шифрования на стороне клиента

Обзор шифрования на стороне клиента для хранилища Microsoft Azure см. в разделе [http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx "Приступая к работе с шифрованием на стороне клиента для хранилища Microsoft Azure").

Вот процесс, описанный в этой записи блога.

1. SDK клиента хранилища Azure создает ключ шифрования содержимого (CEK) — это одноразовый симметричный ключ.
2. Данные пользователя шифруются с помощью этого ключа CEK.
3. CEK заключается в оболочку (шифруется) с помощью ключа шифрования ключа KEK. KEK определяется идентификатором ключа и может быть парой асимметричных ключей или симметричным ключом. Им можно управлять локально, а также хранить его в хранилище ключей Azure. Сам клиент хранилища никогда не имеет доступа к KEK. Он просто вызывает алгоритм шифрования ключа, предоставляемый хранилищем ключей. Пользователи могут при необходимости использовать настраиваемые поставщики для шифрования и расшифровки ключа.
4. Зашифрованные данные затем передаются в службу хранилища Azure.


## Настройка хранилища ключей Azure
Для продолжения работы с этим учебником необходимо выполнить следующие действия, приведенные в учебнике [Приступая к работе с хранилищем ключей Azure](key-vault/key-vault-get-started.md).

- Создайте хранилище ключей.
- Добавьте ключ или секретный код в хранилище ключей.
- Зарегистрируйте приложение с Azure Active Directory.
- Разрешите приложению использовать ключ или секретный код.

Запишите ClientID и ClientSecret, сформированные при регистрации приложения в Azure Active Directory.

Создайте оба ключа в хранилище ключей. В оставшейся части этого учебника подразумевается, что вы использовали следующие имена: ContosoKeyVault и TestRSAKey1.


## Создайте консольное приложение с пакетами и AppSettings.

В Visual Studio создайте новое консольное приложение.

Добавьте необходимые пакеты nuget в консоли диспетчера пакетов:

	// Note that this is the preview version for Azure Storage
	Install-Package WindowsAzure.Storage -Pre

	// This is the latest stable release for ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	// These are currently only available in preview
	Install-Package Microsoft.Azure.KeyVault -Pre
	Install-Package Microsoft.Azure.KeyVault.Extensions -Pre


Добавьте AppSettings в файл App.Config.

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

Добавьте следующие операторы using и убедитесь, что добавили в проект ссылку на System.Configuration.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## Добавьте метод получения маркера в консольное приложение.

Следующий метод используется классами хранилища ключей, когда необходимо пройти проверку подлинности для доступа к вашему хранилищу ключей.

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"], 
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	
	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");
	
	    return result.AccessToken;
	}

## Получите доступ к хранилищу и хранилищу ключей в программе. 

В функцию Main добавьте следующий код:

	// This is standard code to interact with Blob Storage
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage
	// This is where the GetToken method from above is used
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE]Объектные модели хранилища ключей
>
>Важно понимать, что фактически существуют две объектные модели хранилища ключей, которые необходимо принимать во внимание: одна основана на API REST (пространство имен KeyVault), а другая представляет собой расширение для шифрования на стороне клиента.

> Клиент хранилища ключей взаимодействует с API REST и понимает веб-ключи и секретные коды JSON для двух видов элементов, содержащихся в хранилище ключей.

> Расширения хранилища ключей — это классы, которые специально созданы для шифрования на стороне клиента в хранилище Azure. Они содержат интерфейс для ключей — IKey — и классов, основанный на концепции сопоставителя ключей. Существуют две реализации IKey, которые необходимо знать, — RSAKey и SymmetricKey. Они совпадают с элементами, которые находятся в хранилище ключей, но на данный момент остаются независимыми классами (таким образом, ключ и секретный код, получаемые клиентом хранилища ключей, не реализуют IKey).


## Зашифруйте большой двоичный объект и отправьте
Добавьте следующий код, чтобы зашифровать большой двоичный объект и передать его в свою учетную запись хранения Azure. Затем используется метод ResolveKeyAsync, который возвращает IKey.

	
	// Retrieve the key that you created previously
	// The IKey that is returned here is an RsaKey
	// Remember that we used the names contosokeyvault and testrsakey1
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy. 
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


Ниже приведен снимок экрана с текущего портала управления Azure для большого двоичного объекта, который был зашифрован с помощью шифрования на стороне клиента с ключом, хранящимся в хранилище ключей. Свойство KeyId представляет собой URI для ключа в хранилище ключей, которое выступает в качестве ключа шифрования ключа (KEK). Свойство EncryptedKey содержит зашифрованную версию ключа шифрования содержимого (CEK).

![Снимок экрана, показывающий метаданные большого двоичного объекта, содержащие метаданные шифрования][1]

> [AZURE.NOTE]Если вы рассмотрите конструктор BlobEncryptionPolicy, вы увидите, что он может принимать ключ и (или) сопоставитель. Необходимо помнить, что сейчас сопоставитель нельзя использовать для шифрования, так как он не поддерживает ключ по умолчанию.



## Расшифруйте BLOB-объект и загрузите его
Расшифровка действительна, когда имеют смысл классы сопоставителя. Идентификатор ключа, используемого для шифрования, связан с BLOB-объектом в своих метаданных, поэтому нет причин для получения ключа и запоминания связи между ключом и большим двоичным объектом. Необходимо просто убедиться, что ключ остается в хранилище ключей.

Закрытый ключ RSA Key остается в хранилище ключей, поэтому для реализации расшифровки зашифрованный ключ из метаданных большого двоичного объекта, которые содержат CEC (ключ шифрования содержимого), отправляется в хранилище ключей для расшифровки.

Добавьте следующий код для расшифровки отправленного большого двоичного объекта.

	// In this case we will not pass a key and only pass the resolver because 
	// 	this policy will only be used for downloading / decrypting
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE]Существует несколько других видов сопоставителей, которые облегчают управление ключами, например AggregateKeyResolver и CachingKeyResolver.


## Использование секретных кодов хранилища ключей
Секретный код можно использовать для шифрования на стороне клиента через класс SymmetricKey, так как секретный код фактически является симметричным ключом. Но, как указано выше, секретный код в хранилище ключей не сопоставляется точно SymmetricKey. Необходимо понять несколько вещей.


- Ключ в SymmetricKey должен быть фиксированной длины: 128, 192, 256, 384 или 512 бит.
- Ключ в SymmetricKey должен быть в кодировке Base64.
- Секретный код хранилища ключей, который будет использоваться в качестве SymmetricKey, должен иметь тип содержимого application/octet-stream в хранилище ключей.

Ниже приведен пример создания в PowerShell секретного кода в хранилище ключей, который можно использовать как SymmetricKey:

	// Here we are making a 128-bit key so we have 16 characters. 
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// substitute the VaultName and Name in this command
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

В консольном приложении можно использовать тот же вызов для получения этого секретного кода как SymmetricKey.

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/", 
        CancellationToken.None).GetAwaiter().GetResult();

Вот и все. Вот и все!

## Дальнейшие действия

Дополнительные сведения об использовании хранилища Microsoft Azure с C# см. в разделе [Клиентская библиотека хранилища Microsoft Azure для .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Дополнительные сведения об API REST BLOB-объектов см. в разделе [API REST службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Последние сведения о хранилище Microsoft Azure см. в [блоге команды разработчиков хранилища Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=July15_HO4-->