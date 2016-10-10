<properties 
	pageTitle="Защита содержимого HLS с помощью Apple FairPlay и (или) Microsoft PlayReady | Microsoft Azure" 
	description="В этом разделе приводятся общие сведения и показывается, как использовать службы мультимедиа Azure для динамического шифрования содержимого HTTP Live Streaming (HLS) с использованием Apple FairPlay. Здесь же рассказывается, как использовать службу доставки лицензий для служб мультимедиа для доставки клиентам лицензий FairPlay." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2016"
	ms.author="juliako"/>

# Защита содержимого HLS с помощью Apple FairPlay и (или) Microsoft PlayReady

Службы мультимедиа Azure позволяют использовать динамическое шифрование содержимого HTTP Live Streaming (HLS) в следующих форматах:

- **Незащищенный ключ конверта AES-128**

	Весь фрагмент шифруется в режиме **AES-128 CBC**. Проигрыватели iOS и OSX поддерживают расшифровку потока по умолчанию. Дополнительные сведения см. в [этой статье](media-services-protect-with-aes128.md).

- **Apple FairPlay**

	Отдельные образцы видео и аудиоданных шифруются в режиме **AES-128 CBC**. **Потоковая передача FairPlay** (FPS): интегрируется в операционные системы устройств и поддерживается iOS и Apple TV по умолчанию. Safari в OS X включает FPS, используя поддержку интерфейса расширений зашифрованных носителей (EME).
- **Microsoft PlayReady**

На следующем изображении показан рабочий процесс **динамического шифрования HLS + FairPlay и (или) PlayReady**.

![Защита с помощью FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

В этом разделе показано, как использовать службы мультимедиа Azure для динамического шифрования содержимого HLS с помощью Apple FairPlay. Здесь же рассказывается, как использовать службу доставки лицензий для служб мультимедиа для доставки клиентам лицензий FairPlay.

>[AZURE.NOTE] Если также требуется шифрование содержимого HLS с помощью PlayReady, то необходимо создать общий ключ и связать ее с ресурсом-контейнером. Также необходимо настроить политику авторизации ключей содержимого, как описано в разделе [Использование общего динамического шифрования PlayReady и (или) Widevine DRM](media-services-protect-with-drm.md).

	
## Требования и рекомендации

- При использовании AMS для доставки HLS, зашифрованного с помощью FairPlay, и лицензий FairPlay необходимо следующее:

	- Учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
	- Учетная запись служб мультимедиа. Инструкции по созданию учетной записи служб мультимедиа см. в разделе [Создание учетной записи](media-services-create-account.md).
	- Регистрация в [программе разработки Apple](https://developer.apple.com/).
	- Корпорация Apple требует от владельца содержимого получить [пакет развертывания](https://developer.apple.com/contact/fps/). Подтвердите, что у вас реализован модуль KSM (модуль безопасности ключа) с использованием служб мультимедиа Azure и вы подали запрос на окончательный пакет FPS. В окончательном пакете FPS содержатся инструкции по созданию сертификата и получению ключа ASK, который будет использоваться для настройки FairPlay.

	- Пакет SDK служб мультимедиа Azure для .NET **3.6.0** или более поздней версии.

- На стороне доставки ключей AMS должны быть заданы следующие параметры:
	- **Сертификат приложения (AC)** — PFX-файл, содержащий закрытый ключ. Этот файл создается клиентом и шифруется тем же паролем с использованием клиента.
		
	 	Настраивая политику доставки ключей, клиент указывает пароль и PFX-файл в формате base64.

		В следующих действиях описано, как создать сертификат PFX для FairPlay.
		
		1. Установите OpenSSL из https://slproweb.com/products/Win32OpenSSL.html.
		
			Перейдите в папку, где расположены сертификат FairPlay и другие файлы, полученные от Apple.
		
		2. Командная строка для преобразования сертификата в PEM-файл:
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
		
		3. Командная строка для преобразования PEM-файла в PFX-файл с закрытым ключом (пароль для PFX-файла запрашивается с помощью OpenSSL):
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
		
	- **Пароль к сертификату приложения** — пароль клиента для создания PFX-файла.
	- **Идентификатор пароля к сертификату приложения** — клиент должен передать пароль таким же образом, как при передаче других ключей AMS, используя значение перечисления **ContentKeyType.FairPlayPfxPassword**. В результате он получает идентификатор AMS, который нужно использовать в параметре политики доставки ключей.
	- **IV** — случайное 16-байтовое значение, которое должно соответствовать IV в политике доставки ресурсов. Клиент создает IV и размещает его как в параметре политики доставки ресурсов, так и в параметре политики доставки ключей.
	- **ASK** — секретный ключ приложения, принимается при формировании сертификата с использованием портала разработчиков Apple. Каждая группа разработчиков получает свой, уникальный ASK. Сохраните копию ASK в безопасном месте. Впоследствии ASK необходимо настроить как FairPlayAsk для служб мультимедиа Azure.
	-  **Идентификатор ASK** — предоставляется пользователю при загрузке ASK в AMS. ASK необходимо загрузить с помощью значения перечисления **ContentKeyType.FairPlayASk**. После этого возвращается идентификатор AMS, который будет использован при настройке параметра политики доставки ключей.

- На стороне клиента FPS должны быть заданы следующие значения:
 	- **Сертификат приложения (AC)** — CER- или DER-файл, содержащий открытый ключ, который операционная система использует для шифрования некоторых полезных данных. AMS должен знать этот сертификат, поскольку он необходим для проигрывателя. Служба доставки ключей расшифровывает их, используя соответствующий закрытый ключ.

- Для воспроизведения зашифрованного потока FairPlay необходимо получить реальный ASK, а затем создать реальный сертификат. В результате этого процесса создаются три элемента:

	-  DER-файл,
	-  PFX-файл и
	-  пароль для PFX-файла.
 
- Клиенты, поддерживающие HLS с шифрованием **AES-128 CBC**: Safari в OS X, Apple TV, iOS.

##Процедура настройки общего динамического шифрования FairPlay и службы доставки лицензий

Ниже описаны общие действия, которые необходимо выполнить для защиты ресурсов-контейнеров с помощью FairPlay, используя службу доставки лицензий служб мультимедиа и динамическое шифрование.

1. Создайте ресурс-контейнер и отправьте в него файлы.
1. Закодируйте ресурс-контейнер с файлами в набор MP4-файлов с переменной скоростью.
1. Создайте ключ содержимого и свяжите его с закодированным ресурсом-контейнером.
1. Настройте политику авторизации для ключа содержимого. При создании политики авторизации ключа содержимого необходимо указать следующее:
	
	- Способ доставки (в данном случае FairPlay).
	- Конфигурация параметров политики FairPlay. Дополнительные сведения о настройке FairPlay см. ниже в примере метода ConfigureFairPlayPolicyOptions().
	
		>[AZURE.NOTE] Обычно настраивать параметры политики FairPlay необходимо только один раз, так как у вас будет один набор сертификации и ASK.
	- Ограничения (открытая авторизация или с ограничением по маркеру).
	- Сведения, характерные для типа доставки ключей, определяющего порядок доставки ключа к клиенту.
	
2. Настройте политику доставки ресурсов. Конфигурация политики доставки включает:

	- протокол доставки (HLS);
	- тип динамического шифрования (стандартное шифрование CBC);
	- URL-адрес для приобретения лицензии.
	
	>[AZURE.NOTE]Если вам требуется доставка потока данных, зашифрованного с использованием FairPlay и другой системы DRM, необходимо настроить отдельные политики доставки:
	>
	>- одна политика IAssetDeliveryPolicy для настройки DASH с использованием CENC (PlayReady + WideVine) и Smooth с использованием PlayReady;
	>- другая политика IAssetDeliveryPolicy для настройки FairPlay для HLS.

1. Создайте указатель OnDemand, чтобы получить URL-адрес для потоковой передачи.

##Использование доставки ключей FairPlay приложениями плееров и клиентов

Пользователи могут разрабатывать приложения проигрывателя с помощью пакета SDK для iOS. Для воспроизведения содержимого FairPlay клиентам необходимо реализовать протокол обмена лицензиями. Протокол обмена лицензиями не указывается компанией Apple. Каждое приложение может само выбирать, как отправлять запросы доставки ключей. Служба доставки ключей FairPlay AMS ожидает, что SPC поступит как сообщение публикации www-form-url в следующем формате:

	spc=<Base64 encoded SPC>

>[AZURE.NOTE] В проигрывателе мультимедиа Azure предварительно не настроена поддержка воспроизведения FairPlay. Для воспроизведения FairPlay в Mac OSX клиентам необходимо получить образец проигрывателя из учетной записи разработчика Apple.
 
##URL-адреса потоковой передачи

Если ресурс-контейнер был зашифрован с помощью нескольких систем DRM, то в URL-адресе для потоковой передачи следует использовать тег шифрования: (format='m3u8-aapl', encryption='xxx').

Действительны следующие условия.

- Можно указать только один тип шифрования или ни одного.
- Если к ресурсу-контейнеру был применен только один тип шифрования, то в URL-адресе тип шифрования можно не указывать.
- Тип шифрования вводится без учета регистра.
- Можно указать следующие типы шифрования:
	- **cenc**: общее шифрование (Playready или Widevine);
	- **cbcs-aapl**: Fairplay;
	- **cbc**: шифрование конверта AES.


##Пример .NET


В следующем примере демонстрируется функция, впервые представленная в пакете SDK служб мультимедиа Azure для .NET версии 3.6.0 (возможность использовать службы мультимедиа Azure для доставки содержимого, зашифрованного с использованием FairPlay). Для установки пакета использовалась следующая команда пакета NuGet:

	PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Создайте консольный проект.
1. Используйте NuGet для установки и добавления пакета SDK служб мультимедиа для .NET.
2. Добавьте дополнительную ссылку: System.Configuration.
2. Добавьте файл конфигурации, содержащий сведения об имени и ключе учетной записи:
	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. получить по крайней мере одну единицу потоковой передачи для конечной точки потоковой передачи, из которой вы планируете доставлять содержимое. Дополнительные сведения можно найти в разделе [Настройка конечной точки потоковой передачи](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Замените код в файле Program.cs кодом, приведенным в этом разделе.
			
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
		using Newtonsoft.Json;
		using System.Security.Cryptography.X509Certificates;
		
		namespace DynamicEncryptionWithFairPlay
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
		                                                                        DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
		        {
		            var encodingPreset = "H264 Multiple Bitrate 720p";
		
		            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
		                                    inputAsset.Name,
		                                    encodingPreset));
		
		            var mediaProcessors =
		                _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
		
		            var latestMediaProcessor =
		                mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
		
		            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
		            encodeTask.InputAssets.Add(inputAsset);
		            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
		        {
		            // Create HLS SAMPLE AES encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryptionCbcs);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy          
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Open",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                            Requirements = null
		                        }
		                    };
		
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                ContentKeyDeliveryType.FairPlay,
		                restrictions,
		                FairPlayConfiguration);
		
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with no restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key.
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Token Authorization Policy",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                            Requirements = tokenTemplateString,
		                        }
		                    };
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                       ContentKeyDeliveryType.FairPlay,
		                       restrictions,
		                       FairPlayConfiguration);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with token restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        private static string ConfigureFairPlayPolicyOptions()
		        {
		            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
		            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
		            byte[] askBytes = Guid.NewGuid().ToByteArray();
		            var askId = Guid.NewGuid();
		            // Key delivery retrieves askKey by askId and uses this key to generate the response.
		            IContentKey askKey = _context.ContentKeys.Create(
		                                    askId,
		                                    askBytes,
		                                    "askKey",
		                                    ContentKeyType.FairPlayASk);
		
		            //Customer password for creating the .pfx file.
		            string pfxPassword = "<customer password for creating the .pfx file>";
		            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
		            var pfxPasswordId = Guid.NewGuid();
		            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
		            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
		                                    pfxPasswordId,
		                                    pfxPasswordBytes,
		                                    "pfxPasswordKey",
		                                    ContentKeyType.FairPlayPfxPassword);
		
		            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
		            byte[] iv = Guid.NewGuid().ToByteArray();
		
		            //Specify the .pfx file created by the customer.
		            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
		
		            string FairPlayConfiguration =
		                Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
		                    appCert,
		                    pfxPassword,
		                    pfxPasswordId,
		                    askId,
		                    iv);
		
		            return FairPlayConfiguration;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
		
		            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
		
		            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
		
		            // Get the FairPlay license service URL.
		            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
		
					// The reason the below code replaces "https://" with "skd://" is because
					// in the IOS player sample code which you obtained in Apple developer account, 
					// the player only recognizes a Key URL that starts with skd://. 
					// However, if you are using a customized player, 
					// you can choose whatever protocol you want. 
					// For example, "https". 

		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		                {
		                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
		                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
		                };
		
		            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
		                    "AssetDeliveryPolicy",
		                AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
		                AssetDeliveryProtocol.HLS,
		                assetDeliveryPolicyConfiguration);
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		
		        }
		
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }
		    }
		}


##Дальнейшие действия: схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0928_2016-->