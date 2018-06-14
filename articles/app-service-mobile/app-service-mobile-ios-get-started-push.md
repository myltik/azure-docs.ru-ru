---
title: Добавление push-уведомлений в приложение iOS с помощью мобильных приложений Azure
description: Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение iOS.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: 1fd90df3b6935d35834e1f571e80b945716b55ff
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29116676"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Добавление push-уведомлений в приложение iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Обзор
В этом руководстве мы добавим push-уведомления в проект по [быстрому запуску iOS], чтобы при каждом добавлении новой записи на устройство отправлялось push-уведомление.

Если вы не используете скачанный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения см. в руководстве [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Требуется физическое устройство iOS и [участие в программе для разработчиков на платформе Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Настройка центра уведомлений
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Регистрация приложения для работы с push-уведомлениями
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Настройка Azure для отправки push-уведомлений
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Обновление серверной части для отправки push-уведомлений
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Добавление push-уведомлений в приложение
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Тестирование push-уведомлений
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Дополнительные сведения
* Шаблоны обеспечивают гибкость при отправке push-уведомлений локально и между различными платформами. [Использование клиентской библиотеки iOS для мобильных приложений Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) описано, как регистрировать шаблоны.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[быстрому запуску iOS]: app-service-mobile-ios-get-started.md
