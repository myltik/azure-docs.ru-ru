---
title: Расширенная конфигурация для пакета SDK Android для Служб мобильного взаимодействия Azure
description: Описание дополнительных параметров конфигурации, включая манифест Android, в пакете SDK для Android в Службах мобильного взаимодействия Azure.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 29a7bb7daae59e2034504ce27c9ba66755b11e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Расширенная конфигурация для пакета SDK Android для Служб мобильного взаимодействия Azure
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Универсальная платформа Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

В этой статье описано, как настроить различные параметры конфигурации для приложений Android в Службах мобильного взаимодействия Azure.

## <a name="prerequisites"></a>предварительным требованиям
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Требования к разрешениям
Для некоторых параметров необходимы специальные разрешения, которые здесь перечислены для справки, а также включены в некоторые компоненты. Добавьте эти разрешения в файл AndroidManifest.xml проекта непосредственно перед тегом `<application>` или после него.

После того как вы вставите соответствующее разрешение из таблицы ниже, код должен выглядеть следующим образом.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Разрешение | Применение |
| --- | --- |
| ИНТЕРНЕТ |Обязательный элемент. Базовые отчеты |
| ACCESS_NETWORK_STATE |Обязательный элемент. Базовые отчеты |
| RECEIVE_BOOT_COMPLETED |Обязательный элемент. Отображение центра уведомлений после перезагрузки устройства |
| WAKE_LOCK |(рекомендуется). Включает сбор данных при использовании Wi-Fi или при выключенном экране |
| VIBRATE |Необязательный элемент. Включает вибрацию при получении уведомления |
| DOWNLOAD_WITHOUT_NOTIFICATION |Необязательный элемент. Включает общие уведомления Android |
| WRITE_EXTERNAL_STORAGE |Необязательный элемент. Включает общие уведомления Android |
| ACCESS_COARSE_LOCATION |Необязательный элемент. Включает отчет о расположении в реальном времени |
| ACCESS_FINE_LOCATION |Необязательный элемент. Включает отчет о расположении на основе GPS |

Начиная с Android M [управление некоторыми разрешениями осуществляется в среде выполнения](mobile-engagement-android-location-reporting.md#android-m-permissions).

Если вы уже используете разрешение ``ACCESS_FINE_LOCATION``, вам не нужно использовать ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Варианты настройки манифеста для Android
### <a name="crash-report"></a>Отчет о сбоях
Чтобы отключить отчеты о сбоях, добавьте следующий код (между тегами `<application>` и `</application>`).

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Пороговое значение пакета
По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение очень часто отправляет отчеты журналов, лучше сохранять их в буфер и передавать все вместе через определенные промежутки времени (это называется пакетным режимом). Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`).

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Пороговое значение пакета не должно превышать 30 000 (30 с).

### <a name="session-timeout"></a>Время ожидания сеанса
 Можно завершить действие, нажав клавишу **Домой** или **Назад**, переведя телефон в режим ожидания или перейдя к другому приложению. По умолчанию сеанс завершается через десять секунд после завершения его последнего действия. Это позволяет избежать разбиения сеанса каждый раз, когда пользователь выходит из приложения и вскоре возвращается в него (это может происходить при выборе изображения, проверке уведомления и т. п.). Вам может потребоваться изменить данный параметр. Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`).

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Выключение отчетов журналов
### <a name="using-a-method-call"></a>Использование вызова метода
Если необходимо, чтобы служба Engagement перестала отправлять журналы, можно вызвать:

    EngagementAgent.getInstance(context).setEnabled(false);

Этот вызов является постоянным: он использует файл общих параметров.

Если служба Engagement активна, когда вызывается эта функция, то для остановки службы может потребоваться около 1 минуты. Однако в этом случае служба совсем не будет запускаться при следующем запуске приложения.

Вы можете снова включить журнал отчетов путем вызова той же функции с `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Интеграция в собственное `PreferenceActivity`
Вместо вызова этой функции вы можете интегрировать данный параметр непосредственно в существующее `PreferenceActivity`.

Можно настроить Engagement для использования файла настроек (с нужным режимом) в файле `AndroidManifest.xml` с `application meta-data`:

* Ключ `engagement:agent:settings:name` используется для определения имени общего файла настроек.
* Ключ `engagement:agent:settings:mode` используется для определения режима общего файла настроек. Используйте тот же режим, что и `PreferenceActivity`. Режим должен передаваться в виде числа: при использовании сочетания постоянных флагов в коде необходимо проверить общее значение.

Служба Engagement всегда использует логический ключ `engagement:key` в файле настроек для управления данным параметром.

В следующем примере `AndroidManifest.xml` показаны значения по умолчанию:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Затем можно добавить `CheckBoxPreference` в макет параметров, как показано ниже:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
