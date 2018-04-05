---
title: Интеграция пакета SDK для Служб мобильного взаимодействия Azure с Android
description: Описывает, как интегрировать пакет SDK для Служб мобильного взаимодействия Azure в приложения Android.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: d02bcfc7521df9c18edc534e679e597ad2328e6f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Интеграция пакета SDK для Служб мобильного взаимодействия Azure с Android
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Универсальная платформа Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

В этом документе описываются все параметры интеграции и конфигурации в пакете SDK для Android для Служб мобильного взаимодействия Azure.

## <a name="prerequisites"></a>предварительным требованиям
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

