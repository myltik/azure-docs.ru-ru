---
title: Обзор веб-пакета SDK для Azure Mobile Engagement | Microsoft Docs
description: Последние обновления и процедуры для веб-пакета SDK для Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo

---
# Веб-пакет SDK для Azure Mobile Engagement
Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в веб-приложение. Если вы хотите поэкспериментировать, прежде чем интегрировать свое веб-приложение, ознакомьтесь с нашим [15-минутным руководством](mobile-engagement-web-app-get-started.md).

## Процедуры по интеграции
1. Узнайте, [как интегрировать Mobile Engagement в свое веб-приложение](mobile-engagement-web-integrate-engagement.md).
2. Реализация плана добавления тегов описана в статье [Как использовать API Engagement в веб-приложении](mobile-engagement-web-use-engagement-api.md).

## Заметки о выпуске
### 2\.0.1 (10.06.2016)
* веб-пакет SDK для Mobile Engagement отключен в Internet Explorer 8 и Internet Explorer 9;
* исправлено обнаружение веб-браузера Opera.

Информацию о всех версиях см. в [полной версии заметок о выпуске](mobile-engagement-web-release-notes.md).

## Процедуры обновления
### Обновление с версии 1.2.1 до 2.0.0
В этих разделах описан процесс переноса веб-пакета SDK для Mobile Engagement из службы Capptain от Capptain SAS в приложение Azure Mobile Engagement. При переходе с версии, предшествующей 1.2.1, сначала ознакомьтесь с информацией о переходе на версию 1.2.1 на веб-сайте Capptain, а затем выполните следующие процедуры.

Эта версия веб-пакета SDK для Mobile Engagement не поддерживает функции Samsung Smart TV, Opera TV, webOS и Reach.

> [!IMPORTANT]
> Службы Capptain и Azure Mobile Engagement отличаются друг от друга. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе веб-пакета SDK для Mobile Engagement в приложение данные не будут перенесены с сервера Capptain на сервер Mobile Engagement.
> 
> 

#### Файлы JavaScript
Замените файл capptain-sdk.js файлом azure-engagement.js, а затем обновите соответствующим образом импортируемые элементы в сценарии.

#### Удаление модуля Capptain Reach
Эта версия веб-пакета SDK для Mobile Engagement не поддерживает функцию Reach. Если вы интегрировали модуль Capptain Reach в приложение, его необходимо удалить.

Удалите импортированный код CSS Reach со страницы, также удалите связанный CSS-файл (по умолчанию: capptain-reach.css).

Удалите следующие ресурсы Reach: изображение для закрытия (по умолчанию: capptain-close.png) и фирменный значок (по умолчанию: capptain-notification-icon).

Удалите пользовательский интерфейс Reach для получения уведомлений в приложении. Макет по умолчанию выглядит следующим образом:

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

Удалите пользовательский интерфейс Reach для текстовых и веб-объявлений и опросов. Макет по умолчанию выглядит следующим образом:

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

Удалите объект `reach` из конфигурации, если таковой имеется. Это выглядит следующим образом.

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Удалите все прочие настройки Reach, например категории.

#### Удаление нерекомендуемых интерфейсов API
Некоторые интерфейсы API из Capptain являются нерекомендуемыми в веб-пакете SDK для Mobile Engagement.

Удалите все вызовы к следующим API: `agent.connect`, `agent.disconnect`, `agent.pause` и `agent.sendMessageToDevice`.

Удалите все следующие обратные вызовы из конфигурации Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` и `onPushMessageReceived`.

#### Конфигурация
Служба Mobile Engagement использует строку подключения для настройки идентификаторов пакета SDK, таких как идентификатор приложения.

Замените идентификатор приложения строкой подключения. Обратите внимание, что глобальный объект для настройки пакета SDK меняется с `capptain` на `azureEngagement`.

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

#### Интерфейсы API JavaScript
Глобальный объект JavaScript `window.capptain` был переименован в `window.azureEngagement`, но можно использовать псевдоним `window.engagement` для вызовов API. Этот псевдоним нельзя использовать для определения конфигурации пакета SDK.

Например: `capptain.deviceId` становится `engagement.deviceId`, `capptain.agent.startActivity` становится `engagement.agent.startActivity` и т. д.

Если вы уже интегрировали в свое приложение предыдущую версию веб-пакета SDK для Azure Mobile Engagement, ознакомьтесь с [процедурами обновления](mobile-engagement-web-upgrade-procedure.md).

<!---HONumber=AcomDC_0713_2016-->