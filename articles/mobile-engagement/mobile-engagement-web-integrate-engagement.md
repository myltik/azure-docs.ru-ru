<properties
	pageTitle="Интеграция веб-пакета SDK для Azure Mobile Engagement | Microsoft Azure"
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
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Как интегрировать Engagement в веб-приложение

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Эта процедура описывает самый простой способ активации функций аналитики и мониторинга службы Engagement в веб-приложении.

Следующих действий достаточно для активации отчета журналов, который необходим при вычислении всех статистических данных о пользователях, сеансах, действиях сбоях и технических проблемах. Отчеты по журналам, необходимые для вычисления других статистических данных (например, касающихся событий, ошибок и заданий), требуется создавать вручную с помощью API Engagement (см. статью [Как использовать API Engagement в веб-приложении](mobile-engagement-web-use-engagement-api.md), так как эти статистические данные зависят от приложения.

## Введение

Скачайте веб-пакет SDK [отсюда](http://aka.ms/P7b453). Пакет SDK поставляется как отдельный файл JavaScript с именем **azure-engagement.js**, который необходимо включить в каждую страницу сайта или веб-приложения.

Этот сценарий **НЕОБХОДИМО** загружать **ПОСЛЕ** фрагмента сценария или кода, который необходимо создать, чтобы настроить Engagement для приложения.

## Совместимость с браузерами

Веб-пакет SDK для Engagement использует собственное кодирование и декодирование JSON и междоменные запросы AJAX (на основе спецификации W3C CORS).

* Edge 12 или более поздней версии.
* IE 10 или более поздней версии.
* Firefox 3.5 или более поздней версии.
* Chrome 4 или более поздней версии.
* Safari 6 или более поздней версии.
* Opera 12 или более поздней версии.

## Настройка Engagement

Создайте сценарий, который создает глобальный объект JavaScript **azureEngagement** следующим образом.
 
Так как ваш сайт может содержать несколько страниц, в данном примере предполагается, что этот сценарий также включен в каждую страницу. Назовем его `azure-engagement-conf.js` в этой процедуре.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

`connectionString` для приложения отображается на портале Azure.

> [AZURE.NOTE] `appVersionName` и `appVersionCode` являются необязательными. Рекомендуется их настроить, чтобы в данных аналитики учитывались сведения о версии.

## Добавление сценариев Engagement в страницы

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

Или

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

После загрузки сценарий пакета SDK создает псевдоним **engagement** для доступа к интерфейсам API пакета SDK (его нельзя использовать для определения конфигурации пакета SDK). Этот псевдоним будет использоваться в данной документации в справочных целях.

Обратите внимание, что если псевдоним по умолчанию конфликтует с другой глобальной переменной страницы, то вы можете переопределить его в конфигурации перед загрузкой пакета SDK следующим образом.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## Упрощенные отчеты

### Отслеживание сеансов

Сеанс Engagement состоит из последовательности действий, определяемых по имени.

На классическом веб-сайте рекомендуется объявлять разные действия на разных страницах сайта. На веб-сайте или в веб-приложении, которое никогда изменяет текущую страницу, может потребоваться отслеживать действия более точным способом.

В любом случае, чтобы запустить или изменить текущее действие пользователя, вызовите функцию `engagement.agent.startActivity`, как показано в этом примере.

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

Открытый сеанс будет автоматически завершен сервером Engagement через три минуты после закрытия страницы приложения.

В качестве альтернативы можно также завершить сеанс вручную, вызвав `engagement.agent.endActivity`. Это позволит перевести текущее действие пользователя в состояние "неактивно" и фактически завершить сеанс через 10 секунд, если только в этот период новый вызов `engagement.agent.startActivity` не возобновит сеанс.
 
Эту задержку в 10 секунд можно настроить в глобальном объекте **engagement**.

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] `engagement.agent.endActivity` нельзя использовать в обратном вызове `onunload`, так как на этом этапе вызовы AJAX невозможны.

## Расширенные отчеты

Кроме того, если в отчете не требуются сообщения о `events`, `errors` и `jobs` для конкретного приложения, нужно использовать API Engagement посредством объекта `engagement.agent`.

API Engagement позволяет использовать все расширенные возможности Engagement. Это подробно описано в разделе [Как использовать API Engagement в веб-приложении](mobile-engagement-web-use-engagement-api.md).

## Настройка URL-адресов, используемых для вызовов AJAX

Вы можете настроить URL-адреса, используемые пакетом SDK. Например, чтобы переопределить URL-адрес журнала (конечную точку пакета SDK для ведения журнала), можно переопределить конфигурацию следующим образом.

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

Если ваши функции URL-адреса возвращают строку, начинающуюся с `/`, `//`, `http://` или `https://`, то схема по умолчанию не используется.

По умолчанию для этих URL-адресов используется схема `https://`. Если вы хотите настроить схему по умолчанию, то переопределите конфигурацию следующим образом.

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->