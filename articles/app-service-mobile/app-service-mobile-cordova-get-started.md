---
title: Создание мобильного приложения Cordova в службе мобильных приложений Azure | Документация Майкрософт
description: Изучите этот учебник, чтобы начать работу с серверными частями мобильных приложений Azure для разработки программ Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
tags: ''
keywords: cordova, javascript, мобильный, клиент
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: hero-article
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 223e9e35fcab347f9b5b8db01a9fd667b9f5d55d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595182"
---
# <a name="create-an-apache-cordova-app"></a>Создание приложения Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение для платформы Apache Cordova с помощью серверной части мобильного приложения Azure.  Мы создадим серверную часть мобильного приложения и простое приложение *списка задач* (на платформе Apache Cordova), которое хранит свои данные в Azure.

Выполнение инструкций из этого учебника необходимо для работы с другими учебниками по Apache Cordova, посвященными использованию функции мобильных приложений в службе приложений Azure.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с данным руководством вам потребуется:

* компьютер с [Visual Studio Community 2017] или более поздней версии;
* [набор средств Visual Studio для Apache Cordova];
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).

Можно также обойтись без Visual Studio и использовать командную строку Apache Cordova напрямую.  Эта командная строка пригодится, если вы работаете с данным руководством на компьютере Mac.  Компиляция клиентских приложений Apache Cordova с помощью командной строки в этом учебнике не рассматривается.

## <a name="create-an-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Настройка серверного проекта
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Загрузка и запуск приложения Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Дальнейшие действия
Закончив работу с этим кратким учебником, переходите к одному из следующих руководств:

* [Добавьте автономные данные](app-service-mobile-cordova-get-started-offline-data.md) в приложение Apache Cordova.
* [Добавление аутентификации](app-service-mobile-cordova-get-started-users.md) в приложение Apache Cordova.
* [Добавление push-уведомлений](app-service-mobile-cordova-get-started-push.md) в приложение Apache Cordova.

Дополнительные сведения об основных понятиях, связанных со службой приложений Azure.

* [Автономные данные]
* [Аутентификация]
* [Push-уведомления]

Подробнее об использовании пакетов SDK.

* [Пакет SDK для Apache Cordova]
* [Серверный пакет SDK для ASP.NET]
* [Серверный пакет SDK для Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: http://www.visualstudio.com/
[набор средств Visual Studio для Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Автономные данные]: app-service-mobile-offline-data-sync.md
[Аутентификация]: app-service-mobile-auth.md
[Push-уведомления]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Пакет SDK для Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Серверный пакет SDK для ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Серверный пакет SDK для Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
