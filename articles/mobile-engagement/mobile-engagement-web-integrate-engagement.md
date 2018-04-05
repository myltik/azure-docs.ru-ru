---
title: Интеграция веб-пакета SDK для Служб мобильного взаимодействия Azure | Документация Майкрософт
description: Последние обновления и процедуры для веб-пакета SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: bccfbdfe9d99900f58d5dbfa44183146c79b9c88
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Интегрирование Служб мобильного взаимодействия Azure в веб-приложение
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

В этой статье описана самая простая процедура активации функций аналитики и мониторинга Служб мобильного взаимодействия Azure в веб-приложении.

Выполните следующие шаги, чтобы активировать отчеты по журналам, которые необходимы для вычисления всех статистических данных, касающихся пользователей, сеансов, действий, сбоев и технической информации. Для статистики, зависящей от приложения, такой как события, ошибки или задания, необходимо вручную активировать отчеты по журналам, используя API Служб мобильного взаимодействия Azure. Дополнительно узнайте, [как использовать API для расширенного добавления тегов Служб мобильного взаимодействия в веб-приложении](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Введение
[Скачайте веб-пакет SDK для Служб мобильного взаимодействия Azure](http://aka.ms/P7b453).
Веб-пакет SDK для Служб мобильного взаимодействия поставляется как отдельный файл JavaScript с именем azure-engagement.js, который необходимо включить в каждую страницу сайта или веб-приложения.

> [!IMPORTANT]
> Этот сценарий необходимо выполнить после выполнения фрагмента сценария или кода, который вы создаете, чтобы настроить Службы мобильного взаимодействия для приложения.
> 
> 

## <a name="browser-compatibility"></a>Совместимость с браузерами
Веб-пакет SDK для Служб мобильного взаимодействия использует собственное кодирование и декодирование JSON, помимо междоменных запросов AJAX (на основе спецификации W3C CORS). Он совместим со следующими браузерами:

* Microsoft Edge 12 или более поздней версии.
* Internet Explorer 10 или более поздней версии.
* Firefox 3.5 или более поздней версии.
* Chrome 4 или более поздней версии.
* Safari 6 или более поздней версии.
* Opera 12 или более поздней версии.

## <a name="configure-mobile-engagement"></a>Настройка Служб мобильного взаимодействия
Создайте сценарий, который создает глобальный объект JavaScript `azureEngagement` , как показано в следующем примере. Так как сайт может иметь несколько страниц, в примере предполагается, что этот сценарий включен в каждую страницу. В этом примере объект JavaScript имеет имя `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

Значение `connectionString` для приложения отображается на портале Azure.

> [!NOTE]
> Параметры `appVersionName` и `appVersionCode` являются необязательными. Однако рекомендуется их также настроить, чтобы аналитика могла обрабатывать сведения о версии.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Добавление сценариев Служб мобильного взаимодействия в страницы
Добавьте сценарии Служб мобильного взаимодействия в свои страницы одним из следующих способов:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

или

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
После загрузки сценарий веб-пакета SDK для Служб мобильного взаимодействия создает псевдоним **engagement** для доступа к интерфейсам API пакета SDK. Этот псевдоним нельзя использовать для определения конфигурации пакета SDK. В данной документации он используется в справочных целях.

Обратите внимание, что если псевдоним по умолчанию конфликтует с другой глобальной переменной страницы, то вы можете переопределить его в конфигурации перед загрузкой веб-пакета SDK для Служб мобильного взаимодействия. Это делается следующим образом:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Упрощенные отчеты
Базовые отчеты в Службах мобильного взаимодействия охватывают статистику на уровне сеанса, такую как данные о пользователях, сеансах, действиях и сбоях.

### <a name="session-tracking"></a>Отслеживание сеансов
Сеанс Служб мобильного взаимодействия состоит из последовательности действий, каждое из которых определяется по имени.

На классическом веб-сайте рекомендуется объявлять разные действия на разных страницах сайта. На веб-сайте или в веб-приложении, которое никогда не изменяет текущую страницу, может потребоваться отслеживать действия более точным способом, например в пределах страницы.

В любом случае, чтобы запустить или изменить текущее действие пользователя, вызовите функцию `engagement.agent.startActivity` . Например: 

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Сервер Служб мобильного взаимодействия автоматически завершает открытый сеанс через три минуты после закрытия страницы приложения.

Также можно завершить сеанс вручную, вызвав `engagement.agent.endActivity`. При этом текущее действие пользователя переводится в состояние "Неактивно".  Сеанс будет завершен через 10 секунд, если только в этот период его не возобновит новый вызов `engagement.agent.startActivity` .

Эту задержку в 10 секунд можно настроить в глобальном объекте engagement:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> `engagement.agent.endActivity` нельзя использовать в обратном вызове `onunload`, так как на этом этапе вызовы AJAX невозможны.
> 
> 

## <a name="advanced-reporting"></a>Расширенные отчеты
Если вы хотите получать отчеты о событиях, ошибках и заданиях, относящихся к конкретному приложению, следует использовать API Служб мобильного взаимодействия. Доступ к API Служб мобильного взаимодействия можно получить посредством объекта `engagement.agent`.

API Служб мобильного взаимодействия позволяет использовать все расширенные возможности этой службы. Дополнительные сведения об API см. в статье [Использование API Служб мобильного взаимодействия Azure в веб-приложении](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Настройка URL-адресов, используемых для вызовов AJAX
Вы можете настроить URL-адреса, используемые веб-пакетом SDK для Служб мобильного взаимодействия. Например, чтобы переопределить URL-адрес журнала (конечную точку пакета SDK для ведения журнала), можно переопределить конфигурацию следующим образом:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Если ваши функции URL-адреса возвращают строку, начинающуюся с `/`, `//`, `http://` или `https://`, то схема по умолчанию не используется. По умолчанию для этих URL-адресов используется схема `https://` . Если вы хотите настроить схему по умолчанию, то переопределите конфигурацию следующим образом:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
