<properties 
	pageTitle="Динамическое шифрование: настройка политики авторизации ключа содержимого с помощью .NET" 
	description="Узнайте, как настроить политику авторизации для ключа содержимого." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>



#Динамическое шифрование: настройка политики авторизации ключа содержимого 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

Это одна из статей циклов [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md) и [Рабочий процесс для потоковой передачи в службах мультимедиа](media-services-live-streaming-workflow.md).

##Обзор

Службы мультимедиа Microsoft Azure позволяют доставлять содержимое, зашифрованное (динамически) с помощью AES (с использованием 128-битных ключей шифрования) и PlayReady DRM. Они также обеспечивают службы доставки ключей и лицензий PlayReady авторизованным клиентам.

В настоящее время поддерживается шифрование для следующих форматов потоковой передачи: HLS, MPEG DASH и Smooth Streaming. Шифрование формата потоковой передачи HDS и последовательных скачиваний не поддерживается.

Если необходимо, чтобы службы мультимедиа зашифровали ресурс, необходимо связать ключ шифрования (**CommonEncryption** или **EnvelopeEncryption**) с ресурсом (как описано [здесь](media-services-dotnet-create-contentkey.md)), а также настроить политики авторизации ключа (как описано в этой статье).

Когда поток запрашивается проигрыватель, службы мультимедиа используют указанный ключ для динамического шифрования содержимого с помощью шифрования AES или PlayReady. Чтобы расшифровать поток, проигрыватель запросит ключ у службы доставки ключей. Чтобы определить, есть ли у пользователя право на получение ключа, служба оценивает политики авторизации, заданные для ключа.

Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. Политики авторизации ключа содержимого могут иметь одно или несколько ограничений аутентификации: **открытый**, ограничение по **токену** или ограничение по **IP-адресу**. При ограничении по маркеру к политике должен прилагаться маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают маркеры в формате **простого веб-токена** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) и формате **веб-маркера JSON (**JWT).

Службы мультимедиа не предоставляют службы маркеров безопасности. Для выдачи маркеров можно создать пользовательскую службу STS или использовать службу Microsoft Azure ACS. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности (как описано в этой статье). Служба доставки ключей служб мультимедиа возвращает клиенту ключ шифрования, если маркер является допустимым и утверждения маркера соответствуют утверждениям, настроенным для ключа содержимого.

Дополнительную информацию см. в разделе

[Аутентификация по токенам JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Интегрируйте приложение на основе OWIN MVC служб мультимедиа Azure с Azure Active Directory и ограничьте доставку ключей содержимого на основе утверждений JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Используйте Azure ACS для выдачи токенов](http://mingfeiy.com/acs-with-key-services).

###Важные особенности

- Чтобы иметь возможность использовать динамическую упаковку и динамическое шифрование, необходимо иметь по крайней мере одну зарезервированную единицу потоковой передачи. Дополнительную информацию см. в разделе [Масштабирование службы мультимедиа](media-services-manage-origins.md#scale_streaming_endpoints). 
- Ресурс должен содержать набор MP4-файлов с адаптивной скоростью или файлов Smooth Streaming с адаптивной скоростью. Дополнительную информацию см. в разделе [Кодирование ресурса](media-services-encode-asset.md).  
- Отправляйте и кодируйте ресурсы с помощью параметра **AssetCreationOptions.StorageEncrypted**.
- Если вы планируете использовать несколько ключей содержимого, для которых требуется одинаковая конфигурация политики, настоятельно рекомендуется создать единую политику авторизации и повторно использовать ее с несколькими ключами содержимого.
- Служба доставки ключей кэширует политику ContentKeyAuthorizationPolicy и связанные с ней объекты (параметры и ограничения политики) за 15 минут. Если создать политику ContentKeyAuthorizationPolicy и задать для нее ограничение «по маркеру», а затем протестировать ее, то последующее обновление для использования ограничения «открытая» займет примерно 15 минут.
- При добавлении или обновлении политики доставки ресурсов необходимо удалить существующий указатель (если он есть) и создать новый.


##Динамическое шифрование AES-128 

###Ограничение «открытая»

Ограничение открытого типа означает, что система будет доставлять ключ всем, кто его запросит. Это ограничение подходит для тестирования.

В следующем примере создается политика авторизации типа «открытая», которая затем добавляется в ключ содержимого.
	
	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy             
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("Open Authorization Policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	        new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


###Ограничение «по маркеру»

В этом разделе рассказывается о том, как создать политику авторизации ключа содержимого и связать ее с ключом содержимого. Политика авторизации определяет, какие требования авторизации должны быть удовлетворены, чтобы у пользователя было право на получения ключа (например, должен ли список ключей проверки содержать ключ, с помощью которого был подписан маркер).

Чтобы настроить параметр ограничения маркера, необходимо использовать XML для описания требований к авторизации маркера. XML-файл конфигурации ограничений по маркеру должен соответствовать следующей схеме XML.

####<a id="schema"></a>Схема ограничения «по токену»
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

При настройке политики ограничения **по токену** необходимо задать такие параметры, как **основной ключ проверки**, **издатель** и **аудитория**. **Основной ключ проверки **содержит ключ, которым подписан токен, а **издатель** — это служба токенов безопасности, которая выдает токен. **Аудитория** (иногда называется **областью**) описывает назначение токена или ресурс, доступ к которому обеспечивает токен. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в токене значениям в шаблоне. 

При использовании **пакета SDK служб мультимедиа для .NET** можно использовать класс **TokenRestrictionTemplate** для создания токена ограничения. В следующем примере создается политика авторизации с ограничением «по маркеру». В этом примере клиенту нужно будет предоставить маркер, в котором содержатся: ключ подписывания (VerificationKey), поставщик маркера и требуемые утверждения.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

####<a id="test"></a>Тестовый токен

Чтобы получить маркер тестирования на основе маркера ограничения, который использовался для политики авторизации ключа, сделайте следующее.
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


##Динамическое шифрование на основе PlayReady 

Службы мультимедиа позволяют настраивать права и ограничения, которые должны применяться в среде выполнения PlayReady DRM при попытке пользователя воспроизвести защищенное содержимое.

При защите содержимого с помощью PlayReady, среди прочего, в политике авторизации необходимо указать XML-строку, определяющую [шаблон лицензии PlayReady](https://msdn.microsoft.com/library/azure/dn783459.aspx). Классы **PlayReadyLicenseResponseTemplate** и **PlayReadyLicenseTemplate** в пакет SDK служб мультимедиа для .NET помогут определить шаблон лицензии PlayReady.

###Ограничение «открытая»
	
Ограничение открытого типа означает, что система будет доставлять ключ всем, кто его запросит. Это ограничение подходит для тестирования.

В следующем примере создается политика авторизации типа «открытая», которая затем добавляется в ключ содержимого.

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
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

###Ограничение «по маркеру»

Чтобы настроить параметр ограничения маркера, необходимо использовать XML для описания требований к авторизации маркера. XML-файл конфигурации ограничений по токену должен соответствовать схеме XML, показанной в [этом](#schema) разделе.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
	static private string ConfigurePlayReadyLicenseTemplate()
	{
	    // The following code configures PlayReady License Template using .NET classes
	    // and returns the XML string.
	             
	    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	    responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	}

Для получения тестового токена на основе токена ограничения, который использовался для политики авторизации ключа, см. [этот](#test) раздел.

##<a id="types"></a>Типы, используемые при определении ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##Дальнейшие действия
Теперь, после настройки политики авторизации ключа содержимого, перейдите к разделу [Как настроить политику доставки ресурсов](media-services-dotnet-configure-asset-delivery-policy.md).
 

<!---HONumber=July15_HO3-->