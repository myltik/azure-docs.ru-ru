---
title: "Настройка политик защиты содержимого с помощью портала Azure | Документы Microsoft"
description: "В этой статье показано, как с помощью портала Azure настроить политики защиты содержимого. В статье также показано, как включить динамическое шифрование для ресурсов-контейнеров."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 805e1246dbc984582528d2b351d2f14ab2e811fc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Настройка политик защиты содержимого с помощью портала Azure
 С помощью служб мультимедиа Azure можно защитить от времени, когда они покидают ваш компьютер через хранения, обработки и доставки мультимедиа. Службы мультимедиа позволяют доставлять контент зашифрованных динамически с помощью Advanced Encryption Standard (AES) с помощью 128-битные ключи шифрования. Вы также можете использовать с общее шифрование (CENC) с помощью PlayReady и/или Widevine управления цифровыми правами (DRM) и Apple FairPlay. 

Служба Media Services предоставляет услугу для доставки лицензии DRM и незащищенных ключей авторизованным клиентам AES. Портал Azure можно использовать для создания одной политики авторизации ключа/лицензии для всех типов шифрования.

В этой статье показано, как настроить политики защиты содержимого с помощью портала. В статье также показано, как применить динамическое шифрование к ресурсам-контейнерам.

## <a name="start-to-configure-content-protection"></a>Начать настройку защиты содержимого
Использовать портал для настройки глобальных защиты содержимого с помощью учетной записи служб мультимедиа, выполните следующие действия:

1. В [портала](https://portal.azure.com/), выберите учетную запись служб мультимедиа.

2. Выберите **Параметры** > **Защита контента**.

    ![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>политику авторизации ключей и лицензий
Службы мультимедиа поддерживают несколько способов проверки подлинности пользователей, отправляющих запросы ключей или лицензий. Необходимо настроить политику авторизации ключа контента. Клиент затем должен удовлетворять политики ключ/лицензии можно доставить его. Политика авторизации ключа контента может иметь одно или несколько ограничений авторизации, открытых или токена ограничения.

Портал можно использовать для создания одной политики авторизации ключа/лицензии для всех типов шифрования.

### <a name="open-authorization"></a>Открытая авторизация
Ограничение Open означает, что система доставляет ключ всем, кто ключа запроса. Это ограничение подходит для тестирования. 

### <a name="token-authorization"></a>Авторизация с помощью маркера
Политики ограниченного токена должен соответствовать маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают только токены в форматах JSON Web Token (JWT) и простой веб-токен (SWT). Службы мультимедиа не предоставляют STS. Можно создать настраиваемую STS или использовать Azure Access Control Service для выдачи токенов. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности. Если маркер является допустимым и утверждения в токене соответствуют настроенным для ключей (или лицензий), служба доставки ключей Media Services возвращает клиенту запрошенный ключ (или лицензий).

При настройке политики ограниченного токена, необходимо указать основной ключ проверки, издателя и аудитории параметров. Основной ключ проверки содержит ключ, который был подписан токен. Поставщик является службой маркеров безопасности, которая выдает маркер. Аудитория (иногда называется областью) описывает назначение маркера или ресурс, доступ к которому обеспечивает маркер. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в маркере значениям в шаблоне.

![политику авторизации ключей и лицензий](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Шаблон лицензии PlayReady
Шаблон лицензии PlayReady задает функциональные возможности, которая включена лицензии PlayReady. Дополнительные сведения о шаблоне лицензии PlayReady см. в разделе [Обзор шаблона лицензий PlayReady служб мультимедиа](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Несохраненные
Если настраивается лицензию несохраненные, хранится в памяти только в том случае, пока проигрыватель использует лицензии.  

![Несохраненные защиты содержимого](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Постоянный
Если лицензия будет настроен как постоянные, он будет сохранен в постоянном хранилище на клиентском компьютере.

![Постоянная защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Шаблон лицензии Widevine
Шаблон лицензии Widevine задает функциональные возможности, которая включена в лицензии Widevine.

### <a name="basic"></a>базовая;
При выборе **основные**, шаблон создается со всеми значениями по умолчанию.

### <a name="advanced"></a>Расширенная
Дополнительные сведения о шаблоне Widevine прав см. в разделе [Обзор шаблона лицензий Widevine](media-services-widevine-license-template-overview.md).

![Расширенный защиты содержимого](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Настройка FairPlay
Чтобы включить шифрование FairPlay, выберите **FairPlay конфигурации**. Выберите **сертификат приложения** и введите **секретный ключ приложения**. Дополнительные сведения о конфигурации FairPlay и требования см. в разделе [защиты содержимого с Apple FairPlay или Microsoft PlayReady контента HLS](media-services-protect-hls-with-FairPlay.md).

![Настройка FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Применение динамического шифрования к ресурсу-контейнеру
Чтобы воспользоваться преимуществами динамического шифрования, кодирования файла исходного кода в набор файлов MP4 с адаптивной скоростью.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Выбор ресурса-контейнера для шифрования
Чтобы просмотреть все ресурсы-контейнеры, выберите **Параметры** > **Активы**.

![Параметр активы](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Шифрование с помощью AES или DRM
При выборе **Encrypt** для актива, вы видите два варианта: **AES** или **DRM**. 

#### <a name="aes"></a>AES
Очистить по всем протоколам потоковой передачи включено шифрование ключа AES: Smooth Streaming, HLS и MPEG-DASH.

![Конфигурация шифрования](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. После выбора **DRM**, вы видите различных содержимого политик защиты (которые должны настраиваться с использованием этой точки) и набор потоковых протоколов:

    a. **PlayReady и Widevine с MPEG-DASH** динамически шифрует потока MPEG-DASH с помощью PlayReady и Widevine DRMs.

    Б. **PlayReady и Widevine с MPEG-DASH + FairPlay с HLS** динамическое шифрование потока MPEG-DASH с помощью PlayReady и Widevine DRMs. Этот параметр также шифрует потоков HLS с помощью FairPlay.

    c. **PlayReady только с Smooth Streaming, HLS и MPEG-DASH** динамически шифрует потоки Smooth Streaming, HLS и MPEG-DASH с помощью PlayReady DRM.

    d. **Widevine только с MPEG-DASH** динамически шифрует MPEG-DASH с Widevine DRM.
    
    д. **FairPlay только с HLS** динамически шифрует потока HLS с FairPlay.

2. Чтобы включить шифрование FairPlay для **содержимого глобальные параметры защиты** колонке выберите **FairPlay конфигурации**. Выберите **сертификат приложения**и введите **секретный ключ приложения**.

    ![Тип шифрования](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Сделав выбор шифрования, выберите **применить**.

>[!NOTE] 
>Если вы планируете воспроизведение HLS с шифрованием AES в Safari, см. в записи блога [шифрования HLS в Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

