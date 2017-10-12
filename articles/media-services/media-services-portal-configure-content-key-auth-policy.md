---
title: "Настройка политики авторизации ключей содержимого с помощью портала Azure | Документация Майкрософт"
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
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-content-key-authorization-policy"></a>Настройка политики авторизации ключа содержимого
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Обзор
Службы мультимедиа Microsoft Azure позволяют защищать потоковое содержимое MPEG-DASH, Smooth Streaming и HTTP Live Streaming (HLS) с помощью стандарта AES (использующего 128-разрядные ключи шифрования) или технологии [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS позволяет также передавать потоки MPEG DASH с шифрованием Widevine DRM. PlayReady и Widewine шифруются согласно спецификации общего шифрования (ISO/IEC 23001-7 CENC).

Кроме того, службы мультимедиа включают в себя **службы доставки ключей и лицензий** , с помощью которых клиенты могут получать ключи AES либо лицензии PlayReady или Widevine для воспроизведения зашифрованного содержимого.

В этой статье показано, как настроить политики авторизации ключей содержимого с помощью портала Azure. Ключ может использоваться позже для динамического шифрования содержимого. Обратите внимание, что сейчас поддерживается шифрование для таких форматов потоковой передачи: HLS, MPEG DASH и Smooth Streaming. Невозможно шифровать последовательно скачиваемые данные.

Когда проигрыватель запрашивает поток, для которого настроено динамическое шифрование, службы мультимедиа используют настроенный ключ для динамического шифрования содержимого с помощью AES или DRM. Чтобы расшифровать поток, проигрыватель запросит ключ у службы доставки ключей. Чтобы определить, есть ли у пользователя право на получение ключа, служба оценивает политики авторизации, заданные для ключа.

Если вы планируете использовать несколько ключей содержимого или хотите задать URL-адрес **службы доставки ключей или лицензий** , отличный от адреса службы доставки ключей служб мультимедиа, используйте пакет SDK для .NET или интерфейсы REST API для служб мультимедиа.

[Настройка политики авторизации ключей содержимого с помощью пакета .NET SDK служб мультимедиа](media-services-dotnet-configure-content-key-auth-policy.md)

[Настройка политики авторизации ключей содержимого с помощью интерфейсов REST API служб мультимедиа](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Важные особенности
* При создании учетной записи AMS в нее добавляется конечная точка потоковой передачи **по умолчанию** в состоянии **Остановлена**. Чтобы начать потоковую передачу содержимого и воспользоваться динамической упаковкой и динамическим шифрованием, конечная точка потоковой передачи должна находиться в состоянии **Выполняется**. 
* Ресурс должен содержать набор MP4-файлов с адаптивной скоростью или файлов Smooth Streaming с адаптивной скоростью. Дополнительные сведения см. в статье о [кодировании ресурсов](media-services-encode-asset.md).
* Служба доставки ключей кэширует политику ContentKeyAuthorizationPolicy и связанные с ней объекты (параметры и ограничения политики) за 15 минут.  Если создать политику ContentKeyAuthorizationPolicy и задать для нее ограничение "по маркеру", а затем протестировать ее, то последующее обновление для использования ограничения "открытая" займет примерно 15 минут.

## <a name="how-to-configure-the-key-authorization-policy"></a>Практическое руководство. Настройка политики авторизации ключей
Чтобы настроить политику авторизации ключей, выберите страницу **ЗАЩИТА СОДЕРЖИМОГО** .

Службы мультимедиа поддерживают несколько способов аутентификации пользователей, которые запрашивают ключи. В политике авторизации ключей содержимого можно задать ограничения авторизации — **открытое**, **по маркеру** или **по IP-адресу** (**IP-адрес** можно настроить, используя пакет SDK для .NET или REST).

### <a name="open-restriction"></a>Ограничение открытого типа
Ограничение **открытого** типа означает, что система будет доставлять ключ всем, кто его запросит. Это ограничение подходит для тестирования.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Ограничение по маркеру
Чтобы выбрать политику ограничения по маркеру, нажмите кнопку **МАРКЕР** .

При ограничении **по маркеру** к политике должен прилагаться маркер, выданный **службой маркеров безопасности**. Службы мультимедиа поддерживают маркеры в формате **простого веб-маркера** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) и формате **JSON Web Token** (JWT). Дополнительные сведения см. в статье, посвященной [проверке подлинности по маркерам JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Службы мультимедиа не предоставляют **службы маркеров безопасности**. Для выдачи маркеров можно создать пользовательскую службу STS или использовать службу Microsoft Azure ACS. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности. Служба доставки ключей служб мультимедиа возвращает клиенту ключ шифрования, если маркер является допустимым и утверждения маркера соответствуют утверждениям, настроенным для ключа содержимого. Дополнительные сведения см. в статье об [использовании Azure ACS для выдачи маркеров](http://mingfeiy.com/acs-with-key-services).

Если вы настроили политику с ограничением **МАРКЕР**, необходимо указать значения **ключа проверки**, **издателя** и **аудитории**. Основной ключ проверки содержит ключ, которым подписан маркер, а издатель — это служба маркеров безопасности, которая выдает маркер. Аудитория (иногда называется областью) описывает назначение маркера или ресурс, доступ к которому обеспечивает маркер. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в маркере значениям в шаблоне.

### <a name="playready"></a>PlayReady
Когда защита содержимого выполняется с помощью **PlayReady**, в политике авторизации необходимо помимо прочего указать XML-строку, определяющую шаблон лицензии PlayReady. По умолчанию задана следующая политика:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"> <LicenseTemplates> <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates> </PlayReadyLicenseResponseTemplate>

Вы можете нажать кнопку **импорт XML-файла политики** и выбрать другой XML-файл, который соответствует XML-схеме, определенной [здесь](media-services-playready-license-template-overview.md).

## <a name="next-step"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

