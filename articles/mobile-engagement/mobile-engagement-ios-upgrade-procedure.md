<properties 
	pageTitle="Процедуры обновления SDK для рекламных кампаний в iOS для Azure Mobile Engagement" 
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

#Процедуры обновления

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Для каждой новой версии пакета SDK необходимо сначала заменить (удалить и повторно импортировать в проект Хcode) папки EngagementSDK и EngagementReach.

##От версии 1.16.0 до 2.0.0
Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение на платформе Azure Mobile Engagement. При миграции с более ранней версии сначала выполните перенос на версию 1.16, как указано на веб-сайте Capptain, а затем примените следующую процедуру.

>[Azure.IMPORTANT]Службы Capptain и Azure Mobile Engagement отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Mobile Engagement.

### Агент

Метод `registerApp:` заменен новым методом `init:`. Следует обновить делегат приложения соответствующим образом и использовать строку подключения:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Из пакета SDK было удалено средство отслеживания SmartAd, поэтому нужно просто удалить все экземпляры класса `AETrackModule`.

### Изменение имени класса

Как часть ребрендинга следует изменить имена нескольких классов и файлов.

Во всех классах префикс CP нужно заменить префиксом AE.

Пример:

-   `CPModule.h` переименовывается в `AEModule.h`.

Во всех классах префикс Capptain нужно заменить префиксом Engagement.

Примеры:

-   Класс `CapptainAgent` переименовывается в `EngagementAgent`.
-   Класс `CapptainTableViewController` переименовывается в `EngagementTableViewController`.
-   Класс `CapptainUtils` переименовывается в `EngagementUtils`.
-   Класс `CapptainViewController` переименовывается в `EngagementViewController`.

<!--HONumber=54--> 