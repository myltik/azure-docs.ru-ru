<properties 
	pageTitle="Содержимое пакета SDK для Магазина Windows для Azure Mobile Engagement" 
	description="Последние обновления и указания для пакета SDK для Магазина Windows для Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Содержимое пакета SDK

В этом документе указано и описано содержимое архива SDK.

##Папка `Root`

Эта папка содержит копии лицензий на программное обеспечение и ссылку на электронную документацию.

`documentation.html` : Содержит ссылку на электронную документацию по пакету SDK для Engagement для приложений C\# Metro для Windows 8.

Эта папка также содержит файлы лицензий.

##Папка `/lib`

Эта папка содержит информацию о том, где можно получить пакет SDK для Engagement.

`azuresdk-mobileengagement-windows-X.X.X.nupkg` : Пакет NuGet для интеграции пакета SDK.

##Папка `/Resources`

Эта папка содержит все ресурсы, необходимые для Engagement. Вы также можете настроить их в соответствии с особенностями своего приложения.

`EngagementConfiguration.xml` : файл конфигурации Engagement, в котором вы можете настроить параметры Engagement (строку подключения Engagement, сообщение о сбое...).

### Папка /html

`EngagementNotification.html` : HTML-структура веб-представления `Уведомление`.

`EngagementAnnouncement.html` : HTML-структура веб-представления `Объявление`.

### Папка /images

`EngagementIconNotification.png` : фирменный значок, отображающийся слева от уведомления.

`EngagementIconOk.png` : Значок `ОК` страниц содержимого рекламной кампании для кнопки действия или проверки.

`EngagementIconNOK.png` : Значок `Не ОК`, используемый при отключении кнопки проверки страниц содержимого рекламных кампаний.

`EngagementIconClose.png` : Значок `Закрыть` для кнопки закрытия уведомлений и содержимого рекламных кампаний.

### Папка /overlay

`EngagementOverlayAnnouncement.xaml` : XAML-структура `объявления`.

`EngagementOverlayAnnouncement.xaml.cs` : Код, связанный с `EngagementOverlayAnnouncement.xaml`.

`EngagementOverlayNotification.xaml` : XAML-структура `Уведомления`.

`EngagementOverlayNotification.xaml.cs` : Код, связанный с `EngagementOverlayNotification.xaml`.

`EngagementPageOverlay.cs` : Кода для отображения объявлений и уведомлений с `наложением`.

##Папка `/src/agent`

Эта папка содержит EngagementPage.

`EngagementPage.cs` : базовый класс для страниц, которые будут автоматически сообщать об активности в Engagement.

<!--HONumber=47-->
