---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814702"
---
1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта выберите **Add Firebase to your Android app** (Добавить Firebase в приложение Android). и следуйте инструкциям. Скачайте файл **google services.json**. 

    ![Добавление Firebase в приложение Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. В консоли Firebase щелкните значок шестеренки возле имени проекта. Выберите пункт **Project Settings** (Параметры проекта).

    ![Выбор параметров проекта](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Выберите вкладку **General** (Общие) в окне параметров проекта. Затем скачайте файл **google-services.json**, содержащий ключ API сервера и идентификатор клиента.
5. В окне параметров проекта щелкните вкладку **Cloud Messaging** (Обмен сообщениями в облаке) и скопируйте значение параметра **Legacy server key** (Старый ключ сервера). Это значение будет использоваться для настройки политики доступа концентратора уведомлений.
