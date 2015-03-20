<properties 
	pageTitle="Обзор пакета SDK для рекламных кампаний в iOS для Azure Mobile Engagement" 
	description="Последние обновления и указания для пакета SDK для iOS для Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#2.0.0

Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в приложение iOS. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](../mobile-engagement-ios-get-started/).

Щелкните, чтобы просмотреть статью [Содержимое пакета SDK](../mobile-engagement-ios-sdk-content)

##Процедуры по интеграции
1. Начните с этого раздела: ["Как интегрировать Mobile Engagement в приложение iOS"](../mobile-engagement-ios-integrate-engagement/)

2. Информация об уведомлениях: ["Как интегрировать Reach (Notifications) в приложение iOS"](../mobile-engagement-ios-integrate-engagement-reach/)

3. Реализация плана добавления тегов: ["Как использовать API для расширенного добавления тегов Mobile Engagement в приложении iOS"](../mobile-engagement-ios-use-engagement-api/)


##Заметки о выпуске

###Версия 2.0.0 (17.02.2015)

-   Первый выпуск Azure Mobile Engagement
-   Конфигурация appId/sdkKey заменена конфигурацией строки подключения.
-   Удален API для отправки и получения произвольных сообщений XMPP из произвольных сущностей XMPP.
-   Удален API для отправки и получения сообщений между устройствами.
-   Улучшения безопасности.
-   Удалено средство отслеживания SmartAd.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](../mobile-engagement-ios-release-notes/)

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить несколько процедур. См. полную версию статьи [Процедуры обновления](../mobile-engagement-ios-upgrade-procedure/).

Для каждой новой версии пакета SDK необходимо сначала заменить (удалить и повторно импортировать в проект Хcode) папки EngagementSDK и EngagementReach.

###От версии 1.16.0 до 2.0.0
Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение на платформе Azure Mobile Engagement. 

>[Azure.IMPORTANT] Службы Capptain и Azure Mobile Engagement отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Mobile Engagement.

При переносе с использованием более ранней версии сначала ознакомьтесь с информацией о переносе в версии 1.16 на веб-сайте Capptain, а затем примените следующую процедуру.

#### Агент

Метод `registerApp:` заменен новым методом "init:". Следует обновить делегат приложения соответствующим образом и использовать строку подключения:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Из пакета SDK было удалено средство отслеживания SmartAd, поэтому нужно просто удалить все экземпляры класса `AETrackModule`.

#### Изменение имени класса

Как часть ребрендинга следует изменить имена нескольких классов и файлов.

Во всех классах префикс CP нужно заменить префиксом AE.

Пример:

-   `CPModule.h` переименовывается на `AEModule.h`.

Во всех классах префикс Capptain нужно заменить префиксом Engagement.

Примеры:

-   Класс `CapptainAgent` переименовывается на `EngagementAgent`.
-   Класс `CapptainTableViewController` переименовывается на `EngagementTableViewController`.
-   Класс `CapptainUtils` переименовывается на `EngagementUtils`.
-   Класс `CapptainViewController` переименовывается на `EngagementViewController`.

<!--HONumber=47-->
