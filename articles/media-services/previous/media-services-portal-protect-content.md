---
title: Настройка политик защиты содержимого с помощью портала Azure | Документация Майкрософт
description: В этой статье показано, как с помощью портала Azure настроить политики защиты содержимого. В статье также показано, как включить динамическое шифрование для ресурсов-контейнеров.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 8603716d30e1061ca9d600f2c053e90ff50c2433
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785883"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Настройка политик защиты содержимого с помощью портала Azure
 Службы мультимедиа Azure позволяют защитить данные мультимедиа, покидающие ваш компьютер, на этапах их хранения, обработки и доставки. С помощью служб мультимедиа Azure можно доставлять содержимое, динамически шифруемое с помощью алгоритма AES с 128-разрядным ключом шифрования. При использовании управления цифровыми правами (DRM) PlayReady и (или) Widevine и Apple FairPlay можно использовать шифрование CENC. 

Службы мультимедиа также обеспечивают доставку лицензий DRM и незащищенных ключей AES авторизованным клиентам. На портале Azure можно создать единую политику авторизации ключей и лицензий для всех типов шифрования.

В этой статье показано, как настроить политику защиты содержимого с помощью этого портала. В статье также показано, как применить динамическое шифрование к ресурсам-контейнерам.

## <a name="start-to-configure-content-protection"></a>Начало настройки системы защиты содержимого
Чтобы использовать портал для настройки глобальной защиты содержимого с помощью учетной записи Служб мультимедиа, выполните следующие действия.

1. На [портале](https://portal.azure.com/) выберите свою учетную запись Служб мультимедиа.

2. Выберите **Параметры** > **Защита контента**.

    ![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>политику авторизации ключей и лицензий
Службы мультимедиа поддерживают несколько способов аутентификации пользователей, запрашивающих ключи или лицензии. Потребуется настроить политику авторизации для ключа содержимого. Чтобы клиенту можно было доставлять ключ или лицензию, он должен соответствовать политике. Для политики авторизации ключа содержимого можно задать одно или несколько ограничений: открытая авторизация или авторизация с помощью маркера.

На портале можно создать единую политику авторизации ключей и лицензий для всех типов шифрования.

### <a name="open-authorization"></a>Открытая авторизация
Ограничение открытого типа означает, что система доставляет ключ всем, кто подает на него запрос. Это ограничение подходит для тестирования. 

### <a name="token-authorization"></a>Авторизация с помощью маркера
При ограничении с помощью маркера к политике должен прилагаться маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают маркеры в форматах простого веб-маркера (SWT) и JSON Web Token (JWT). Службы мультимедиа не предоставляют службу токенов безопасности. Вы можете создать настраиваемую службу STS или выдавать токены с помощью службы контроля доступа Azure. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности. Если маркер является допустимым, а его утверждения соответствуют утверждениям, настроенным для ключа (или лицензии), служба доставки ключей Служб мультимедиа возвращает клиенту запрошенный ключ (или лицензию).

При настройке политики ограничения по маркеру необходимо задать такие параметры, как основной ключ проверки, издатель и аудитория. Основной ключ проверки содержит ключ, которым был подписан маркер. Издателем является служба токенов безопасности, которая выдает токен. Аудитория (иногда называется областью) описывает назначение маркера или ресурс, доступ к которому обеспечивает маркер. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в маркере значениям в шаблоне.

![политику авторизации ключей и лицензий](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Шаблон лицензии PlayReady
Шаблон лицензии PlayReady позволяет включить функции в лицензии PlayReady. Дополнительные сведения о шаблоне лицензии PlayReady см. в статье [Обзор шаблонов лицензий PlayReady служб мультимедиа](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Временный
Если при настройке лицензии выбрать параметр "Временный", то она будет храниться в памяти лишь до тех пор, пока проигрыватель использует эту лицензию.  

![Временная защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Постоянный
Если при настройке лицензии выбрать параметр "Постоянный", она будет сохранена в постоянном хранилище клиента.

![Постоянная защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Шаблон лицензии Widevine
Шаблон лицензии Widevine позволяет включить функции, предусмотренные в лицензиях Widevine.

### <a name="basic"></a>базовая;
Если выбрать тип лицензии **Базовый**, то шаблон будет создан со всеми значениями по умолчанию.

### <a name="advanced"></a>Расширенная
Подробные сведения о шаблоне прав Widevine см. в статье [Общие сведения о шаблоне лицензии Widevine](media-services-widevine-license-template-overview.md).

![Расширенная защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Настройка FairPlay
Чтобы включить шифрование FairPlay, выберите **Конфигурация FairPlay**. Выберите **Сертификат приложения** и введите **Секретный ключ приложения**. Дополнительные требования и сведения о конфигурации FairPlay см. в разделе [Защита содержимого HLS с помощью Apple FairPlay или Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Настройка FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Применение динамического шифрования к ресурсу-контейнеру
Чтобы воспользоваться преимуществами динамического шифрования, необходимо закодировать исходный файл в набор MP4-файлов с переменной скоростью.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Выбор ресурса-контейнера для шифрования
Чтобы просмотреть все ресурсы-контейнеры, выберите **Параметры** > **Активы**.

![Параметр "Активы"](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Шифрование с помощью AES или DRM
При выборе для актива параметра **Шифровать** вы увидите два варианта шифрования: **AES** или **DRM**. 

#### <a name="aes"></a>AES
Шифрование с помощью открытого ключа AES будет включено для всех протоколов потоковой передачи: Smooth Streaming, HLS и MPEG-DASH.

![Конфигурация шифрования](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. После выбора **DRM** можно увидеть различные политики защиты содержимого (которые в этот момент необходимо настраивать) и набор потоковых протоколов:

    a. **PlayReady and Widevine with MPEG-DASH** (PlayReady и Widevine с MPEG-DASH): динамически шифрует поток MPEG-DASH, используя системы DRM PlayReady и Widevine.

    Б. **PlayReady and Widevine with MPEG-DASH + FairPlay with HLS** (PlayReady и Widevine с MPEG-DASH + FairPlay с HLS): динамически шифрует поток MPEG-DASH, используя системы DRM PlayReady и Widevine. Этот параметр также шифрует потоки HLS, используя FairPlay.

    c. **PlayReady only with Smooth Streaming, HLS and MPEG-DASH** (PlayReady только с Smooth Streaming, HLS и MPEG-DASH): динамически шифрует потоки Smooth Streaming, HLS и MPEG-DASH, используя систему DRM PlayReady.

    d. **Widevine only with MPEG-DASH** (Widevine только с MPEG-DASH): динамически шифрует поток MPEG-DASH, используя систему DRM Widevine.
    
    д. **FairPlay only with HLS** (FairPlay только с HLS): динамически шифрует поток HLS, используя FairPlay.

2. Чтобы включить шифрование FairPlay на колонке **Глобальные параметры системы защиты содержимого**, выберите **Конфигурация FairPlay**. Выберите **Сертификат приложения** и введите **Секретный ключ приложения**.

    ![Тип шифрования](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Выбрав вариант шифрования, нажмите кнопку **Применить**.

>[!NOTE] 
>Если вы планируете воспроизводить HLS-поток с шифрованием AES в Safari, см. [эту запись блога](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Дополнительная информация
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

