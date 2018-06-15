---
title: Использование castLabs для предоставления лицензий Widevine для служб мультимедиа Azure | Документация Майкрософт
description: В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки потока, который зашифрован динамически службой AMS, с помощью лицензий DRM PlayReady и Widevine. Лицензию PlayReady выдает сервер лицензирования служб мультимедиа PlayReady, а лицензию Widevine — сервер лицензирования castLabs.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: aff5b94840e63176358d64a535c9cc0dd9ec617a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783193"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Использование castLabs для доставки лицензий Widevine для служб мультимедиа Azure
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Обзор
В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки потока, который зашифрован динамически службой AMS, с помощью лицензий DRM PlayReady и Widevine. Лицензию PlayReady выдает сервер лицензирования служб мультимедиа PlayReady, а лицензию Widevine — сервер лицензирования **castLabs** .

Чтобы воспроизвести потоковое содержимое, защищенное с помощью CENC (PlayReady или Widevine), используйте [Проигрыватель мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Подробные сведения см. в [документе по AMP](http://amp.azure.net/libs/amp/latest/docs/).

На схеме показана высокоуровневая архитектура служб мультимедиа Azure и интеграция castLabs.

![интеграция](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Настройка типичной системы
* Мультимедийное содержимое хранится в AMS.
* Идентификаторы ключей содержимого хранятся в castLabs и AMS.
* CastLabs и AMS имеют встроенную проверку подлинности маркеров. В следующих разделах рассматриваются маркеры проверки подлинности. 
* Когда клиент запрашивает потоковую передачу видео, содержимое динамически шифруется с помощью **стандартного шифрования** (CENC) и динамически упаковывается сервером AMS для передачи по протоколах Smooth Streaming и DASH. Для протокола потоковой передачи HLS предлагается также шифрование элементарного потока PlayReady M2TS.
* Лицензию PlayReady выдает сервер лицензирования AMS, а лицензию Widevine — сервер лицензирования castLabs. 
* Проигрыватель мультимедиа автоматически выбирает нужную лицензию в соответствии с возможностями платформы клиента. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Создания маркеров проверки подлинности для получения лицензии
CastLabs и AMS поддерживают формат маркера JWT (веб-маркера JSON), который используется для авторизации лицензии. 

### <a name="jwt-token-in-ams"></a>Маркер JWT в AMS
В приведенной ниже таблице описан маркер JWT в AMS. 

| Издатель | Строка издателя из выбранной службы маркеров безопасности |
| --- | --- |
| Аудитория |Строка аудитории из используемой службы маркеров безопасности |
| Claims |Набор утверждений |
| NotBefore |Начало действия маркера |
| Expires |Конец действия маркера |
| SigningCredentials |Общий ключ для сервера лицензирования PlayReady, castLabs и службы маркеров безопасности. Он может быть симметричным или асимметричным. |

### <a name="jwt-token-in-castlabs"></a>Маркер JWT в castLabs
В приведенной ниже таблице описан маркер JWT в castLabs. 

| ИМЯ | ОПИСАНИЕ |
| --- | --- |
| optData |Строка JSON со сведениями о вас. |
| crt |Строка JSON со сведениями о файле, лицензии на него и правах на его воспроизведение. |
| iat |Текущая дата и время в Unix-времени. |
| jti |Уникальный идентификатор этого маркера (каждый маркер может использоваться в системе castLabs только один раз). |

## <a name="sample-solution-set-up"></a>Настройка образца решения
[Образец решения](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) состоит из двух проектов.

* Консольное приложение, которое можно использовать для задания ограничений DRM на уже существующий файл для PlayReady и Widevine.
* Веб-приложение, которое передает маркеры, которые можно рассматривать как ОЧЕНЬ УПРОЩЕННУЮ версию службы маркеров безопасности.

Чтобы использовать консольное приложение, сделайте следующее.

1. Измените файл app.config для настройки учетных данных для серверов AMS, castLabs, настройки службы маркеров безопасности и общего ключа.
2. Отправьте файл в AMS.
3. Получите UUID отправленного файла и измените строку 32 в файле Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Используйте AssetId для именования файла в системе castLabs (строка 44 в файле Program.cs).
   
   Необходимо задать AssetId в **castLabs**; это должна быть уникальная буквенно-цифровая строка.
5. Запустите программу.

Чтобы использовать веб-приложение (служба маркеров безопасности), сделайте следующее.

1. Измените файл web.config для настройки идентификатора подразделения castlabs, службы маркеров безопасности и общего ключа.
2. Разверните приложение на веб-сайтах Azure.
3. Перейдите на веб-сайт.

## <a name="playing-back-a-video"></a>Воспроизведение видео
Чтобы воспроизвести видео со стандартным шифрованием (PlayReady и/или Widevine), можно использовать [Проигрыватель мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). При запуске консольного приложения выводится идентификатор ключа содержимого и URL-адрес манифеста.

1. Откройте новую вкладку и запустите службу маркеров безопасности: http://[имя_службы_маркеров_безопасности].azurewebsites.net/api/token/assetid/[ваш_AssetId_в_CastLabs]/contentkeyid/[идентификатор_ключа_содержимого].
2. Перейдите к [Проигрывателю мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Вставьте URL-адрес для потоковой передачи.
4. Установите флажок **Дополнительные параметры** .
5. В раскрывающемся списке **Защита** выберите PlayReady и/или Widevine.
6. Вставьте маркер, полученный от службы маркеров безопасности в текстовом поле маркера. 
   
   Сервер лицензирования CastLab не использует префикс "Bearer=" перед маркером. Поэтому удалите его перед отправкой маркера.
7. Обновите проигрыватель.
8. Видео должно воспроизводиться.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

