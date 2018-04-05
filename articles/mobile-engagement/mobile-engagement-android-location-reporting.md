---
title: Создание отчетов о расположении для пакета SDK для Android в Службах мобильного взаимодействия
description: Сведения о создании отчетов о расположении для пакета SDK для Android в Службах мобильного взаимодействия Azure.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 7978010bd92af18e3ab7c8ccab8dc682d7ef18df
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Создание отчетов о расположении для пакета SDK для Android в Службах мобильного взаимодействия
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

В этой статье описывается, как создавать отчеты о расположении для приложения Android.

## <a name="prerequisites"></a>предварительным требованиям
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Отчеты о расположении
Для того чтобы создавались отчеты о расположениях, необходимо добавить несколько строк конфигурации (между тегами `<application>` и `</application>`).

### <a name="lazy-area-location-reporting"></a>Отчеты о расположении отложенной области
Отчет о приблизительном местоположении устройств позволяют включать в отчеты страну, область или регион и населенный пункт, связанные с устройствами. Этот тип отчетов о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Отчеты об области устройства выполняются максимум один раз за сеанс. GPS никогда не используется, поэтому этот тип отчета о расположении оказывает исключительно небольшое воздействие на батарею.

Области или регионы в отчетах используются для вычисления географических статистических данных о пользователях, сеансах, событиях и ошибках. Они также могут использоваться в качестве критерия для рекламных компаний Reach.

Чтобы включить отчет о приблизительном местоположении устройств, можно использовать конфигурацию, упомянутую ранее, в приведенной ниже процедуре.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Необходимо также указать разрешение для расположения. В данном коде используется разрешение ``COARSE`` .

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Вместо него можно использовать ``ACCESS_FINE_LOCATION`` , если это требуется приложению.

### <a name="real-time-location-reporting"></a>Отчет о расположении в реальном времени
Отчет о расположении в реальном времени позволяет включать в отчет широту и долготу, связанные с устройствами. По умолчанию этот тип отчетов о расположении использует только сетевые расположения (на основе идентификатора соты или Wi-Fi). Отчеты активны только тогда, когда приложение выполняется в фоновом режиме (например, во время сеанса).

Расположения в реальном времени *НЕ* используются для вычисления статистических данных. Единственное их назначение — дать возможность использовать критерий геозоны реального времени \<Reach-Audience-geofencing\> в рекламных кампаниях.

Чтобы включить функцию отчетов о расположении в реальном времени, добавьте строку кода туда, где указана строка подключения Engagement в действии запуска. Результат имеет следующий вид.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Отчеты на базе GPS
По умолчанию отчеты о расположении в реальном времени используют только сетевые расположения. Чтобы включить использование расположений на основе GPS (которые значительно точнее), используйте следующий объект конфигурации.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Также необходимо добавить следующее разрешение, если оно отсутствует:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Отчет в фоновом режиме
По умолчанию отчеты о расположении в реальном времени активны только тогда, когда приложение выполняется в фоновом режиме (например, во время сеанса). Чтобы включить отчеты в фоновом режиме, используйте следующий объект конфигурации:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Когда приложение выполняется в фоновом режиме, в отчете показываются только расположения на основе сети, даже если включена функция GPS.
> 
> 

Если пользователь перезагрузит устройство, то создание отчетов о расположении в фоновом режиме будет остановлено. Чтобы оно автоматически перезапустилось после перезагрузки, добавьте приведенный ниже код.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Также необходимо добавить следующее разрешение, если оно отсутствует:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Разрешения в Android M
Начиная с Android M, управление некоторыми разрешениями осуществляется в среде выполнения и требует согласия пользователя.

Если планируется использовать API Android уровня 23, то разрешения среды выполнения будут по умолчанию отключены для новых установленных приложений. В противном случае они будут включены по умолчанию.

Вы можете включить или отключить эти разрешения в меню параметров устройства. Отключение разрешений в системном меню приводит к завершению фоновых процессов приложения. Такое поведение системы не влияет на возможность получения push-уведомлений в фоновом режиме.

В контексте отчетов о расположении Служб мобильного взаимодействия утверждения в среде выполнения требуют следующие разрешения.

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Разрешения для пользователя следует запрашивать с помощью стандартного диалогового окна системы. Укажите ``EngagementAgent`` , что если пользователь дает разрешение, то это нужно учесть в режиме реального времени. В противном случае изменение будет обработано только при следующем запуске приложения пользователем.

Ниже приведен пример кода для использования в действии приложения для запроса разрешений и переадресации результата (если он положительный) в класс ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
