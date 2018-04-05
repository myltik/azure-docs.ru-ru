---
title: Общие сведения о пакете SDK для Windows Phone Silverlight для Служб мобильного взаимодействия Azure | Документация Майкрософт
description: Общие сведения о пакете SDK для Windows Phone Silverlight для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: fe4df1f914e7b53f24a9855d5f96ecb193986b7f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Общие сведения о пакете SDK для Windows Phone Silverlight для Служб мобильного взаимодействия Azure
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Начните с этой статьи, чтобы получить подробную информацию об интеграции Служб мобильного взаимодействия Azure в приложение Windows Phone Silverlight. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-windows-phone-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Процедуры по интеграции
1. Начните с этого раздела: [Как интегрировать Службы мобильного взаимодействия в приложение Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в приложение Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Служб мобильного взаимодействия в приложении Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Заметки о выпуске
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
Входит в пакет NuGet *MicrosoftAzure.MobileEngagement***версии 3.4.1**

* Улучшение стабильности.

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
См. статью [Процедуры обновления SDK универсальных приложений для Windows](mobile-engagement-windows-phone-upgrade-procedure.md).

