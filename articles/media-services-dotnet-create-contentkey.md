<properties 
	pageTitle="Создание ContentKey с использованием .NET" 
	description="Узнайте, как создавать ключи содержимого, которые обеспечивают безопасный доступ к ресурсам." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Создание ContentKey с использованием .NET

Это одна из статей серии [Рабочий процесс для видео по запросу в службах мультимедиа](../media-services-video-on-demand-workflow) и серии [Рабочий процесс для потоковой трансляции в службах мультимедиа](../media-services-live-streaming-workflow).  

Службы мультимедиа позволяют создавать новые ресурсы и доставлять зашифрованные ресурсы. **ContentKey** обеспечивает безопасный доступ к вашим **ресурсам**. 

При создании нового ресурса (например, перед [передачей файлов](../media-services-dotnet-upload-files/)) можно указать следующие параметры шифрования: **StorageEncrypted**, **CommonEncryptionProtected** или **EnvelopeEncryptionProtected**. 

При доставке ресурсов клиентам можно [настроить выполнение динамического шифрования ресурсов](../media-services-dotnet-configure-asset-delivery-policy). Вы можете использовать один из двух следующих типов шифрования: **DynamicEnvelopeEncryption** или **DynamicCommonEncryption**.

Зашифрованные ресурсы должны быть связаны с сущностями **ContentKey**. В этой статье описано, как создать ключ содержимого.

>[AZURE.NOTE] При создании нового ресурса **StorageEncrypted** с помощью пакета SDK служб мультимедиа для .NET **ключ содержимого** создается и связывается с ресурсом автоматически.

##ContentKeyType

Одно из значений, которые необходимо задать при создания ключа содержимого - тип ключа содержимого. Выберите одно из следующих значений. 

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
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
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for url encryption.  Only used internally.
        /// </summary>
        UrlEncryption = 3,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Создание конвертного типа ключа содержимого

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

call

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Создание общего типа ключа содержимого    

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
call

	IContentKey key = CreateCommonTypeContentKey(encryptedsset);
<!--HONumber=45--> 
