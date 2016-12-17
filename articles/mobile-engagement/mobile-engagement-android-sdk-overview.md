---
title: "Интеграция пакета Android SDK с Azure Mobile Engagement"
description: "Описывает, как интегрировать пакет SDK для Azure Mobile Engagement в приложения Android."
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
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b89a13230439698854656f0bb5367f1ed35ce1c


---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Интеграция пакета Android SDK с Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Универсальная платформа Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

В этом документе описываются все параметры интеграции и конфигурации в пакете Android SDK для Azure Mobile Engagement.

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

1. Google Cloud Messaging (GCM): [Интеграция GCM с помощью службы Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [Интеграция ADM с помощью службы Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Реализация плана добавления тегов:
[Использование расширенного API добавления тегов службы Mobile Engagement в приложении Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Заметки о выпуске
### <a name="423-08102016"></a>4.2.3 (10.08.2016)
* Больше не блокируется сеть Wi-Fi.
* Устранена взаимоблокировка при вызове getDeviceId перед инициализацией (ошибка появилась в версии 4.2.0).

Информацию о всех версиях см. в [полной версии заметок о выпуске](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Процедуры обновления
Если вы уже интегрировали более старую версию нашего пакета SDK в приложение, ознакомьтесь с разделом [Процедуры обновления](mobile-engagement-android-upgrade-procedure.md).




<!--HONumber=Nov16_HO3-->


