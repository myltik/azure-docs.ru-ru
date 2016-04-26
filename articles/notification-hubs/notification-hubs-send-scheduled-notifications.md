<properties
	pageTitle="Как отправлять запланированные уведомления | Microsoft Azure"
	description="В этом разделе описывается использование запланированных уведомлений с помощью центров уведомлений Azure."
	services="notification-hubs"
	documentationCenter=".net"
	keywords="push-уведомления, push-уведомление, планирование push-уведомлений"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="wesmc"/>

# Практическое руководство. Отправка запланированных уведомлений


##Обзор

Предположим, возникла необходимость отправить уведомление в какой-либо момент в будущем, но у вас нет простого способа пробудить внутренней код для отправки уведомления. Центры уведомлений уровня "Стандартный" поддерживает функцию, которая позволяет запланировать уведомления на будущее до 7 дней.

При отправке уведомления просто используйте класс [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) из пакета SDK центров уведомлений, как показано в следующем примере.

	Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
	var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Кроме того, вы можете отменить ранее запланированное уведомление, используя его notificationId.

	await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Количество запланированных уведомлений, которые можно отправлять, не ограничено.

<!---HONumber=AcomDC_0413_2016-->