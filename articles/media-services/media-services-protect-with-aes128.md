---
title: "Использование динамического шифрования AES-128 и службы доставки ключей | Документы Microsoft"
description: "Доставка содержимого зашифрованы с ключами шифрования AES 128-разрядное с помощью служб мультимедиа Microsoft Azure. Службы мультимедиа также предоставляют службу доставки ключей, которая доставляет ключи шифрования авторизованным пользователям. В этой статье показано, как динамически шифровать содержимое с помощью алгоритма AES-128 и службы доставки ключей."
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
ms.openlocfilehash: 013c14c00096c9958a732d1f0eaacc9248f57da9
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Использование динамического шифрования AES-128 и службы доставки ключей
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Чтобы получить последнюю версию пакета SDK для Java и приступить к разработке с помощью Java, в разделе [начало работы с Java клиентский пакет SDK для Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Чтобы загрузить последние PHP SDK для служб мультимедиа, найдите версию 0.5.7 пакета Microsoft/WindowsAzure в [Packagist репозитория](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Обзор
> [!NOTE]
> Сведения о том, как для шифрования контента с Advanced Encryption Standard (AES) для доставки в Safari на macOS см. в разделе [этой записи блога](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
> Общие сведения о способах защиты содержимого с шифрованием AES мультимедиа. в разделе [в этом видеоролике](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).
> 
> 

 Службы мультимедиа можно использовать для предоставления HTTP Live Streaming (HLS) и Smooth Streaming, зашифрованных с помощью AES с помощью 128-битные ключи шифрования. Службы мультимедиа также предоставляют службу доставки ключей, которая доставляет ключи шифрования авторизованным пользователям. Если требуется, чтобы службы мультимедиа для шифрования актива, связывается с активом ключ шифрования и также настроить политики авторизации для ключа. Когда проигрыватель запрашивает поток, службы мультимедиа используют указанный ключ для динамического шифрования контента с помощью шифрования AES. Чтобы расшифровать поток, проигрыватель запросит ключ у службы доставки ключей. Чтобы определить, авторизован ли пользователь получить ключ, служба оценивает политики авторизации, заданный для ключа.

Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. Политика авторизации ключа контента может иметь одно или несколько ограничений авторизации, открытых или токена ограничения. Политики ограниченного токена должен соответствовать маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают только токены в [простой веб-токен](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) и [веб-маркера JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) форматы (JWT). Дополнительные сведения см. в разделе [Настройка политики авторизации ключа контента](media-services-protect-with-aes128.md#configure_key_auth_policy).

Чтобы воспользоваться преимуществами динамического шифрования, необходимо иметь ресурс-контейнер, содержащий набор многоскоростных MP4-файлов или многоскоростных исходных файлов Smooth Streaming. Вам также потребуется настроить политику доставки для ресурса-контейнера (описывается далее в этой статье). Затем в зависимости от формата, указанного в URL-адрес потоковой передачи сервера потоковой передачи по требованию обеспечивает доставку потока выбранного протокола. В результате необходимо хранить и оплачивать файлы в едином формате хранения. Службы мультимедиа создает и обслуживает соответствующий ответ на основе запросов клиента.

Эта статья полезна для разработчиков, работающих над приложениями, осуществляющими доставку защищенных файлов мультимедиа. Статьи показано, как настроить службу доставки ключей с политиками авторизации, чтобы ключи шифрования можно получать только авторизованные клиенты. а также рассматривается использование динамического шифрования.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Динамическое шифрование AES-128 и рабочий процесс службы доставки ключей

При шифровании активов с помощью AES с помощью службы доставки ключей Media Services, а также с помощью динамического шифрования, выполните следующие шаги.

1. [Создание актива и отправка файлов в актив](media-services-protect-with-aes128.md#create_asset).

2. [Кодирование активов, содержащей файл в набор MP4 с адаптивной скоростью](media-services-protect-with-aes128.md#encode_asset).

3. [Создание ключа контента и связать его с зашифрованным активом](media-services-protect-with-aes128.md#create_contentkey). В службах мультимедиа ключ контента содержит ключ шифрования актива.

4. [Настройка политики авторизации ключа контента](media-services-protect-with-aes128.md#configure_key_auth_policy). Необходимо настроить политику авторизации ключа контента. Клиент должен удовлетворять политики ключ содержимого доставляется клиенту.

5. [Настройте политику доставки для ресурса-контейнера](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Конфигурация политики доставки включает URL-адрес получения ключа и вектора инициализации (IV). (AES-128 требуется тот же IV для шифрования и расшифровки). Кроме того, конфигурация включает протокол доставки (например, MPEG-DASH, HLS, Smooth Streaming или все) и тип динамического шифрования (например конверт или без динамического шифрования).

    Можно применять различные политики для каждого протокола в том же активе. Например можно применить шифрование PlayReady в Smooth/DASH и AES envelope в HLS. Все протоколы, которые не определены в политике доставки будут заблокированы для потоковой передачи. (Например, если вы добавляете отдельную политику, определяющее только HLS как протокол). Исключение, если у вас есть нет определенных политик доставки активов. Тогда все протоколы разрешено использовать в незашифрованном виде.

6. [Создание указателя OnDemand](media-services-protect-with-aes128.md#create_locator) для получения URL-адрес потоковой передачи.

В статье также показано, [как клиентское приложение может запрашивать ключ у службы доставки ключей](media-services-protect-with-aes128.md#client_request).

Можно найти полный [пример .NET](media-services-protect-with-aes128.md#example) в конце статьи.

На следующем рисунке показан рабочий процесс, описанный ранее. Здесь токен используется для проверки подлинности.

![Защита с помощью AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

В оставшейся части этой статьи содержит объяснения, примеры кода и ссылки на разделы, в которых показано, как добиться задач, описанных ранее.

## <a name="current-limitations"></a>Текущие ограничения
При добавлении или обновлении политики доставки актива необходимо удалить все существующие указатель и создать новый указатель.

## <a id="create_asset"></a>Создание ресурса-контейнера и отправка в него файлов
Для управления, кодирования и потоковой передачи видео, необходимо сначала передать содержимое в службы мультимедиа. После отправки, контент безопасно хранится в облаке для дальнейшей обработки и потоковой передачи. 

Дополнительные сведения см. в разделе [отправьте файлы в учетную запись служб носителей](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Кодирование активов, содержащей файл в набор MP4 с адаптивной скоростью
При использовании динамического шифрования создать актив, содержащий набор MP4-файлов с разными скоростями или файлы источника Smooth Streaming с разными скоростями. Затем в зависимости от формата, указанного в манифесте или запросе фрагмента сервера потоковой передачи по требованию гарантирует получать поток выбранного протокола. После этого достаточно для хранения и платить за файлы в едином формате хранения. Службы мультимедиа создает и обслуживает соответствующий ответ на основе запросов клиента. Дополнительные сведения см. в разделе [Общие сведения о динамической упаковке](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>При создании учетной записи служб мультимедиа конечной точки потоковой передачи по умолчанию добавляется к учетной записи в состоянии «Остановлена». Чтобы запустить потоковой передачи контента и воспользоваться преимуществами динамической упаковки и динамического шифрования, конечной точки потоковой передачи, из которой требуется поток содержимого необходимо в состоянии «Работает». 
>
>Кроме того для использования динамической упаковки и динамического шифрования, ваш ресурс должен содержать набор MP4 с адаптивным битрейтом или файлов Smooth Streaming с адаптивной скоростью.

Инструкции о том, как кодировать в разделе [закодировать ресурс с помощью Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Создание ключа содержимого и связывание его с закодированным ресурсом-контейнером
В службах мультимедиа ключ содержимого содержит ключ, который используется для шифрования ресурса-контейнера.

Дополнительные сведения см. в разделе [Создание ключа контента](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Настройка политики авторизации ключа контента
Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. Необходимо настроить политику авторизации ключа контента. Клиент (проигрыватель) должен удовлетворять политики можно передать ключ клиента. Политика авторизации ключа контента может иметь одно или несколько ограничений авторизации, откройте, token или ограничения IP-адресов.

Дополнительные сведения см. в разделе [Настройка политики авторизации ключа контента](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Настройте политику доставки актива
Настройте политику доставки для ресурса-контейнера. — Конфигурация политики доставки актива включает следующее:

* URL-адрес получения ключа. 
* Вектор инициализации (IV), используемый для шифрования конверта. AES-128 требуется тот же IV для шифрования и расшифровки. 
* Протокол доставки актива (например, MPEG-DASH, HLS, Smooth Streaming или все).
* Тип динамического шифрования (например, конвертное шифрование с помощью AES), если оно используется. 

Дополнительные сведения см. в разделе [конфигурация политики доставки активов](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Создание OnDemand потоковой передачи указателя, чтобы получить URL-адрес потоковой передачи
Необходимо предоставить пользователю URL-адрес потоковой передачи для Smooth Streaming, DASH или HLS.

> [!NOTE]
> При добавлении или обновлении политики доставки актива необходимо удалить все существующие указатель и создать новый указатель.
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
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Можно использовать [проигрыватель служб мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) для тестирования потока.

## <a id="client_request"></a>Отправка запроса клиента на получение ключа в службе доставки ключей
На предыдущем этапе вы создали URL-адрес, указывающий на файл манифеста. Клиент должен извлекать необходимые сведения из файлов манифеста потоковой передачи, чтобы сделать запрос в службу доставки ключей.

### <a name="manifest-files"></a>Файлы манифестов
Клиент должен извлечь URL-адрес (содержащий содержимое ключа ID [kid]) значение из файла манифеста. Клиент пытается получить ключ шифрования от службы доставки ключей. Клиент также должен извлечь значение IV и использовать его для расшифровки потока. В следующем фрагменте кода показано <Protection> манифеста Smooth Streaming:

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

Например, является корневой манифест: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Он содержит список имен файлов сегмента.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Если открыть один из файлов сегмента в текстовом редакторе (например, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), it contains # EXT-X-ключ, который указывает, что файл был зашифрован.

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
>Если вы планируете воспроизведение HLS с шифрованием AES в Safari, см. раздел [этот блог](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Запрос ключа в службе доставки ключей

Ниже показано, как отправить запрос в службу доставки ключей служб мультимедиа с помощью Uri доставки ключей (полученного из манифеста) и токена. (В этой статье не объясняется, как получить SWT от службы маркеров безопасности.)

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Защитить содержимое с помощью AES-128 с помощью .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Создание и настройка проекта Visual Studio

1. Настройка среды разработки и заполнить файл app.config с данными подключения, как описано в [разработки служб мультимедиа с помощью .NET](media-services-dotnet-how-to-use.md).

2. Добавьте следующие элементы appSettings, как определено в файле app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Пример

Замените код в файле Program.cs кодом, приведенным в этом разделе.
 
>[!NOTE]
>Действует ограничение в 1 000 000 записей для разных политик служб мультимедиа (например, для политики Locator или ContentKeyAuthorizationPolicy). Используйте тот же идентификатор политики, если всегда использовать те же разрешения дней или доступа. Например, политики для указателей, которые призваны остаются на месте в течение длительного времени (без передачи политики). Дополнительные сведения см. в разделе «Ограничения политик доступа» в [управления активами и связанных сущностей с помощью Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Обязательно обновите переменные, чтобы они указывали на папки, в которых находятся входные файлы.

    [!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

