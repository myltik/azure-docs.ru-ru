---
title: Процедуры обновления веб-пакета SDK для Служб мобильного взаимодействия Azure | Документация Майкрософт
description: Последние обновления и процедуры для веб-пакета SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 42f78b5e6bfa028f3a798f6cb6c0dee3f17b3ebe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Процедуры обновления веб-пакета SDK для Служб мобильного взаимодействия Azure
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Если вы уже интегрировали в веб-приложение предыдущую версию веб-пакета SDK для Служб мобильного взаимодействия Azure, то при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий веб-пакета SDK для Служб мобильного взаимодействия Azure, то в процессе обновления может потребоваться выполнить несколько процедур. Например, при миграции с версии 1.4.0 на 1.6.0 сначала выполните обновление с версии 1.4.0 до 1.5.0. Затем выполните процедуры обновления с версии 1.5.0 до 1.6.0.

Независимо от того, с какой версии выполняется обновление, необходимо заменить предыдущую версию файла azure-engagement.js его последней версией.

## <a name="upgrade-from-121-to-200"></a>Обновление с версии 1.2.1 до 2.0.0
В этом разделе описан процесс переноса веб-пакета SDK для Служб мобильного взаимодействия из службы Capptain от Capptain SAS в приложение Служб мобильного взаимодействия Azure. При миграции с более ранней версии сначала ознакомьтесь с информацией о переносе на версию 1.2.1 на веб-сайте Capptain, а затем выполните описанные ниже процедуры.

Эта версия веб-пакета SDK для Служб мобильного взаимодействия не поддерживает функции Samsung Smart TV, Opera TV, webOS и Reach.

> [!IMPORTANT]
> Службы Capptain и Службы мобильного взаимодействия Azure отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе веб-пакета SDK для Служб мобильного взаимодействия в приложение данные не будут перенесены с сервера Capptain на сервер Служб мобильного взаимодействия.
> 
> 

### <a name="javascript-files"></a>Файлы JavaScript
Замените файл capptain-sdk.js файлом azure-engagement.js, а затем обновите соответствующим образом импортируемые элементы в сценарии.

### <a name="remove-capptain-reach"></a>Удаление модуля Capptain Reach
Эта версия веб-пакета SDK для Служб мобильного взаимодействия не поддерживает функцию Reach. Если вы интегрировали модуль Capptain Reach в приложение, то его необходимо удалить.

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

### <a name="remove-deprecated-apis"></a>Удаление нерекомендуемых интерфейсов API
Некоторые интерфейсы API из Capptain являются нерекомендуемыми в веб-пакете SDK для Служб мобильного взаимодействия.

Удалите все вызовы к следующим API: `agent.connect`, `agent.disconnect`, `agent.pause` и `agent.sendMessageToDevice`.

Удалите все экземпляры следующих обратных вызовов из конфигурации Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` и `onPushMessageReceived`.

### <a name="configuration"></a>Параметр Configuration
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

### <a name="javascript-apis"></a>Интерфейсы API JavaScript
Глобальный объект JavaScript `window.capptain` был переименован в `window.azureEngagement`, но можно использовать псевдоним `window.engagement` для вызовов API. Этот псевдоним нельзя использовать для определения конфигурации пакета SDK.

Например: `capptain.deviceId` становится `engagement.deviceId`, `capptain.agent.startActivity` становится `engagement.agent.startActivity` и т. д.

