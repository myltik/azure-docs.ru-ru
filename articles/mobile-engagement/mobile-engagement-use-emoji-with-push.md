---
title: Использование смайликов эмодзи в Службах мобильного взаимодействия Azure
description: Как использовать смайлики эмодзи в push-уведомлениях
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b5b0e7bfe07054d093dc164cb5f72bde4ba28170
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Использование смайликов эмодзи в push-уведомлениях
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Вы легко можете включать смайлики эмодзи в свои push-уведомления. 

1. Сначала вам нужно найти смайлик эмодзи, который будет добавлен в сообщение. Убедитесь, что выбранный смайлик эмодзи будет отображаться на целевом устройстве, так как производителям устройств обычно требуется некоторое время, чтобы добавить новые смайлики эмодзи на платформы устройств. 
2. В системе **Windows** вы можете перейти по этой [ссылке](http://apps.timwhitlock.info/emoji/tables/unicode) и скопировать значок Native.
   
    ![][7] 
3. В системе **Mac** вы можете найти эмодзи в приложении Dictionary (Словарь), выбрав Edit -> Emoji & Symbols (Правка -> Эмодзи и символы).
   
    ![][6] 
4. На портале Служб мобильного взаимодействия Azure перейдите на вкладку **Reach** (Рекламная кампания). Выберите тип push-уведомления (объявление, опрос и т. д.). В данном примере мы выберем объявление.
5. Заполняйте поля уведомления, пока не дойдете до основного текста. В это поле можно добавить смайлик эмодзи. Вы можете вставить его как в заголовок, так и в основной текст. Вам нужно будет перетащить или скопировать выбранный ранее смайлик эмодзи. 
   
    ![][1]
6. Заполните остальные поля уведомления и сохраните его. 
7. При тестировании или активации объявления появится уведомление с выбранным вами смайликом.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

