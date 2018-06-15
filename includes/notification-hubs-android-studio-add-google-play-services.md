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
ms.openlocfilehash: 540c9775ae56798ce2d2d87fed4924244c31412f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33835786"
---
1. Откройте диспетчер пакетов SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** > **Android** > **SDK Manager** (Диспетчер пакетов SDK) в меню. Найдите нужную версию пакета SDK для Android, которая используется в проекте. Откройте ее, щелкнув **Show Package Details** (Показать окно свойств пакета), и выберите **Google APIs** (API-интерфейсы Google), если они еще не установлены.
2. Щелкните вкладку **Пакет инструментов SDK**. Если сервисы Google Play еще не установлены, щелкните **Google Play Services** (Сервисы Google Play), как показано ниже. Нажмите кнопку **Применить**, чтобы начать установку. 
   
    Запишите путь к пакету SDK. Он вам потребуется в дальнейшем. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Откройте файл **build.gradle** в каталоге приложения.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Добавьте следующую строку в *зависимости*: 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Щелкните значок **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle) на панели инструментов.
6. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application* .
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

