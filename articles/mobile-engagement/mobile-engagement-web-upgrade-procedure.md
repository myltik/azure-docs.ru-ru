<properties
	pageTitle="Процедуры обновления веб-пакета SDK для Azure Mobile Engagement | Microsoft Azure"
	description="Последние обновления и процедуры для веб-пакета SDK для Azure Mobile Engagement."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />


# Процедуры обновления веб-пакета SDK для Azure Mobile Engagement

Если вы уже интегрировали в приложение старую версию пакета SDK, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить несколько процедур. Например, при миграции из версии 1.4.0 в 1.6.0, необходимо сначала выполнить процедуру "из версии 1.4.0 в 1.5.0", а затем процедуру "из версии 1.5.0 в 1.6.0".

Независимо от того, с какой версии выполняется обновление, необходимо заменить `azure-engagement.js` на новую версию.

## С версии 1.2.1 до 2.0.0

Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение на платформе Azure Mobile Engagement. При миграции с более ранней версии сначала ознакомьтесь с информацией о переносе на версию 1.2.1 на веб-сайте Capptain, а затем выполните следующую процедуру.

Эта версия веб-пакета SDK для Engagement не поддерживает функции samsung-tv, OperaTV, webOS и Reach.

>[AZURE.IMPORTANT] Службы Capptain и Azure Mobile Engagement отличаются друг от друга. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Mobile Engagement.

### Файлы JavaScript

Замените файл `capptain-sdk.js` файлом `azure-engagement.js` и соответствующим образом обновите импортируемые элементы в сценарии.

### Удаление модуля Capptain Reach

Эта версия не поддерживает функцию Reach. Если вы интегрировали модуль Capptain Reach в приложение, то его необходимо удалить.

Удалите импортированный код css Reach со страницы, также удалите связанный CSS-файл (по умолчанию: capptain-reach.css).

Удалите ресурсы Reach: изображение для закрытия (по умолчанию: capptain-close.png) и фирменный значок (по умолчанию: capptain-notification-icon).

Удалите пользовательский интерфейс Reach для получения уведомлений в приложении. Макет по умолчанию выглядит следующим образом.

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

Удалите пользовательский интерфейс Reach для текстовых и веб-объявлений и опросов. Макет по умолчанию выглядит следующим образом.

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

Удалите объект `reach` из конфигурации, если таковой имеется. Он выглядит следующим образом.

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Удалите все прочие настройки Reach, например категории.

### Удаление нерекомендуемых интерфейсов API

Некоторые интерфейсы API из Capptain являются нерекомендуемыми в версии пакета SDK для Engagement.

Удалите все вызовы к следующим API: `agent.connect`, `agent.disconnect`, `agent.pause`, `agent.sendMessageToDevice`.

Удалите следующие обратные вызовы, если они имеются, из конфигурации Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, `onPushMessageReceived`.

### Конфигурация

Теперь служба Engagement использует строку подключения для настройки идентификаторов пакета SDK, таких как идентификатор приложения.

Замените идентификатор приложения строкой подключения. Обратите внимание, что глобальный объект для настройки пакета SDK перемещается из `capptain` в `azureEngagement`.

Перед миграцией:

	window.capptain = {
	  appId: ...,
	  [...]
	};

После миграции:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

Теперь строка подключения для приложения отображается на портале Azure.

### Интерфейсы API JavaScript

Глобальный объект JavaScript `window.capptain` был переименован в `window.azureEngagement`, но можно использовать псевдоним `window.engagement` для вызовов API (псевдоним нельзя использовать для определения конфигурации пакета SDK).

Например: `capptain.deviceId` становится `engagement.deviceId`, `capptain.agent.startActivity` становится `engagement.agent.startActivity` и т. д.

<!---HONumber=AcomDC_0615_2016-->