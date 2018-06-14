---
title: Использование существующих проигрывателей для воспроизведения содержимого в Azure | Документация Майкрософт
description: В этой статье перечислены существующие проигрыватели, которые можно использовать для воспроизведения содержимого.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: 48f373b013b1192c353352b801876d706d91dd28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23030519"
---
# <a name="playing-your-content-with-existing-players"></a>Воспроизведение содержимого с помощью существующих проигрывателей
Службы мультимедиа Azure поддерживают многие популярные форматы потоковой передачи, например Smooth Streaming, HTTP Live Streaming и MPEG-Dash. В этой статье описываются имеющиеся проигрыватели, которые можно использовать для тестирования потоков.

### <a name="the-azure-portal-media-services-content-player"></a>Проигрыватель содержимого служб мультимедиа портала Azure
Портал **Azure** предлагает проигрыватель содержимого, с помощью которого можно проверить видео.

Выберите нужное видео (убедитесь, что оно [опубликовано](media-services-portal-publish.md)) и нажмите кнопку **Воспроизвести** в нижней части портала.

Важные особенности

* **ПРОИГРЫВАТЕЛЬ КОНТЕНТА СЛУЖБ МУЛЬТИМЕДИА** выполняет воспроизведение из конечной точки потоковой передачи по умолчанию. Если требуется воспроизвести из конечной точке потоковой передачи не по умолчанию, используйте другой проигрыватель. Например, [Проигрыватель мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Проигрыватель мультимедиа Azure
Используйте [Проигрыватель мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) для воспроизведения содержимого (незашифрованного или защищенного) в любом из следующих форматов:

* Smooth Streaming
* MPEG DASH
* HLS
* Последовательный MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>Шифрование AES с маркером
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Проигрыватели Silverlight
#### <a name="monitoring"></a>Мониторинг
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady с маркером
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Проигрыватели DASH
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Другие
Для проверки URL-адресов HLS также можно использовать:

* **Safari** на устройстве iOS или
* **3ivx HLS Player** в Windows.

## <a name="developing-video-players"></a>Разработка видеопроигрывателей
Сведения о том, как разрабатывать собственные проигрыватели, см. в статье [Разработка приложений видеопроигрывателя](media-services-develop-video-players.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
