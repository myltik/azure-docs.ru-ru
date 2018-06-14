---
title: Как отправлять запланированные уведомления | Документация Майкрософт
description: В этом разделе описывается использование запланированных уведомлений с помощью центров уведомлений Azure.
services: notification-hubs
documentationcenter: .net
keywords: push-уведомления, push-уведомление, планирование push-уведомлений
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776983"
---
# <a name="how-to-send-scheduled-notifications"></a>Практическое руководство. Отправка запланированных уведомлений
## <a name="overview"></a>Обзор
Предположим, возникла необходимость отправить уведомление в какой-либо момент в будущем, но у вас нет простого способа пробудить внутренней код для отправки уведомления. Центры уведомлений уровня "Стандартный" поддерживают функцию, которая позволяет запланировать уведомления на будущее до 7 дней.

При отправке уведомления просто используйте класс [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) из пакета SDK центров уведомлений, как показано в следующем примере.

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Кроме того, вы можете отменить ранее запланированное уведомление, используя его notificationId.

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Количество запланированных уведомлений, которые можно отправлять, не ограничено.

