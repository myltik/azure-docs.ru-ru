---
title: "Общие сведения о пакете SDK для Windows Phone Silverlight"
description: "Общие сведения о пакете SDK для Windows Phone Silverlight для Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 105335b3c4c6e805f9398fbc002d5c4051acd147


---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Общие сведения о пакете SDK для Windows Phone Silverlight для Azure Mobile Engagement
Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в приложение Windows Phone Silverlight. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-windows-phone-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Процедуры по интеграции
1. Начните с этого раздела: [Как интегрировать Mobile Engagement в приложение Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в приложение Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Заметки о выпуске
### <a name="330-04192016"></a>3.3.0 (19.04.2016)
Входит в пакет NuGet *MicrosoftAzure.MobileEngagement***версии 3.4.0**

* Добавлен API "TestLogLevel" для включения, отключения или фильтрации журналов консоли, созданных с помощью пакета SDK.

Сведения о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-windows-phone-release-notes.md).

## <a name="upgrade-procedures"></a>Процедуры обновления
Если вы уже интегрировали в приложение старую версию пакета SDK, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить несколько процедур. Посмотрите полные [процедуры обновления](mobile-engagement-windows-phone-upgrade-procedure.md). Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции «с 0.9.0 в 0.10.1», а затем процедуру миграции «с 0.10.1 в 0.11.0».

### <a name="from-200-to-330"></a>С 2.0.0 в 3.3.0
#### <a name="test-logs"></a>Журналы тестирования
Теперь журналы консоли, созданные с помощью пакета SDK, можно включать, отключать или фильтровать. Чтобы настроить это действие, обновите свойство `EngagementAgent.Instance.TestLogEnabled` до одного из значений, доступных в перечислении `EngagementTestLogLevel`, например:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Обновление предыдущих версий
См. статью [Процедуры обновления пакета SDK для Windows Phone Silverlight](mobile-engagement-windows-phone-upgrade-procedure.md).




<!--HONumber=Nov16_HO3-->


