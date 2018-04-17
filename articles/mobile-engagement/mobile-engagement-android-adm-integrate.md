---
title: Интеграция пакета Android SDK для Служб мобильного взаимодействия Azure
description: Последние обновления и процедуры пакета Android SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4c3b8f60333e6096411aad8499bb4bfc36e53f3c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-adm-with-engagement"></a>Интеграция ADM с платформой Engagement
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!IMPORTANT]
> Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе "Интеграция Engagement на платформе Android".
> 
> Этот документ пригоден только в том случае, если вы уже встроили модуль обработки рекламных кампаний и план для отправки данных на устройства Amazon. Для интеграции кампаний, обработанных модулем, в приложение необходимо сначала ознакомиться с разделом «Интеграция модуля обработки рекламных кампаний платформы Engagement для Android».
> 
> 

## <a name="introduction"></a>Введение
Благодаря интеграции ADM можно передавать ваше приложение при помощи push-технологий на устройства Android, созданные Amazon.

Полезные данные ADM, перемещаемые при помощи push-технологии в пакете SDK, всегда содержат ключ `azme` в объекте данных. Таким образом, если вы в своем приложении используете ADM для другой цели, вы можете фильтровать push-передачи на основе этого ключа.

> [!IMPORTANT]
> Только устройства Amazon Kindle под управлением Android 4.0.3 или более поздней версии поддерживаются службой Amazon Device Messaging (ADM). Тем не менее, этот код можно безопасно интегрировать на других устройствах.
> 
> 

## <a name="sign-up-to-adm"></a>Подписка на ADM
Если это еще не сделано, необходимо включить ADM в учетной записи Amazon.

Процедура подробно описана здесь: [<https://developer.amazon.com/sdk/adm/credentials.html>].

После завершения процедуры вы получаете в свое распоряжение:

* Учетные данные OAuth (идентификатор и секрет клиента) для платформы Engagement, чтобы иметь возможность направлять push-уведомления своим устройствам.
* Ключ API, который должен быть интегрирован в приложение.

## <a name="sdk-integration"></a>Интеграция пакета SDK
### <a name="managing-device-registrations"></a>Управление регистрацией устройств
Каждое устройство должно отправлять команду регистрации серверам ADM, в противном случае с ними невозможно связаться.

Если вы уже используете [клиентскую библиотеку ADM] и [интегрировали ADM], можно переходить прямо к команде android-sdk-adm-receive.

Если интеграция ADM еще не выполнена, платформа Engagement предлагает более простой путь для ее включения в приложении.

Отредактируйте файл `AndroidManifest.xml` :

* Добавьте пространство имен Amazon, файл должен начинаться следующим образом:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Внутри тега `<application/>` добавьте этот раздел:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* После добавление тега Amazon может возникнуть ошибка построения, если конечная сборка проекта ниже версии Android 2.1. Необходимо использовать конечную сборку **версии Android 2.1 или выше** (не беспокойтесь, значение `minSdkVersion` может быть равным 4).
* Интегрируйте ключ API ADM как ресурс, выполнив [следующую процедуру].

Затем выполните указания следующих разделов.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Передайте идентификатор регистрации службе Push-уведомлений платформы Engagement и начните получать уведомления.
Чтобы передать идентификатор регистрации устройства службе push-уведомлений Engagement и получать ее уведомления, необходимо добавить следующий код в файл `AndroidManifest.xml` внутри тега `<application/>` (даже в случае использования ADM без платформы Engagement):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Убедитесь в наличии следующих разрешений в `AndroidManifest.xml` (перед тегом `</application>`).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Предоставление учетных данных OAuth для платформы Engagement
Отправьте учетные данные OAuth (идентификатор и секрет клиента) на портал Engagement.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[клиентскую библиотеку ADM]:https://developer.amazon.com/sdk/adm/setup.html
[интегрировали ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[следующую процедуру]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
