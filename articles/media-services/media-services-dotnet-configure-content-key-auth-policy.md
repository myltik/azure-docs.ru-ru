---
title: "Настройка политики авторизации ключа контента с помощью Media Services .NET SDK | Документы Microsoft"
description: "Подробные сведения о настройке политики авторизации для ключа контента с помощью пакета SDK .NET служб мультимедиа."
services: media-services
documentationcenter: 
author: mingfeiy
manager: cfowler
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 39782bd687c9c1b50699c05e61e57d9c767a8d32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Динамическое шифрование: Настройка политики авторизации ключа контента
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Обзор
 Службы мультимедиа Azure позволяют доставлять потоки MPEG-DASH, Smooth Streaming и HTTP Live Streaming (HLS), защищенные с помощью Advanced Encryption Standard (AES) с помощью 128-битные ключи шифрования или [PlayReady цифровыми правами (DRM) ](https://www.microsoft.com/playready/overview/). С помощью служб мультимедиа можно доставлять потоки ТИРЕ зашифрован Widevine DRM. PlayReady и Widevine шифруются в соответствии со спецификацией общих шифрование (CENC ISO/IEC 23001-7).

Службы мультимедиа также предоставляют служба доставки ключа/лицензий, по которому клиенты могут получить ключи AES или лицензии PlayReady или Widevine воспроизведение зашифрованное содержимое.

Если требуется, чтобы службы мультимедиа шифруется актив, необходимо связать ключ шифрования (CommonEncryption и EnvelopeEncryption) с активом. Дополнительные сведения см. в разделе [Создание ContentKeys в .NET Framework](media-services-dotnet-create-contentkey.md). Необходимо также настроить политики авторизации для ключа (как описано в этой статье).

Когда проигрыватель запрашивает поток, службы мультимедиа используют указанный ключ для динамического шифрования контента с помощью шифрования AES или DRM. Чтобы расшифровать поток, проигрыватель запросит ключ у службы доставки ключей. Чтобы определить, авторизован ли пользователь получить ключ, служба оценивает политики авторизации, заданный для ключа.

Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. Политика авторизации ключа контента может иметь одно или несколько ограничений авторизации. Возможные варианты: ограничение open или маркера. Политики ограниченного токена должен соответствовать маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают только токены в простой веб-токен ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) формат и JSON Web Token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) формате.

Службы мультимедиа не предоставляют службы маркеров безопасности. Можно создать настраиваемую STS или использовать Azure Access Control Service для выдачи токенов. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности (как описано в этой статье). Если маркер является допустимым и утверждения в токене соответствуют настроенным для ключа контента, служба доставки ключей Media Services Возвращает ключ шифрования клиенту.

Дополнительные сведения см. в следующих статьях:

- [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (Аутентификация токена JWT в службах мультимедиа Azure и динамическое шифрование)
- [Интеграция приложений на основе MVC OWIN служб мультимедиа Azure с Azure Active Directory и ограничения доставки ключа содержимого, на основе утверждений JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Некоторые соображения
* При создании учетной записи служб мультимедиа конечной точки потоковой передачи по умолчанию добавляется к учетной записи в состоянии «Остановлена». Чтобы запустить потоковой передачи контента и воспользоваться преимуществами динамической упаковки и динамического шифрования, вашей конечной точки потоковой передачи необходимо в состоянии «Работает». 
* Ресурс должен содержать набор MP4-файлов с адаптивной скоростью или файлов Smooth Streaming с адаптивной скоростью. Дополнительные сведения см. в статье о [кодировании ресурсов](media-services-encode-asset.md).
* Отправка и кодирование активов с помощью параметра AssetCreationOptions.StorageEncrypted.
* Если вы планируете создать несколько ключей контента, которые должны иметь одинаковую конфигурацию политики, рекомендуется создать одну политику авторизации и использовать его с несколькими ключами контента.
* Служба доставки ключей кэширует ContentKeyAuthorizationPolicy и связанные объекты (параметры и ограничения политики) на 15 минут. Можно создать ContentKeyAuthorizationPolicy и указать использование ограничения token, протестируйте его, а потом обновить политику, задав ограничение open. Этот процесс занимает примерно 15 минут до переключения политики на версию open политики.
* При добавлении или обновлении политики доставки актива необходимо удалить все существующие указатель и создать новый указатель.
* В настоящее время не удается зашифровать прогрессивной загрузки.
* Конечной точке потоковой передачи служб мультимедиа задает значение заголовка «Access-Control-Allow-Origin» CORS в предварительный ответ как подстановочный знак "\*". Это значение можно использовать большинство проигрывателей, включая Azure Media Player, Roku и JWPlayer и другие. Однако некоторые проигрыватели, использующих dashjs не работают так, как с режимом учетные данные для «включить», XMLHttpRequest в их dashjs не допускает символ-шаблон «\*» в качестве значения «Access-Control-Allow-Origin». Эти ограничения в dashjs избежать этого при размещении вашего клиента из одного домена, службы мультимедиа можно указать этого домена в заголовке предварительный ответ. Обратитесь за помощью в службу поддержки через портал Azure.

## <a name="aes-128-dynamic-encryption"></a>Динамическое шифрование AES-128
### <a name="open-restriction"></a>Ограничение открытого типа
Ограничение открытого типа означает, что система доставляет ключ всем, кто подает на него запрос. Это ограничение подходит для тестирования.

Следующий пример создает политика открытой авторизации и добавляет его ключ содержимого:

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


### <a name="token-restriction"></a>Ограничение по маркеру
В этом разделе рассказывается о том, как создать политику авторизации ключа содержимого и связать ее с ключом содержимого. Политика проверки подлинности описывает, какие требования проверки подлинности должны быть выполнены, чтобы определить, авторизован ли пользователь для получения ключа. Например список ключей проверки содержит ключ, который был подписан токен?

Чтобы настроить параметр ограничения маркеров, необходимо использовать XML для описания требований маркера авторизации. XML-файл конфигурации ограничений по маркеру должен соответствовать следующей схеме XML:

#### <a name="token-restriction-schema"></a>Схема ограничения по маркеру
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

При настройке политики ограниченного токена, необходимо указать основной ключ проверки, издателя и аудитории параметров. Основной ключ проверки содержит ключ, который был подписан токен. Поставщик является STS, которая выдает маркер. Аудитория (иногда называется областью) описывает назначение маркера или ресурс, доступ к которому обеспечивает маркер. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в маркере значениям в шаблоне.

При использовании пакета SDK служб мультимедиа для .NET, можно использовать класс TokenRestrictionTemplate для создания токена ограничения.
В следующем примере создается политика авторизации с ограничением "по маркеру". В этом примере клиент должен предоставить маркер, который содержит ключ подписывания (VerificationKey), поставщика маркера и необходимых утверждений.

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
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }

#### <a name="test-token"></a>Тестовый маркер
Чтобы получить тестовый токен на основе токена ограничения, которое использовалось для политики авторизации ключа, выполните следующие действия.

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


## <a name="playready-dynamic-encryption"></a>Динамическое шифрование PlayReady
Службы мультимедиа позволяют настроить права и ограничения, которые среда выполнения PlayReady DRM будет соблюдать при попытке пользователя воспроизвести защищенный контент. 

При защите контента с помощью PlayReady, одно из действий, необходимо указать в политике авторизации является XML-строку, которая определяет [шаблон лицензии PlayReady](media-services-playready-license-template-overview.md). В пакет SDK служб мультимедиа для .NET классы PlayReadyLicenseResponseTemplate и PlayReadyLicenseTemplate помогают определить шаблон лицензии PlayReady.

Чтобы узнать, как для шифрования контента с помощью PlayReady и Widevine, см. [используйте PlayReady и/или Widevine динамического общее шифрование](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Ограничение открытого типа
Ограничение открытого типа означает, что система доставляет ключ всем, кто подает на него запрос. Это ограничение подходит для тестирования.

Следующий пример создает политика открытой авторизации и добавляет его ключ содержимого:

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

### <a name="token-restriction"></a>Ограничение по маркеру
Чтобы настроить параметр ограничения маркеров, необходимо использовать XML для описания требований маркера авторизации. XML должно соответствовать схеме XML, показанный в конфигурации ограничения токенов "[маркер ограничения схемы](#token-restriction-schema)» раздела.

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
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Получить тестовый токен на основе токена ограничения, которое использовалось для политики авторизации ключа, в разделе "[тестовый токен](#test-token)» раздела. 

## <a id="types"></a>Типы, используемые при определении ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Настройки политики авторизации ключа содержимого, см. статью [конфигурация политики доставки активов](media-services-dotnet-configure-asset-delivery-policy.md).

