<properties 
	pageTitle="Содержимое пакета SDK для универсальных приложений для Windows" 
	description="Изучите содержимое пакета SDK универсальных приложений для Windows для Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="piyushjo" />

#Содержимое пакета SDK для универсальных приложений для Windows

В этом документе перечислено и описано содержимое, которое развертывается в приложении с помощью пакета SDK.

##Папка `/Resources`

Эта папка содержит все ресурсы, необходимые для Mobile Engagement. Вы также можете настроить их в соответствии с особенностями своего приложения.

- `EngagementConfiguration.xml`: файл конфигурации Mobile Engagement, в котором вы можете настроить параметры Mobile Engagement (строку подключения Mobile Engagement, сообщение о сбое и т. д.).

### Папка /html

- `EngagementNotification.html`: HTML-код веб-представления `Notification`.

- `EngagementAnnouncement.html`: HTML-код веб-представления `Announcement`.

### Папка /images

- `EngagementIconNotification.png`: фирменный значок, отображающийся слева от уведомления. Замените его собственным логотипом.

- `EngagementIconOk.png`: значок `Ok` страниц содержимого рекламной кампании для кнопки действия или проверки.

- `EngagementIconNOK.png`: значок `NOK`, используемый, когда кнопка проверки на страницах содержимого рекламных кампаний отключена.
 
- `EngagementIconClose.png`: значок `Close` для кнопки закрытия уведомлений и содержимого рекламных кампаний.

### Папка /overlay

- `EngagementOverlayAnnouncement.xaml` : XAML-код `Announcement`.

- `EngagementOverlayAnnouncement.xaml.cs`: код, связанный с `EngagementOverlayAnnouncement.xaml`.
 
- `EngagementOverlayNotification.xaml` : XAML-код `Notification`.
 
- `EngagementOverlayNotification.xaml.cs`: код, связанный с `EngagementOverlayNotification.xaml`.
 
- `EngagementPageOverlay.cs`: кода для отображения объявлений и уведомлений `Overlay`.
  

<!---HONumber=July15_HO4-->