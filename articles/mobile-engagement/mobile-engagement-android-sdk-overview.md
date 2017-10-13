---
title: "Интеграция пакета SDK для Служб мобильного взаимодействия Azure с Android"
description: "Описывает, как интегрировать пакет SDK для Служб мобильного взаимодействия Azure в приложения Android."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Интеграция пакета SDK для Служб мобильного взаимодействия Azure с Android
> [!div class="op_single_selector"]
> * [Универсальная платформа Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

В этом документе описываются все параметры интеграции и конфигурации в пакете SDK для Android для Служб мобильного взаимодействия Azure.

## <a name="prerequisites"></a>Предварительные требования
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Дополнительные функции
### <a name="reporting-features"></a>Функции отчетов
Можно добавить такие функции:

1. [дополнительные параметры отчетов;](mobile-engagement-android-advanced-reporting.md)
2. [параметры отчетов о местонахождении;](mobile-engagement-android-location-reporting.md)
3. [дополнительные параметры конфигурации.](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Уведомления:
[Интеграция модуля Reach (уведомления) в приложение Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [Интеграция GCM с помощью Служб мобильного взаимодействия](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [Интеграция ADM с помощью Служб мобильного взаимодействия](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Реализация плана добавления тегов:
[Использование расширенного API добавления тегов Служб мобильного взаимодействия в приложении Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Заметки о выпуске

### <a name="431-07172017"></a>4.3.1 (17.07.2017)
* Исправлена проблема с аварийным завершением работы, которое изредка могло происходить при вызове `EngagementAgentUtils.isInDedicatedEngagementProcess` (также используется классом `EngagementApplication`).

### <a name="430-06272017"></a>4.3.0 (27.06.2017)
* Поддержка Android 8 (предыдущие версии пакета SDK не будут работать с Android 8).
* Удалена зависимость от библиотеки поддержки.
* Удален класс `EngagementFragmentActivity`.
* Из-за [ограничений на фоновое выполнение приложений](https://developer.android.com/preview/features/background.html) в Android 8 журналы в фоновом режиме могут отображаться с задержкой (когда пользователь воспользуется устройством), что, в свою очередь, может повлиять на отображение статистики по **доставленным** push-уведомлениям и **отображенным системным уведомлениям**. Причина — соответствующие данные не поступают, пока устройство находится в спящем режиме. (Это не повлияет на отображение уведомления, подачу устройством звукового сигнала и вибросигнала в реальном времени.)
* Из-за [ограничений на доступ к геолокационным данным в фоновом режиме](https://developer.android.com/preview/features/background-location-limits.html) данные о текущем местонахождении не будут часто обновляться в Android 8.

Информацию о всех версиях см. в [полной версии заметок о выпуске](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Процедуры обновления
Если вы уже интегрировали более старую версию нашего пакета SDK в приложение, ознакомьтесь с разделом [Процедуры обновления](mobile-engagement-android-upgrade-procedure.md).

