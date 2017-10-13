---
title: "Использование динамического шифрования AES-128 и службы доставки ключей | Документация Майкрософт"
description: "Службы мультимедиа Microsoft Azure позволяют доставлять содержимое, зашифрованное с помощью 128-битных ключей шифрования AES. Они также включают в себя службу доставки ключей, которая доставляет ключи шифрования авторизованным пользователям. В этой статье показано, как динамически шифровать содержимое с помощью алгоритма AES-128 и службы доставки ключей."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: a441e76fae0bda829cb112d307b3b436809b9c9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Использование динамического шифрования AES-128 и службы доставки ключей
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Обзор
> [!NOTE]
> Обзор способов защиты содержимого мультимедиа с помощью шифрования AES представлен в [этом](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) видео.
> 
> 

Службы мультимедиа Microsoft Azure позволяют доставлять по каналам HLS и Smooth Streaming содержимое, зашифрованное с помощью AES (с использованием 128-битных ключей шифрования). Они также включают в себя службу доставки ключей, которая доставляет ключи шифрования авторизованным пользователям. Если вам нужно, чтобы службы мультимедиа зашифровали ресурс-контейнер, свяжите с ним ключ шифрования и настройте политики авторизации для ключа. Когда поток запрашивается проигрывателем, службы мультимедиа используют указанный ключ для динамического шифрования содержимого с помощью AES. Чтобы расшифровать поток, проигрыватель запросит ключ у службы доставки ключей. Чтобы определить, есть ли у пользователя право на получение ключа, служба оценивает политики авторизации, заданные для ключа.

Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. Для политики авторизации ключа содержимого можно задать одно или несколько из ограничений авторизации: открытая авторизация или с ограничением по маркеру. При ограничении по маркеру к политике должен прилагаться маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают [простые веб-маркеры](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) и маркеры в формате [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Дополнительную информацию см. в разделе [Настройка политики авторизации для ключа содержимого](media-services-protect-with-aes128.md#configure_key_auth_policy).

Чтобы воспользоваться преимуществами динамического шифрования, необходимо иметь ресурс-контейнер, содержащий набор многоскоростных MP4-файлов или многоскоростных исходных файлов Smooth Streaming. Вам также потребуется настроить политику доставки для ресурса-контейнера (описывается далее в этой статье). В зависимости от формата, указанного в URL-адресе потоковой передачи, сервер потокового воспроизведения по запросу обеспечивает доставку содержимого по выбранному протоколу. В результате вы сможете хранить и оплачивать файлы только в одном формате, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента.

Эта статья будет полезна разработчикам приложений, которые доставляют защищенные файлы мультимедиа. В ней показано, как настроить политики авторизации для службы доставки ключей, чтобы только авторизованные клиенты могли получать ключи шифрования, а также рассматривается использование динамического шифрования.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Рабочий процесс динамического шифрования AES-128 и службы доставки ключей

Ниже описаны общие действия, которые необходимо выполнить для шифрования ресурсов-контейнеров с помощью AES, используя службу доставки ключей служб мультимедиа и динамическое шифрование.

1. [Создание ресурса-контейнера и отправка в него файлов](media-services-protect-with-aes128.md#create_asset).
2. [Кодирование ресурса-контейнера с файлами в набор MP4-файлов с переменной скоростью](media-services-protect-with-aes128.md#encode_asset).
3. [Создание ключа содержимого и связывание его с закодированным ресурсом-контейнером](media-services-protect-with-aes128.md#create_contentkey). В службах мультимедиа ключ содержимого содержит ключ шифрования ресурса-контейнера.
4. [Настройка политики авторизации для ключа содержимого](media-services-protect-with-aes128.md#configure_key_auth_policy). Чтобы получить ключ содержимого, клиент должен соответствовать заданной для этого ключа политике авторизации.
5. [Настройте политику доставки для ресурса-контейнера](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Конфигурация политики доставки включает: URL-адрес для получения ключа и вектор инициализации (IV) (при использовании AES 128 необходимо указывать один и тот же вектор инициализации для шифрования и расшифровки), протокол доставки (например, MPEG-DASH, HLS, Smooth Streaming или все перечисленные) и тип динамического шифрования (например, конвертное или без динамического шифрования).

    К разным протоколам можно применять разные политики в отношении одного и того же ресурса-контейнера. Например, вы можете применить шифрование PlayReady при использовании Smooth или DASH и конвертное шифрование AES при использовании HLS. Потоковая передача по тем протоколам, которые не определены в политике доставки (например, если вы добавили одну политику, которая предусматривает использование только протокола HLS), будет блокироваться. Исключением являются те случаи, когда политика доставки ресурсов совсем не определена. Тогда все протоколы могут использоваться в незашифрованном виде.

6. [Создайте указатель OnDemand](media-services-protect-with-aes128.md#create_locator) , чтобы получить URL-адрес для потоковой передачи.

В этой статье также показано, [как клиентское приложение может запрашивать ключ у службы доставки ключей](media-services-protect-with-aes128.md#client_request).

В конце раздела вы найдете полный [пример](media-services-protect-with-aes128.md#example) для .NET.

На следующем изображении показан описанный выше рабочий процесс. В данном случае для проверки подлинности используется маркер.

![Защита с помощью AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Далее приводятся подробные объяснения, примеры кода и ссылки на статьи с инструкциями для выполнения описанных выше задач.

## <a name="current-limitations"></a>Текущие ограничения
При добавлении или обновлении политики доставки ресурсов необходимо удалить существующий указатель (если он есть) и создать новый.

## <a id="create_asset"></a>Создание ресурса-контейнера и отправка в него файлов
Для кодирования и потоковой передачи видео, а также управления ими необходимо сначала отправить содержимое в службы мультимедиа Microsoft Azure. Оно будет сохранено в безопасном облачном хранилище для последующей обработки и потоковой передачи. 

Дополнительные сведения см. в статье [Передача файлов в учетную запись служб мультимедиа с помощью .NET](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Закодируйте ресурс-контейнер с файлами в набор MP4-файлов с переменной скоростью.
При использовании динамического шифрования вам достаточно создать ресурс-контейнер, содержащий набор многоскоростных MP4-файлов или многоскоростных исходных файлов Smooth Streaming. Затем с учетом формата, указанного в манифесте или запросе фрагмента, сервер потоковой передачи по запросу организует передачу содержимого по выбранному протоколу. В результате вы сможете хранить и оплачивать файлы только в одном формате, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента. Дополнительные сведения см. в статье [Динамическая упаковка](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>При создании учетной записи AMS в нее добавляется конечная точка потоковой передачи **по умолчанию** в состоянии **Остановлена**. Чтобы начать потоковую передачу содержимого и воспользоваться динамической упаковкой и динамическим шифрованием, конечная точка потоковой передачи, из которой необходимо выполнять потоковую передачу содержимого, должна находиться в состоянии **Выполняется**. 
>
>Кроме того, чтобы использовать динамическую упаковку и динамическое шифрование, ресурс должен содержать набор файлов формата MP4 или потоковой передачи Smooth Streaming с переменной скоростью.

Инструкции по выполнению шифрования см. в статье [Порядок кодирования ресурса с использованием стандартного кодировщика мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Создание ключа содержимого и связывание его с закодированным ресурсом-контейнером
В службах мультимедиа ключ содержимого содержит ключ, который используется для шифрования ресурса-контейнера.

Дополнительные сведения см. в статье [Создание ContentKey с использованием .NET](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Настройка политики авторизации ключа содержимого
Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. Чтобы получить ключ содержимого, клиент (проигрыватель) должен соответствовать заданной для этого ключа политике авторизации. Для политики авторизации ключа содержимого можно задать одно или несколько из ограничений авторизации: открытая авторизация, с ограничением по маркеру или с ограничением по IP-адресу.

Дополнительные сведения см. в разделе [Настройка политики авторизации ключа содержимого](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Настройка политики доставки для ресурса-контейнера
Настройте политику доставки для ресурса-контейнера. Вот некоторые элементы, входящие в конфигурацию политики доставки:

* URL-адрес для получения ключа. 
* Вектор инициализации, используемый для конвертного шифрования. При использовании AES 128 для шифрования и расшифровки необходимо указывать один и тот же вектор инициализации. 
* Протокол доставки ресурсов-контейнеров (например, MPEG-DASH, HLS, Smooth Streaming или все перечисленные).
* Тип динамического шифрования (например, конвертное шифрование с помощью AES), если оно используется. 

Дополнительные сведения см. в разделе [Настройка политик доставки ресурсов](media-services-rest-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Создание указателя потоковой передачи по запросу для получения URL-адреса для потоковой передачи
При использовании протоколов Smooth, DASH или HLS вам потребуется предоставить пользователю URL-адрес для потоковой передачи.

> [!NOTE]
> При добавлении или обновлении политики доставки ресурсов необходимо удалить существующий указатель (если он есть) и создать новый.
> 
> 

Указания по публикации ресурса и созданию URL-адреса потоковой передачи см. в статье [Создание URL-адреса потоковой передачи](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Получение маркера тестирования
Получите маркер тестирования в зависимости от ограничения по маркеру, заданного в политике авторизации ключа.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Для проверки потока можно использовать [проигрыватель AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) .

## <a id="client_request"></a>Отправка запроса клиента на получение ключа в службе доставки ключей
На предыдущем этапе вы создали URL-адрес, указывающий на файл манифеста. Клиент должен извлечь необходимые сведения из файлов манифеста потоковой передачи и отправить запрос в службу доставки ключей.

### <a name="manifest-files"></a>Файлы манифестов
Клиенту необходимо извлечь из файла манифеста значение URL-адреса (который также содержит идентификатор ключа содержимого (KID)). Затем клиент попытается получить ключ шифрования в службе доставки ключей. Клиенту также потребуется извлечь значение вектора инициализации и использовать его для расшифровки потока. В следующем фрагменте кода показан элемент <Protection> манифеста Smooth Streaming.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

При использовании протокола HLS корневой манифест разбивается на файлы сегментов. 

Например, корневой манифест http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) содержит список имен файлов сегментов.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Если открыть один из файлов сегментов в текстовом редакторе (например, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), можно увидеть строку #EXT-X-KEY, которая означает, что файл зашифрован.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Если вы планируете воспроизводить HLS с шифрованием AES в Safari, см. [этот блог](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Запрос ключа в службе доставки ключей

В следующем коде показано, как отправить запрос в службу доставки ключей служб мультимедиа, используя код URI доставки ключа (извлеченный из манифеста) и маркер (получение простых веб-маркеров из службы маркеров безопасности в этой статье не рассматривается).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>Защита содержимого с помощью AES-128 и .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Создание и настройка проекта Visual Studio

1. Настройте среду разработки и укажите в файле app.config сведения о подключении, как описано в статье [Разработка служб мультимедиа с помощью .NET](media-services-dotnet-how-to-use.md). 
2. Добавьте следующие элементы в **appSettings**, определенные в файле app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Пример

Замените код в файле Program.cs кодом, приведенным в этом разделе.
 
>[!NOTE]
>Действует ограничение в 1 000 000 записей для разных политик AMS (например, для политики Locator или ContentKeyAuthorizationPolicy). Следует указывать один и тот же идентификатор политики, если вы используете те же дни, разрешения доступа и т. д. Например, политики для указателей, которые должны оставаться на месте в течение длительного времени (не политики передачи). Чтобы узнать больше, ознакомьтесь с [этим](media-services-dotnet-manage-entities.md#limit-access-policies) разделом.

Обязательно обновите переменные, чтобы они указывали на папки, в которых находятся входные файлы.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

