---
title: "Передавать токены проверки подлинности для служб мультимедиа Azure | Документы Microsoft"
description: "Узнайте, как отправлять маркеры проверки подлинности от клиента в службу доставки ключей служб мультимедиа Azure"
services: media-services
keywords: content protection, DRM, token authentication
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 7d143242231444b8557a303d1b504d5311693f1a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Узнайте, как клиентам передавать токены в службу доставки ключей служб мультимедиа Azure
Пользователи часто попросите как проигрыватель может передать токены службы доставки ключей служб мультимедиа Azure для проверки, ключ можно получить проигрыватель. Форматы JSON Web Token (JWT) и службы мультимедиа поддерживают только простые веб-токен (SWT). Маркер проверки подлинности применяется к любому типу ключа независимо от того, используется ли общее шифрование или шифрование конвертов Advanced Encryption Standard (AES) в системе.

 В зависимости от проигрывателя и целевой платформы можно передать токен с проигрывателем одним из следующих способов:

- С использованием заголовка авторизации HTTP.
    > [!NOTE]
    > Префикс «Bearer» ожидается в спецификации OAuth 2.0. Образец проигрывателя с маркера конфигурации размещается в Azure проигрыватель [демонстрационной страницы](http://ampdemo.azureedge.net/). Чтобы настроить источник видео, выберите **AES (токена JWT)** или **AES (маркера SWT)**. Маркер передается через заголовок авторизации.

- Через Добавление URL-адреса параметр с помощью запроса «token = tokenvalue.»  
    > [!NOTE]
    > Префикс «Bearer» не ожидается. Поскольку маркер отправляется через URL-адрес, вам потребуется для защиты строку токена. Ниже приведен пример кода C#, показано, как это сделать:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Через поле CustomData.
Этот параметр используется для получения лицензии PlayReady, только через поле CustomData запрос приобретения лицензии PlayReady. В этом случае маркер должна быть внутри XML-документа, как описано здесь:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Поместите срок действия токена проверки подлинности в элементе маркер.

- Через альтернативного списка воспроизведения HTTP Live Streaming (HLS). Если необходимо настроить проверки подлинности маркера для AES + HLS воспроизведения на iOS и Safari не способом, можно отправить непосредственно в маркере. Дополнительные сведения о том, как дополнительный список воспроизведения, чтобы реализовать этот сценарий, см. в этой [блога](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]