---
title: "Содержимое пакета SDK для универсальных приложений для Windows"
description: "Изучите содержимое пакета SDK универсальных приложений для Windows для Служб мобильного взаимодействия Azure"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b28d525ab16487b963772e23fdecd11f94dcabd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-content"></a>Содержимое пакета SDK для универсальных приложений для Windows
В этом документе перечислено и описано содержимое, которое развертывается в приложении с помощью пакета SDK.

## <a name="the-resources-folder"></a>Папка `/Resources`
Эта папка содержит все ресурсы, необходимые для Служб мобильного взаимодействия. Вы также можете настроить их в соответствии с особенностями своего приложения.

* `EngagementConfiguration.xml`: файл конфигурации Служб мобильного взаимодействия, в котором вы можете настроить параметры Служб мобильного взаимодействия (строку подключения Служб мобильного взаимодействия, сообщение о сбое и т. д.).

### <a name="html-folder"></a>Папка /html
* `EngagementNotification.html`: HTML-код веб-представления `Notification` для баннеров в приложении.
* `EngagementAnnouncement.html`: HTML-код веб-представления `Announcement` для представлений в приложении.

### <a name="images-folder"></a>Папка /images
* `EngagementIconNotification.png`: фирменный значок, отображающийся слева от уведомления. Замените его собственным логотипом.
* `EngagementIconOk.png`: значок `Ok` страниц содержимого рекламной кампании для кнопки действия или проверки.
* `EngagementIconNOK.png`: значок `NOK`, используемый, когда кнопка проверки на страницах содержимого рекламных кампаний отключена.
* `EngagementIconClose.png`: значок `Close` для кнопки закрытия уведомлений и содержимого рекламных кампаний.

### <a name="overlay-folder"></a>Папка /overlay
* `EngagementPageOverlay.cs` : страница наложения, ответственная за добавление пользовательского интерфейса Engagement в приложении в дочерний элемент.

