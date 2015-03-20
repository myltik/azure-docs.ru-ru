<properties 
	pageTitle="Интеграция пакета SDK для Windows Phone для Azure Mobile Engagement" 
	description="Информация о содержимом пакета SDK для Windows Phone для Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
#Содержимое пакета SDK

В этом документе указано и описано содержимое архива SDK.

##Папка `Root`

Эта папка содержит копии лицензий на программное обеспечение и ссылку на электронную документацию.

`documentation.html` : Содержит ссылку на электронную документацию по пакету SDK для Engagement для Windows Phone.

Эта папка также содержит файлы лицензий.

##Папка `/lib`

Эта папка содержит информацию о том, где можно получить пакет SDK для Engagement.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : Пакет NuGet для интеграции пакета SDK.

##Папка `/Resources`

Эта папка содержит все ресурсы, необходимые для Engagement. Вы также можете настроить их в соответствии с особенностями своего приложения.

`EngagementIconNotification.png` : фирменный значок, отображающийся слева от уведомления.

`EngagementIconOk.png` : значок "ОК" на панели ApplicationBar на страницах Reach.

`EngagementIconCancel.png` : значок "Отмена" на панели ApplicationBar на страницах Reach.

`EngagementIconCloseLight.png` : значок "Закрыть" уведомлений рекламных кампаний в Engagement для светлой темы Windows Phone.

`EngagementIconCloseDark.png` : значок "Закрыть" уведомлений рекламных кампаний в Engagement для темной темы Windows Phone.

`EngagementConfiguration.xml` : файл конфигурации Engagement, в котором вы можете настроить параметры Engagement (строку подключения Engagement, сообщение о сбое...).

##Папка `/src/agent`

Эта папка содержит EngagementPage.

`EngagementPage.cs` : базовый класс для страниц, которые будут автоматически сообщать об активности в Engagement.

##Папка `/src/reach`

В этой последней папке вы найдете используемые по умолчанию XAML-файлы (и их аналоги на языке C\#) для каждой страницы.

Вы можете использовать их в качестве основы для собственных страниц. Информацию о том, что именно нужно сделать, см. в комментариях.

### Объявление TextView

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### Объявление WebView

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### Опрос

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### Уведомление

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
