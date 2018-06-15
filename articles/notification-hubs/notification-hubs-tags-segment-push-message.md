---
title: Маршрутизация и выражения тегов
description: В этом разделе рассказывается о маршрутизации и выражениях тегов для центров уведомлений Azure.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: e08fca0b6b57d654f2b2ff7b935f38d8c517487b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776171"
---
# <a name="routing-and-tag-expressions"></a>Маршрутизация и выражения тегов
## <a name="overview"></a>Обзор
Выражения тегов позволяют выбирать определенные целевые наборы устройств, а точнее регистраций, при отправке push-уведомлений через центры уведомлений.

## <a name="targeting-specific-registrations"></a>Выбор регистраций
Единственный способ выбрать определенные регистрации для получения уведомлений — это связать с ними теги, а затем указывать эти теги в качестве назначения. Как описано в статье [Управление регистрациями](notification-hubs-push-notification-registration-management.md), для получения push-уведомлений приложение должно зарегистрировать маркер устройства в центре уведомлений. После создания регистрации в центре уведомлений серверная часть приложения может отправлять ей push-уведомления.
Серверная часть приложения выбирает целевые регистрации для отправки определенных уведомлений следующим образом.

1. **Широковещательная рассылка**: уведомление получают все регистрации в центре уведомлений.
2. **По тегу**: уведомление получают все регистрации, которые содержат указанный тег.
3. **По выражению тега**: уведомление получают все регистрации, набор тегов которых соответствует указанному выражению.

## <a name="tags"></a>Теги
Тегом может быть любая строка длиной до 120 знаков, содержащая буквы, цифры и следующие знаки: "_", "@", "#", ".", ":", "-". В следующем примере показано приложение, из которого можно получать всплывающие уведомления об определенных музыкальных группах. В этом случае для маршрутизации уведомлений удобно пометить регистрации тегами различных групп, как показано на следующем рисунке.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

На этом рисунке сообщение с тегом **Beatles** поступает только на планшет, зарегистрированный с тегом **Beatles**.

Дополнительные сведения о создании регистраций по тегам см. в статье [Управление регистрацией](notification-hubs-push-notification-registration-management.md).

Можно отправлять уведомления для тегов, используя методы отправки уведомлений класса `Microsoft.Azure.NotificationHubs.NotificationHubClient` из пакета SDK [центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Также можно использовать Node.js или интерфейсы API REST push-уведомлений.  Ниже приведен пример с использованием пакета SDK.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Теги необязательно определяются заранее и могут быть связаны с несколькими аспектами приложения. Например, пользователи приложения из этого примера могут оставлять свои комментарии по группам и хотят получать всплывающие уведомления не только о комментариях по своим любимым группам, но и обо всех комментариях от своих друзей, независимо от того, какую группу они комментируют. На рисунке ниже показан пример такого сценария.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

На этом рисунке Алиса интересуется новостями о Beatles, а Боб — о Wailers. Боба также интересуют комментарии Чарли, а Чарли интересуется Wailers. Когда отправляется уведомление о комментарии Чарли по Beatles, его получают и Алиса, и Боб.

Несмотря на то, что теги могут соответствовать нескольким характеристикам сразу (например, "band_Beatles" или "follows_Charlie"), они являются простыми строками, это не свойства со значениями. Успешное сопоставление с регистрацией происходит только при наличии или отсутствии конкретного тега.

Полное пошаговое руководство по использованию тегов для отправки сообщений по группам интересов см. в статье [Использование Центров уведомлений для передачи экстренных новостей](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>Использование тегов для выбора конечных пользователей
Теги также могут использоваться для идентификации всех устройств определенного пользователя. Регистрации можно пометить тегом, который содержит идентификатор пользователя, как показано на следующем рисунке.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

На этом рисунке сообщение с тегом Alice поступает во все регистрации с тегом Alice, то есть на все устройства Алисы.

## <a name="tag-expressions"></a>Выражения тегов
Бывают случаи, когда уведомление должно направляться в ряд регистраций, которые определяются не одним тегом, а логическим выражением с тегами.

Рассмотрим спортивное приложение, которое отправляет напоминание всем пользователям в Бостоне об игре между командами Red Sox и Cardinals. Если в клиентском приложении регистрируются теги об интересе к командам и месту, то уведомление должно отправляться всем пользователям в Бостоне, интересующимся Red Sox или Cardinals. Это условие можно задать с помощью следующего логического выражения:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Выражения тегов могут содержать любые логические операторы, такие как AND (&&), OR (||) и NOT (!). В них также можно использовать скобки. В выражении можно использовать не больше 20 тегов, если в нем содержатся только операторы OR; в противном случае максимальное число тегов равно 6.

Ниже приведен пример отправки уведомлений с помощью выражения тегов и пакета SDK.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)";    

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Sox</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Sox</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
