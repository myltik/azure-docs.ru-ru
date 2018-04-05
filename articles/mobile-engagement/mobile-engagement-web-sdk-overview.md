---
title: Обзор веб-пакета SDK для Служб мобильного взаимодействия Azure | Документация Майкрософт
description: Последние обновления и процедуры для веб-пакета SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: ad0a83e05c6a60953c2f8a0036f823e7ed497f45
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk"></a>Веб-пакет SDK для Служб мобильного взаимодействия Azure
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Начните с этой статьи, чтобы получить подробную информацию об интеграции Служб мобильного взаимодействия Azure в веб-приложение. Если вы хотите поэкспериментировать, прежде чем интегрировать свое веб-приложение, ознакомьтесь с нашим [15-минутным руководством](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Процедуры по интеграции
1. Узнайте, [как интегрировать Службы мобильного взаимодействия в свое веб-приложение](mobile-engagement-web-integrate-engagement.md).
2. Реализация плана добавления тегов описана в статье [Как использовать API Служб мобильного взаимодействия в веб-приложении](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Заметки о выпуске
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* Фиксированный сбой в режиме закрытого просмотра (Safari).
* Фиксированный сбой в браузерах с отключенными файлами cookie.

Информацию о всех версиях см. в [полной версии заметок о выпуске](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Процедуры обновления
### <a name="upgrade-from-121-to-200"></a>Обновление с версии 1.2.1 до 2.0.0
В этих разделах описан процесс переноса веб-пакета SDK для Служб мобильного взаимодействия из службы Capptain от Capptain SAS в приложение Служб мобильного взаимодействия Azure. При переходе с версии, предшествующей 1.2.1, сначала ознакомьтесь с информацией о переходе на версию 1.2.1 на веб-сайте Capptain, а затем выполните следующие процедуры.

Эта версия веб-пакета SDK для Служб мобильного взаимодействия не поддерживает функции Samsung Smart TV, Opera TV, webOS и Reach.

> [!IMPORTANT]
> Службы Capptain и Службы мобильного взаимодействия Azure отличаются друг от друга. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе веб-пакета SDK для Служб мобильного взаимодействия в приложение данные не будут перенесены с сервера Capptain на сервер Служб мобильного взаимодействия.
> 
> 

#### <a name="javascript-files"></a>Файлы JavaScript
Замените файл capptain-sdk.js файлом azure-engagement.js, а затем обновите соответствующим образом импортируемые элементы в сценарии.

#### <a name="remove-capptain-reach"></a>Удаление модуля Capptain Reach
Эта версия веб-пакета SDK для Служб мобильного взаимодействия не поддерживает функцию Reach. Если вы интегрировали модуль Capptain Reach в приложение, его необходимо удалить.

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

#### <a name="remove-deprecated-apis"></a>Удаление нерекомендуемых интерфейсов API
Некоторые интерфейсы API из Capptain являются нерекомендуемыми в веб-пакете SDK для Служб мобильного взаимодействия.

Удалите все вызовы к следующим API: `agent.connect`, `agent.disconnect`, `agent.pause` и `agent.sendMessageToDevice`.

Удалите все следующие обратные вызовы из конфигурации Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` и `onPushMessageReceived`.

#### <a name="configuration"></a>Параметр Configuration
Службы мобильного взаимодействия используют строку подключения для настройки идентификаторов пакета SDK, таких как идентификатор приложения.

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

#### <a name="javascript-apis"></a>Интерфейсы API JavaScript
Глобальный объект JavaScript `window.capptain` был переименован в `window.azureEngagement`, но можно использовать псевдоним `window.engagement` для вызовов API. Этот псевдоним нельзя использовать для определения конфигурации пакета SDK.

Например: `capptain.deviceId` становится `engagement.deviceId`, `capptain.agent.startActivity` становится `engagement.agent.startActivity` и т. д.

Если вы уже интегрировали в свое приложение предыдущую версию веб-пакета SDK для Служб мобильного взаимодействия Azure, ознакомьтесь с [процедурами обновления](mobile-engagement-web-upgrade-procedure.md).

