---
title: "Настройка политик защиты содержимого с помощью портала Azure | Документация Майкрософт"
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
ms.openlocfilehash: ecc766abb5df38813b3eb6dde98cdc9afd24ac6b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Настройка политик защиты содержимого с помощью портала Azure
Службы мультимедиа Microsoft Azure (AMS) позволяют защитить данные мультимедиа, покидающие ваш компьютер, на этапах их хранения, обработки и доставки. Службы мультимедиа позволяют доставлять содержимое, зашифрованное динамически с помощью AES (с использованием 128-битных ключей шифрования), общим шифрованием (CENC), используя PlayReady и (или) Widevine DRM, а также Apple FairPlay. 

Службы AMS также обеспечивают доставку лицензий DRM и незащищенных ключей AES авторизованным клиентам. Портал Azure позволяет создать единую **политику авторизации ключей и лицензий** для всех типов шифрования.

В этой статье показано, как настроить политику защиты содержимого с помощью портала Azure. В статье также показано, как применить динамическое шифрование к ресурсам-контейнерам.

## <a name="start-configuring-content-protection"></a>Начало настройки защиты содержимого
Чтобы приступить к настройке защиты содержимого (действующей глобально для учетной записи AMS) с помощью портала, выполните следующие действия:
1. Создание учетной записи служб мультимедиа Azure с помощью [портале Azure](https://portal.azure.com/).
2. Выберите **Параметры** > **Защита контента**.

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>политику авторизации ключей и лицензий
Службы AMS поддерживают несколько способов аутентификации пользователей, запрашивающих ключи или лицензии. Чтобы получить ключ (лицензию) содержимого, клиент должен соответствовать заданной для этого ключа (лицензии) политике авторизации. Для политики авторизации ключа содержимого можно задать одно или несколько ограничений: **открытая** авторизация или авторизация с помощью **маркера**.

Портал Azure позволяет создать единую **политику авторизации ключей и лицензий** для всех типов шифрования.

### <a name="open-authorization"></a>Открытая авторизация
Ограничение открытого типа означает, что система будет доставлять ключ всем, кто его запросит. Это ограничение подходит для тестирования. 

### <a name="token-authorization"></a>Авторизация с помощью маркера
При ограничении по маркеру к политике должен прилагаться маркер, выданный службой маркеров безопасности (STS). Службы мультимедиа поддерживают маркеры в формате простого веб-маркера (SWT) и формате веб-маркера JSON (JWT). Службы мультимедиа не предоставляют службы маркеров безопасности. Для выдачи маркеров можно создать пользовательскую службу STS или использовать службу Microsoft Azure ACS. Чтобы создать маркер, подписанный указанным ключом, и получить утверждения, указанные в конфигурации ограничения по маркерам, должна быть настроена служба маркеров безопасности. Служба доставки ключей служб мультимедиа возвращает клиенту запрошенный ключ (или лицензию), если маркер является допустимым, а его утверждения соответствуют утверждениям, настроенным для ключа (или лицензии).

При настройке политики ограничения по маркеру необходимо задать такие параметры, как "основной ключ проверки", "издатель" и "аудитория". Основной ключ проверки содержит ключ, которым подписан маркер, а издатель — это служба маркеров безопасности, которая выдает маркер. Аудитория (иногда называется областью) описывает назначение маркера или ресурс, доступ к которому обеспечивает маркер. Служба доставки ключей служб мультимедиа проверяет, соответствуют ли эти значения в маркере значениям в шаблоне.

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Шаблон лицензии PlayReady
Шаблон лицензии PlayReady позволяет включить функции в лицензии PlayReady. Дополнительные сведения о шаблоне лицензии PlayReady см. в статье [Обзор шаблонов лицензий PlayReady служб мультимедиа](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Временный
Если при настройке лицензии выбрать параметр "Временный", то она будет храниться в памяти лишь до тех пор, пока проигрыватель использует эту лицензию.  

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Постоянный
Если при настройке лицензии выбрать параметр "Постоянный", то она будет сохранена в постоянном хранилище на клиенте.

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Шаблон лицензии Widevine
Шаблон лицензии Widevine позволяет включить функции в лицензии Widevine.

### <a name="basic"></a>Basic
Если выбрать тип лицензии **Основной**, то шаблон будет создан со всеми значениями по умолчанию.

### <a name="advanced"></a>Расширенная
Подробные сведения о шаблоне прав Widevine см. в статье [Общие сведения о шаблоне лицензии Widevine](media-services-widevine-license-template-overview.md).

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Настройка FairPlay
Чтобы включить шифрование FairPlay, необходимо предоставить сертификат приложения и секретный ключ приложения (ASK), используя параметр настройки FairPlay. Подробные сведения о настройке и требованиях FairPlay см. в [этой статье](media-services-protect-hls-with-FairPlay.md).

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Применение динамического шифрования к ресурсу-контейнеру
Чтобы воспользоваться преимуществами динамического шифрования, необходимо закодировать исходный файл в набор MP4-файлов с переменной скоростью.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Выбор ресурса-контейнера для шифрования
Чтобы просмотреть все ресурсы-контейнеры, выберите **Параметры** > **Активы**.

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Шифрование с помощью AES или DRM
При нажатии кнопки **Зашифровать** появятся два варианта: **AES** или **DRM**. 

#### <a name="aes"></a>AES
Шифрование с помощью открытого ключа AES будет включено для всех протоколов потоковой передачи: Smooth Streaming, HLS и MPEG-DASH.

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
Если выбрать вкладку "DRM", то отобразятся различные варианты политик защиты содержимого (которые вы ранее настроили), а также набор протоколов потоковой передачи.

* **PlayReady and Widevine with MPEG-DASH** (PlayReady и Widevine с MPEG-DASH): будет динамически шифровать поток MPEG-DASH, используя системы DRM PlayReady и Widevine.
* **PlayReady and Widevine with MPEG-DASH + FairPlay with HLS** (PlayReady и Widevine с MPEG-DASH + FairPlay с HLS): будет динамически шифровать поток MPEG-DASH, используя системы DRM PlayReady и Widevine. Также будет шифровать потоки HLS, используя FairPlay.
* **PlayReady only with Smooth Streaming, HLS and MPEG-DASH** (PlayReady только с Smooth Streaming, HLS и MPEG-DASH): будет динамически шифровать потоки Smooth Streaming, HLS и MPEG-DASH, используя систему DRM PlayReady.
* **Widevine only with MPEG-DASH** (Widevine только с MPEG-DASH): будет динамически шифровать поток MPEG-DASH, используя систему DRM Widevine.
* **FairPlay only with HLS** (FairPlay только с HLS): будет динамически шифровать поток HLS, используя FairPlay.

Чтобы включить шифрование FairPlay, необходимо предоставить сертификат приложения и секретный ключ приложения (ASK), используя параметр настройки FairPlay в колонке "Content Protection settings" (Параметры защиты содержимого).

![Защита содержимого](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Выбрав вариант шифрования, нажмите кнопку **Применить**.

>[!NOTE] 
>Если вы планируете воспроизводить HLS с шифрованием AES в Safari, см. [эту запись блога](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

