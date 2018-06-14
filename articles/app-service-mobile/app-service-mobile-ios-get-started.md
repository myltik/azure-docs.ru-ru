---
title: Создание мобильного приложения для iOS в службе мобильных приложений Azure | Документация Майкрософт
description: Изучите этот учебник, чтобы начать работу с серверным частями мобильных приложений Azure для разработки приложений iOS на Objective-C или Swift
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 970ad90aa14d5778e8b232e32aa1dacf32789988
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595320"
---
# <a name="create-an-ios-app"></a>Создание приложения iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как добавить облачную серверную службу [мобильных приложений Azure](app-service-mobile-value-prop.md)в приложение iOS. Сначала мы создадим новую серверную часть мобильного приложения. Затем мы воспользуемся простым приложением iOS *Список дел* для хранения данных в Azure.

Для работы с этим учебником требуется [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Шаг 1. Создание серверной части мобильного приложения Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Шаг 2. Настройка проекта серверной части
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Шаг 3. Скачивание и запуск приложения iOS
[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
