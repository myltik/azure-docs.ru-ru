---
title: "Как отправлять запланированные уведомления | Документация Майкрософт"
description: "В этом разделе описывается использование запланированных уведомлений с помощью центров уведомлений Azure."
services: notification-hubs
documentationcenter: .net
keywords: "push-уведомления, push-уведомление, планирование push-уведомлений"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>Практическое руководство. Отправка запланированных уведомлений
## <a name="overview"></a>Обзор
Предположим, возникла необходимость отправить уведомление в какой-либо момент в будущем, но у вас нет простого способа пробудить внутренней код для отправки уведомления. Центры уведомлений уровня "Стандартный" поддерживает функцию, которая позволяет запланировать уведомления на будущее до 7 дней.

При отправке уведомления просто используйте класс [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) из пакета SDK центров уведомлений, как показано в следующем примере.

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Кроме того, вы можете отменить ранее запланированное уведомление, используя его notificationId.

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Количество запланированных уведомлений, которые можно отправлять, не ограничено.

