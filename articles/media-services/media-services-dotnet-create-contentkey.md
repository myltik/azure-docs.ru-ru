<properties 
	pageTitle="Создание ContentKey с использованием .NET" 
	description="Узнайте, как создавать ключи содержимого, которые обеспечивают безопасный доступ к ресурсам." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>


#Создание ContentKey с использованием .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Службы мультимедиа позволяют создавать и доставлять зашифрованные ресурсы. **ContentKey** обеспечивает безопасный доступ к вашим **ресурсам-контейнерам**.

При создании нового ресурса-контейнера (например, перед [передачей файлов](media-services-dotnet-upload-files.md)) можно указать следующие параметры шифрования: **StorageEncrypted**, **CommonEncryptionProtected** или **EnvelopeEncryptionProtected**.

При доставке ресурсов-контейнеров клиентам можно [настроить динамическое шифрование таких ресурсов](media-services-dotnet-configure-asset-delivery-policy.md), используя один из двух следующих типов шифрования: **DynamicEnvelopeEncryption** или **DynamicCommonEncryption**.

Зашифрованные ресурсы-контейнеры должны быть связаны с сущностями **ContentKey**. В этой статье описано, как создать ключ содержимого.

>[AZURE.NOTE] Когда ресурс-контейнер **StorageEncrypted** создается с помощью пакета SDK служб мультимедиа для .NET, ключ содержимого (**ContentKey**) создается и связывается с ресурсом-контейнером автоматически.

##ContentKeyType

Одно из значений, которые необходимо задать при создания ключа содержимого — тип ключа содержимого. Выберите одно из следующих значений.

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Создание ContentKey конвертного типа

В следующем фрагменте кода создается ключ содержимого конвертного типа шифрования. Затем ключ связывается с указанным ресурсом.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

вызывает

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Создание ContentKey общего типа    

В следующем фрагменте кода создается ключ содержимого общего типа шифрования. Затем ключ связывается с указанным ресурсом.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

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
вызывает

	IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->