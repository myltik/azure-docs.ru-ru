---
title: "Передача маркеров проверки подлинности в службы мультимедиа Azure | Документация Майкрософт"
description: "Узнайте, как отправлять маркеры проверки подлинности из клиента в службу доставки ключей службы мультимедиа Azure."
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
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Как клиенты передают маркеры проверки подлинности в службу доставки ключей служб мультимедиа Azure
Нам постоянно присылают вопросы о том, как проигрыватель может передать в наши службы доставки ключей маркер, который будет использоваться для проверки, и как проигрыватель получает ключ. Мы поддерживаем маркеры двух форматов: Simple Web Token (SWT) и JSON Web Token (JWT). Проверку подлинности с использованием маркера можно применять к ключу любого типа, независимо от того, какое шифрование выполняется в системе: общее или шифрование конвертов AES.

Ниже приведены способы передачи маркера с помощью проигрывателя, выбор которых зависит от целевой платформы и проигрывателя.
- С использованием заголовка авторизации HTTP.
> [!NOTE]
> Обратите внимание, что для каждой спецификации OAuth 2.0. ожидается префикс Bearer. На [странице демоверсии](http://ampdemo.azureedge.net/) Проигрывателя мультимедиа Azure приведен пример проигрывателя с конфигурацией маркера. Выберите AES (маркер JWT) или AES (маркер SWT), чтобы указать источник видео. Маркер передается через заголовок авторизации.

- Путем добавления параметра запроса URL-адреса со строкой token=tokenvalue.  
> [!NOTE]
> Обратите внимание, что префикс Bearer не ожидается. Так как маркер отправляется через URL-адрес, необходимо защитить строку маркера. В этом примере кода C# показано, как это сделать:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- С использованием поля CustomData.
Только для приобретения лицензии PlayReady. С использованием поля CustomData в запросе на приобретение лицензии PlayReady. В этом случае маркер должен содержаться в XML-документе, описанном ниже.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
Укажите маркер проверки подлинности в элементе <Token>.

- С помощью дополнительного списка воспроизведения HLS. Если необходимо настроить маркер проверки подлинности для воспроизведения HLS с шифрованием AES в Safari или iOS, вы не сможете отправить маркер напрямую. Сведения о том, как изменить список воспроизведения для реализации этого сценария, см. в [этой записи блога](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]