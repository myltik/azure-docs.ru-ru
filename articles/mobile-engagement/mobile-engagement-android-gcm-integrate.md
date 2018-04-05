---
title: Интеграция пакета Android SDK для Служб мобильного взаимодействия Azure
description: Последние обновления и процедуры пакета Android SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 21f9c1007c6b7dabc625555c66f267b2b7c579f8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>Интеграция GCM с помощью Служб мобильного взаимодействия
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!IMPORTANT]
> Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе "Интеграция Engagement на платформе Android".
> 
> Этот документ пригоден только в том случае, если вы уже встроили модуль обработки рекламных кампаний и план для отправки данных на устройства Google Play. Для интеграции кампаний, обработанных модулем, в приложение необходимо сначала ознакомиться с разделом «Интеграция модуля обработки рекламных кампаний платформы Engagement для Android».
> 
> 

## <a name="introduction"></a>Введение
Интеграция GCM позволяет приложению получать push-уведомления.

Полезные данные GCM, перемещаемые при помощи push-технологии в SDK, всегда содержат ключ `azme` в объекте данных. Таким образом, если вы в своем приложении используете GCM для другой цели, вы можете фильтровать push-передачи в зависимости от этого ключа.

> [!IMPORTANT]
> С помощью GCM отправлять push-уведомления можно только на устройства под управлением Android 2.2 или выше с установленным Google Play, а также включенным фоновым подключением Google. Тем не менее, этот код можно безопасно интегрировать и на неподдерживаемых  устройствах (он использует только намерения).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Создание проекта Google Cloud Messaging с ключом API
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>Интеграция пакета SDK
### <a name="managing-device-registrations"></a>Управление регистрацией устройств
Каждое устройство должно отправлять команду регистрации серверам Google, в противном случае с ними невозможно связаться.

Устройство также может отменить регистрацию на получение уведомлений GCM (отмена регистрации устройства выполняется автоматически при удалении приложения).

Если вы не используете [пакет SDK для Google Play] или еще не отправили намерение регистрации, можно сделать так, чтобы платформа Engagement выполняла регистрацию устройств автоматически.

Для этого добавьте следующий код в файл `AndroidManifest.xml` внутри тега `<application/>`:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Передайте идентификатор регистрации службе Push-уведомлений платформы Engagement и начните получать уведомления.
Чтобы передать идентификатор регистрации устройства службе push-уведомлений Engagement и получать ее уведомления, необходимо добавить следующий код в файл `AndroidManifest.xml` внутри тега `<application/>` (даже если вы управляете регистрацией устройств самостоятельно):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Убедитесь в наличии следующих разрешений в `AndroidManifest.xml` (после тега `</application>`).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Предоставление Службам мобильного взаимодействия доступа к ключу API GCM
Следуйте [этому руководству](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key), чтобы предоставить Службам мобильного взаимодействия доступ к вашему ключу API GCM.

[пакет SDK для Google Play]:https://developers.google.com/cloud-messaging/android/start
