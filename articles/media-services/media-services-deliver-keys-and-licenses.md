---
title: "Использование служб мультимедиа Azure для доставки лицензий DRM или ключей AES"
description: "В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки лицензий PlayReady и (или) Widevine и ключей AES и использование локальных серверов для выполнения других задач (кодирование, шифрование, потоковая передача)."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 02bf743d310519477bb87a2930a2afe687c62c4e


---
# <a name="use-azure-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Использование служб мультимедиа Azure для доставки лицензий DRM или ключей AES
Службы мультимедиа Azure (AMS) позволяют принимать и кодировать содержимое, добавлять функции защиты содержимого, а также передавать содержимое в потоковом режиме (см. [эту статью](media-services-protect-with-drm.md)). Однако некоторые клиенты хотят использовать AMS только для доставки лицензий и (или) ключей, а выполнять кодирование, шифрование и потоковую передачу содержимого — с помощью своих локальных серверов AMS. В этой статье содержатся сведения об использовании AMS для доставки лицензий PlayReady и (или) Widevine и использовании локальных серверов для остальных задач. 

## <a name="overview"></a>Обзор
Службы мультимедиа обеспечивают доставку лицензий PlayReady и DRM Widevine, а также ключей AES-128. Они также предоставляют API-интерфейсы для настройки прав и ограничений, которые должны применяться в среде выполнения DRM, когда пользователь воспроизводит защищенное DRM содержимое. Когда пользователь запрашивает защищенное содержимое, приложение проигрывателя запросит лицензию из службы лицензий AMS. Служба лицензий AMS выдаст лицензию проигрывателю (если он авторизован). Лицензии PlayReady и Widevine содержат ключ расшифровки, который может использоваться клиентским проигрывателем для расшифровки и потоковой передачи содержимого.

Службы мультимедиа поддерживают несколько способов авторизации пользователей, которые запрашивают лицензии или ключи. При настройке политики авторизации ключа содержимого можно задать одно или несколько ограничений: открытая авторизация или с ограничением по маркеру. При ограничении по маркеру к политике должен прилагаться маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают маркеры в формате простого веб-маркера (SWT) и формате веб-маркера JSON (JWT).

На следующей схеме представлены основные действия, которые необходимо выполнить, чтобы использовать AMS для доставки лицензий PlayReady и Widevine. При этом остальные задачи выполняются с помощью локальных серверов.

![Защита с помощью PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="download-sample"></a>Скачивание образца
Пример, описанный в этой статье, можно скачать [по этой ссылке](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses).

## <a name="net-code-example"></a>Пример кода .NET
В примере кода в этой статье показано, как создать общий ключ содержимого и получить URL-адреса для получения лицензии PlayReady или Widevine. Вам нужно настроить локальный сервер и получить следующие сведения из AMS: **ключ содержимого**, **идентификатор ключа**, **URL-адрес для получения лицензии**. После настройки локального сервера можно передавать потоки данных с собственного сервера потоковой передачи. Поскольку зашифрованный поток указывает на сервер лицензирования AMS, проигрыватель будет запрашивать лицензии из AMS. При выборе проверки подлинности маркеров сервер лицензирования AMS проверит маркер, отправленный по протоколу HTTPS, и (если он является допустимым) доставит лицензию обратно в проигрыватель. (В примере кода показано только создание общего ключа содержимого и получение URL-адресов для получения лицензий PlayReady или Widevine. Если вам требуется доставка ключей AES-128, нужно создать ключ содержимого типа "конверт" и получить URL-адрес для получения ключа. Инструкции см. в [этой статье](media-services-protect-with-aes128.md).)

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Threading;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
    using Newtonsoft.Json;


    namespace DeliverDRMLicenses
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

            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                bool tokenRestriction = true;
                string tokenTemplateString = null;


                IContentKey key = CreateCommonTypeContentKey();

                // Print out the key ID and Key in base64 string format
                Console.WriteLine("Created key {0} with key value {1} ", 
                    key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));

                Console.WriteLine("PlayReady License Key delivery URL: {0}", 
                    key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));

                Console.WriteLine("Widevine License Key delivery URL: {0}",
                    key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));

                if (tokenRestriction)
                    tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                else
                    AddOpenAuthorizationPolicy(key);

                Console.WriteLine("Added authorization policy: {0}", 
                    key.AuthorizationPolicyId);
                Console.WriteLine();
                Console.ReadLine();
            }

            static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
            {

                // Create ContentKeyAuthorizationPolicy with Open restrictions 
                // and create authorization policy          

                List<ContentKeyAuthorizationPolicyRestriction> restrictions = 
                    new List<ContentKeyAuthorizationPolicyRestriction>
                {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                };

                // Configure PlayReady and Widevine license templates.
                string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, PlayReadyLicenseTemplate);

                IContentKeyAuthorizationPolicyOption WidevinePolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.Widevine,
                        restrictions, WidevineLicenseTemplate);

                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with no restrictions").
                            Result;


                contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                // Associate the content key authorization policy with the content key.
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;
            }

            public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
            {
                string tokenTemplateString = GenerateTokenRequirements();

                List<ContentKeyAuthorizationPolicyRestriction> restrictions = 
                    new List<ContentKeyAuthorizationPolicyRestriction>
                {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                };

                // Configure PlayReady and Widevine license templates.
                string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, PlayReadyLicenseTemplate);

                IContentKeyAuthorizationPolicyOption WidevinePolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                        ContentKeyDeliveryType.Widevine,
                            restrictions, WidevineLicenseTemplate);

                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with token restrictions").
                            Result;

                contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

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

                //The PlayReadyLicenseResponseTemplate class represents the template 
                //for the response sent back to the end user. 
                //It contains a field for a custom data string between the license server 
                //and the application (may be useful for custom app logic) 
                //as well as a list of one or more license templates.

                PlayReadyLicenseResponseTemplate responseTemplate = 
                    new PlayReadyLicenseResponseTemplate();

                // The PlayReadyLicenseTemplate class represents a license template 
                // for creating PlayReady licenses
                // to be returned to the end users. 
                // It contains the data on the content key in the license 
                // and any rights or restrictions to be 
                // enforced by the PlayReady DRM runtime when using the content key.
                PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

                // Configure whether the license is persistent 
                // (saved in persistent storage on the client) 
                // or non-persistent (only held in memory while the player is using the license).  
                licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

                // AllowTestDevices controls whether test devices can use the license or not.  
                // If true, the MinimumSecurityLevel property of the license
                // is set to 150.  If false (the default), 
                // the MinimumSecurityLevel property of the license is set to 2000.
                licenseTemplate.AllowTestDevices = true;

                // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                // It grants the user the ability to playback the content subject to the zero or more restrictions 
                // configured in the license and on the PlayRight itself (for playback specific policy). 
                // Much of the policy on the PlayRight has to do with output restrictions 
                // which control the types of outputs that the content can be played over and 
                // any restrictions that must be put in place when using a given output.
                // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                //then the DRM runtime will only allow the video to be displayed over digital outputs 
                //(analog video outputs won’t be allowed to pass the content).

                // IMPORTANT: These types of restrictions can be very powerful 
                // but can also affect the consumer experience. 
                // If the output protections are configured too restrictive, 
                // the content might be unplayable on some clients. 
                // For more information, see the PlayReady Compliance Rules document.

                // For example:
                //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

                responseTemplate.LicenseTemplates.Add(licenseTemplate);

                return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
            }


            private static string ConfigureWidevineLicenseTemplate()
            {
                var template = new WidevineMessage
                {
                    allowed_track_types = AllowedTrackTypes.SD_HD,
                    content_key_specs = new[]
                    {
                        new ContentKeySpecs
                        {
                            required_output_protection = 
                                new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                            security_level = 1,
                            track_type = "SD"
                        }
                    },
                    policy_overrides = new
                    {
                        can_play = true,
                        can_persist = true,
                        can_renew = false
                    }
                };

                string configuration = JsonConvert.SerializeObject(template);
                return configuration;
            }


            static public IContentKey CreateCommonTypeContentKey()
            {
                // Create envelope encryption content key
                Guid keyId = Guid.NewGuid();
                byte[] contentKey = GetRandomBuffer(16);

                IContentKey key = _context.ContentKeys.Create(
                                        keyId,
                                        contentKey,
                                        "ContentKey",
                                        ContentKeyType.CommonEncryption);

                return key;
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


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Дополнительные материалы
[Использование общего динамического шифрования PlayReady и (или) Widevine DRM](media-services-protect-with-drm.md)

[Использование динамического шифрования AES-128 и службы доставки ключей](media-services-protect-with-aes128.md)

[Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure](media-services-licenses-partner-integration.md)




<!--HONumber=Nov16_HO3-->


