---
title: Создание клипов с помощью Azure Media Clipper | Документация Майкрософт
description: Обзор Azure Media Clipper — инструмент для создания клипов мультимедиа из файлов
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f3822386d0d16b1feaf16853424329558a18f910
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782653"
---
# <a name="create-clips-with-azure-media-clipper"></a>Создание клипов с помощью Azure Media Clipper
Azure Media Clipper — это бесплатная библиотека JavaScript, которая позволяет веб-разработчикам предоставлять пользователям интерфейс для создания клипов мультимедиа. Это средство можно интегрировать в любую веб-страницу. Оно предоставляет API-интерфейсы для загрузки файлов и отправки заданий создания клипов.

Azure Media Clipper позволяет делать следующее:
- обрезать ненужное предшествующее и последующее содержимое из динамических архивов; 
- составлять видеоколлажи из динамических событий AMS, динамических архивов или файлов fMP4 VOD; 
- объединять видео из нескольких источников; 
- создавать сводные клипы из ресурсов мультимедиа AMS; 
- обрезать видео с точностью до кадра; 
- создавать фильтры динамических манифестов поверх существующих динамических файлов и ресурсов VOD с точностью до группы изображений (GOP); 
- создавать задания кодирования для файлов в вашей учетной записи служб мультимедиа.

Для запроса новых функций и обратной связи отправляйте сообщения в [UserVoice для служб мультимедиа Azure](http://aka.ms/amsvoice/). При наличии конкретных проблем, вопросов или найденных программных ошибок сообщите об этом группе разработки служб мультимедиа по адресу amcinfo@microsoft.com.

На следующем изображении представлен интерфейс Clipper: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Заметки о выпуске
В следующем списке приведена ссылка на запись а блоге, а также описаны известные проблемы и журнал изменений последней версии средства Clipper:
- [Запись блога](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Список известных проблем](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Журнал изменений](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Поддержка браузеров
Средство Azure Media Clipper создано на основе современных технологий HTML5. Оно поддерживает следующие браузеры:

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54+
- Safari 10+
- Firefox 50+

> [!NOTE]
> Сейчас поддерживается только воспроизведение потоков HTML5 из служб мультимедиа Azure.

## <a name="language-support"></a>Поддержка языков
Мини-приложение Clipper доступно на следующих 18 языках:
- Китайский (упрощенное письмо)
- Китайский (традиционное письмо)
- Чешский
- Нидерландский, фламандский
- Английский
- Французский
- Немецкий
- Венгерский
- Итальянский
- Японский
- Корейский
- Польский
- Португальский (Бразилия)
- Португальский (Португалия)
- Русский
- Испанский
- Шведский
- Турецкий

## <a name="next-steps"></a>Дополнительная информация
Чтобы начать работу с Azure Media Clipper, прочитайте статью о [начале работы](media-services-azure-media-clipper-getting-started.md). Из нее вы узнаете, как развернуть мини-приложение.
