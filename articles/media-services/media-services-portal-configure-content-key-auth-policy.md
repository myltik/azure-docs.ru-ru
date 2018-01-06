---
title: "Настройка политики авторизации ключа контента с помощью портала Azure | Документы Microsoft"
description: "Узнайте, как настроить политику авторизации для ключа содержимого."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: a0ab954bda3340b9010b16f54e343933808cc463
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>Настройка политики авторизации ключа контента
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Обзор
 Службы мультимедиа Azure позволяют доставлять потоки MPEG-DASH, Smooth Streaming и HTTP Live Streaming (HLS), защищенные с помощью Advanced Encryption Standard (AES) с помощью 128-битные ключи шифрования или [PlayReady цифровыми правами (DRM)](https://www.microsoft.com/playready/overview/). С помощью служб мультимедиа можно доставлять потоки ТИРЕ зашифрован Widevine DRM. PlayReady и Widevine шифруются в соответствии со спецификацией общих шифрование (CENC ISO/IEC 23001-7).

Службы мультимедиа также предоставляют служба доставки ключа/лицензий, по которому клиенты могут получить ключи AES или лицензии PlayReady или Widevine воспроизведение зашифрованное содержимое.

В этой статье показано, как настроить политики авторизации ключей содержимого с помощью портала Azure. Ключ может использоваться позже для динамического шифрования содержимого. В настоящее время вы можете зашифровать форматы Smooth Streaming, MPEG-DASH и HLS. Не удается зашифровать прогрессивной загрузки.

Когда проигрыватель запрашивает поток, который имеет значение для динамического шифрования, службы мультимедиа используют настроенным ключом для динамического шифрования контента с помощью шифрования AES или DRM. Чтобы расшифровать поток, проигрыватель запросит ключ у службы доставки ключей. Чтобы определить, авторизован ли пользователь получить ключ, служба оценивает политики авторизации, заданный для ключа.

Если вы планируете нескольких ключей контента или требуется указывать ключ/лицензии доставки URL-адрес службы отличные от службы доставки ключей Media Services, используйте Media Services .NET SDK или API REST. Дополнительные сведения можно найти в разделе 

* [Настройка политики авторизации ключа контента с помощью пакета SDK .NET служб мультимедиа](media-services-dotnet-configure-content-key-auth-policy.md)
* [Настройка политики авторизации ключа контента с помощью API REST служб мультимедиа](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Некоторые соображения
* При создании учетной записи служб мультимедиа конечной точки потоковой передачи по умолчанию добавляется к учетной записи в состоянии «Остановлена». Чтобы запустить потоковой передачи контента и воспользоваться преимуществами динамической упаковки и динамического шифрования, вашей конечной точки потоковой передачи необходимо в состоянии «Работает». 
* Ресурс должен содержать набор MP4-файлов с адаптивной скоростью или файлов Smooth Streaming с адаптивной скоростью. Дополнительные сведения см. в статье о [кодировании ресурсов](media-services-encode-asset.md).
* Служба доставки ключей кэширует ContentKeyAuthorizationPolicy и связанные объекты (параметры и ограничения политики) на 15 минут. Можно создать политику ContentKeyAuthorizationPolicy и указать использование ограничения token, протестируйте его, а потом обновить политику, задав ограничение open. Этот процесс занимает примерно 15 минут до переключения политики на версию open.
* Конечной точки потоковой передачи Media Services задает значение заголовка CORS Access-Control-Allow-Origin в предварительный ответ как символ-шаблон «\*». Это значение можно использовать большинство проигрывателей, включая Azure Media Player, Roku и JWPlayer и другие. Однако некоторые проигрыватели, которые использовать dash.js не работают так, как с учетными данными режимом «включить», XMLHttpRequest в их dash.js не допускает символ-шаблон «\*» в качестве значения Access-Control-Allow-Origin. Эти ограничения в dash.js избежать этого при размещении вашего клиента из одного домена, службы мультимедиа можно указать этого домена в заголовке предварительный ответ. Обратитесь за помощью в службу поддержки через портал Azure.

## <a name="configure-the-key-authorization-policy"></a>Настройка политики авторизации ключа
Чтобы настроить политику авторизации ключей, выберите страницу **ЗАЩИТА СОДЕРЖИМОГО** .

Службы мультимедиа поддерживают несколько способов проверки подлинности пользователей, запрашивающих ключ. Политика авторизации ключа контента может быть открытым, маркер или авторизации ограничения IP-адресов. (IP можно настроить с помощью REST или .NET SDK.)

### <a name="open-restriction"></a>Ограничение открытого типа
Ограничение open означает, что система предоставляет ключ для тех, кто выполняет запрос ключа. Это ограничение подходит для тестирования.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Ограничение по маркеру
Для выбора политики с ограничением токенов, установите **МАРКЕРА** кнопки.

Политика с ограничением токенов должна сопровождаться маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают только токены в простой веб-токен ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) и форматах JSON Web Token (JWT). Дополнительные сведения см. в разделе [проверки подлинности JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Службы мультимедиа не предоставляют службы маркеров безопасности. Можно создать настраиваемую STS или использовать Azure Access Control Service для выдачи токенов. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности. Если маркер является допустимым и утверждения в токене соответствуют настроенным для ключа контента, служба доставки ключей Media Services Возвращает ключ шифрования клиенту. Дополнительные сведения см. в разделе [использование Azure Access Control Service для выдачи токенов](http://mingfeiy.com/acs-with-key-services).

При настройке политики ограниченного токена, необходимо указать основной ключ проверки, издателя и аудитории параметров. Основной ключ проверки содержит ключ, который был подписан токен. Поставщик является STS, которая выдает маркер. Аудитория (иногда называется областью) описывает назначение маркера или ресурс, доступ к которому обеспечивает маркер. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в маркере значениям в шаблоне.

### <a name="playready"></a>PlayReady
При защите контента с помощью PlayReady, одно из действий, которые необходимо указать в политике авторизации является XML-строку, определяющий шаблон лицензии PlayReady. По умолчанию задана следующая политика:

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

Можно выбрать **импорта политики xml** кнопку и укажите другой XML, который соответствует схеме XML, определенные в [Обзор шаблона лицензий PlayReady служб мультимедиа](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

