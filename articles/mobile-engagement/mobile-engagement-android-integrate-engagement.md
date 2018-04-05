---
title: Интеграция пакета Android SDK для Служб мобильного взаимодействия Azure
description: Последние обновления и процедуры пакета Android SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Интеграция службы Engagement на Android
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Эта процедура описывает самый простой способ активации функции аналитики и мониторинга службы Engagement в приложении Android.

> [!IMPORTANT]
> Минимальный уровень пакета API SDK Android должен быть 10 или выше (Android 2.3.3 ил выше).
> 
> 

Следующих действий достаточно для активации отчета журналов, который необходим при вычислении всех статистических данных о пользователях, сеансах, действиях сбоях и технических проблемах. Отчеты по журналам, необходимые для вычисления других статистических данных (например, касающихся событий, ошибок и заданий), требуется создавать вручную с помощью API Служб мобильного взаимодействия (см. статью [Использование API Служб мобильного взаимодействия в Android](mobile-engagement-android-use-engagement-api.md)), так как эти статистические данные зависят от приложения.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Внедрение пакета SDK Engagement и службы в проект Android
Скачайте пакет Android SDK [отсюда](https://aka.ms/vq9mfn). Поместите `mobile-engagement-VERSION.jar` в папку `libs` вашего проекта Android (создайте папку libs, если она еще не создана).

> [!IMPORTANT]
> При создании пакета приложения с помощью ProGuard необходимо сохранить некоторые классы. Можно использовать следующие фрагменты кода конфигурации:
> 
> -keep public class * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

Укажите строку подключения Engagement, вызвав следующий метод в операции запуска.

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

* Если она отсутствует, добавьте следующие разрешения Android (перед тегом `<application>`):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Добавьте следующий раздел (между тегами `<application>` и `</application>`):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Замените `<Your application name>` на имя вашего приложения.

> [!TIP]
> Ключ `android:label` позволяет выбрать имя службы Engagement в том виде, в каком оно будет выводиться для конечных пользователей на экране "Работающие службы" их телефонов. Рекомендуется задать для этого атрибута значение `"<Your application name>Service"` (например, `"AcmeFunGameService"`).
> 
> 

Задание атрибута `android:process` гарантирует, что служба Engagement будет запускаться в своем собственном процессе (запуск службы Engagement в одном процессе с приложением может привести к тому, что основной поток (поток пользовательского интерфейса) станет хуже реагировать).

> [!NOTE]
> Любой код, помещаемый в `Application.onCreate()` и другие обратные вызовы приложения, будут выполняться для всех процессов приложения, включая службу Engagement. При этом возможны нежелательные побочные эффекты (например, выделение ненужной памяти и потоки в процессе Engagement, повторяющиеся получатели рассылки или службы).
> 
> 

При переопределении `Application.onCreate()` рекомендуется добавить следующий фрагмент кода в начало функции `Application.onCreate()`:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

То же самое можно сделать для `Application.onTerminate()`, `Application.onLowMemory()` и `Application.onConfigurationChanged(...)`.

Вы можете также расширить `EngagementApplication` вместо расширения `Application`: обратный вызов `Application.onCreate()` проверяет процесс и вызывает `Application.onApplicationProcessCreate()` только в том случае, если текущий процесс не размещает службу Engagement. Эти же правила применяются к другим обратным вызовам.

## <a name="basic-reporting"></a>Упрощенные отчеты
### <a name="recommended-method-overload-your-activity-classes"></a>Рекомендуемый метод: перегрузка классов `Activity`
Для активации отчетов всех журналов, которые требуются службе Engagement для вычисления статистических данных пользователей, действий, сбоев и технических проблем, достаточно сделать все подклассы `*Activity` наследуемыми из соответствующих классов `Engagement*Activity` (например, если устаревшее действие расширяет `ListActivity`, сделайте так, чтобы оно расширяло `EngagementListActivity`).

**Без Engagement:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**С Engagement:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> При использовании `EngagementListActivity` или `EngagementExpandableListActivity` необходимо обеспечить, чтобы любой вызов `requestWindowFeature(...);` выполнялся до вызова `super.onCreate(...);`, в противном случае произойдет сбой.
> 
> 

Эти классы можно найти в папке `src` и скопировать их в проект. Данные классы также представлены в **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Альтернативный метод: вызов `startActivity()` и `endActivity()` вручную
Если вы не можете перегружать классы `Activity` или не хотите этого делать, вы можете запускать и завершать действия, вызывая методы `EngagementAgent` напрямую.

> [!IMPORTANT]
> Пакет SDK для Android никогда не вызывает метод `endActivity()`, даже если приложение закрыто (на Android приложения фактически никогда не закрываются). Поэтому *настоятельно* рекомендуется вызывать метод `startActivity()` в обратном вызове `onResume` *всех* действий, а метод `endActivity()` в обратном вызове `onPause()` *всех* действий. Это единственный способ гарантировать отсутствие утечки сеансов. Если наблюдается утечка сеанса, служба Engagement никогда не отключится от внутреннего сервера Engagement (так как служба остается подключенной до тех пор, пока сеанс находится в состоянии ожидания).
> 
> 

Вот пример: 

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

Этот пример очень похож на класс `EngagementActivity` и его варианты, исходный код которых предоставляется в папке `src`.

## <a name="test"></a>Тест
Теперь проверьте интеграцию, запустив мобильное приложение в эмуляторе или на устройстве и убедившись в том, что сеанс регистрируется на вкладке "Монитор".

Следующие разделы необязательны.

## <a name="location-reporting"></a>Отчеты о расположении
Для того чтобы создавались отчеты о расположениях, необходимо добавить несколько строк конфигурации (между тегами `<application>` и `</application>`).

### <a name="lazy-area-location-reporting"></a>Отчеты о расположении отложенной области
Отчеты о расположении отложенной области позволяют включать в отчеты страну, область и населенный пункт, связанные с устройствами. Этот тип отчетов о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Отчеты об области устройства выполняются максимум один раз за сеанс. GPS никогда не используется, и в результате этот тип отчета о расположении оказывает исключительно небольшое (если не сказать вообще не оказывает) воздействие на батарею.

Области в отчетах используются для вычисления географических статистических данных о пользователях, сеансах, событиях и ошибках. Они также могут использоваться в качестве критерия для рекламных компаний Reach.

Чтобы включить отчет о приблизительном местоположении устройств, можно использовать конфигурацию, упомянутую ранее в этой процедуре:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Также необходимо добавить следующее разрешение, если оно отсутствует:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Вы также можете продолжить использовать команду ``ACCESS_FINE_LOCATION``, если она уже используется в приложении.

### <a name="real-time-location-reporting"></a>Отчет о расположении в реальном времени
Отчет о расположении в реальном времени позволяет включать в отчет широту и долготу, связанные с устройствами. По умолчанию такой тип отчета о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Этот отчет активен только тогда, когда приложение выполняется в фоновом режиме (т.е во время сеанса).

Расположения в реальном времени *НЕ* используются для вычисления статистических данных. Единственное их назначение — дать возможность использовать критерий геозоны реального времени \<Reach-Audience-geofencing\> в рекламных кампаниях.

Чтобы включить отчет о местоположении устройств в реальном времени, можно использовать конфигурацию, упомянутую ранее в этой процедуре:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Также необходимо добавить следующее разрешение, если оно отсутствует:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Вы также можете продолжить использовать команду ``ACCESS_FINE_LOCATION``, если она уже используется в приложении.

#### <a name="gps-based-reporting"></a>Отчеты на базе GPS
По умолчанию отчеты о расположении в реальном времени используют только сетевые расположения. Чтобы включить использование расположений на базе GPS (которые значительно точнее), используйте следующий объект конфигурации:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Также необходимо добавить следующее разрешение, если оно отсутствует:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Отчет в фоновом режиме
По умолчанию отчеты о расположении в реальном времени активны только тогда, когда приложения выполняются в фоновом режиме (т.е. во время сеанса). Чтобы включить отчеты в фоновом режиме, используйте следующий объект конфигурации:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Когда приложение выполняется в фоновом режиме, в отчете показываются только расположения на основе сети, даже если включен GPS.
> 
> 

Отчет о расположении в фоновом режиме будет остановлен, если пользователь перезагружает устройство. Можно добавить следующий код, чтобы обеспечить его автоматический перезапуск во время загрузки:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Также необходимо добавить следующее разрешение, если оно отсутствует:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Разрешения в Android M
Начиная с Android M, управление некоторыми разрешениями осуществляется в среде выполнения и требует согласия пользователя.

Разрешения среды выполнения будут по умолчанию отключены для новых установленных приложений, если планируется использовать API Android уровня 23. В противном случае они будут включены по умолчанию.

Пользователь может включить или отключить эти разрешения в меню параметров устройства. Отключение разрешений в системном меню приводит к завершению фоновых процессов приложения. Это поведение системы, которое не влияет на возможность получения push-уведомлений в фоновом режиме.

В контексте Служб мобильного взаимодействия утверждения в среде выполнения требуют следующие разрешения.

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (только если для него планируется использовать API Android уровня 23)

Внешнее хранилище используется только для получения общей картины обработки рекламных кампаний. Если пользователи сообщают вам, что это разрешение мешает в работе, вы можете его удалить, если оно использовалось только для Служб мобильного взаимодействия, но при этом также отключается компонент общей картины.

Что касается характеристик расположений, разрешения для пользователя следует запрашивать с помощью стандартного диалогового окна системы. В случае утверждения пользователем вам необходимо указать классу ``EngagementAgent`` о необходимости принятия этого изменения во внимание в реальном времени (в противном случае изменение будет обработано, когда пользователь в следующий раз запустит приложение).

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
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Расширенные отчеты
Если вы хотите получать отчеты о событиях, ошибках и заданиях, относящихся к конкретному приложению, следует использовать API Engagement при помощи методов класса `EngagementAgent` . Объект этого класса можно получить с помощью вызова статического метода `EngagementAgent.getInstance()` .

API службы Engagement позволяет использовать все расширенные возможности Engagement и подробно описывается в разделе "Использование API службы Engagement в Android" (а также в технической документации по классу `EngagementAgent` ).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Расширенная конфигурация (в AndroidManifest.xml)
### <a name="wake-locks"></a>Блокировки пробуждения
Если необходимо гарантировать отправку статистики в реальном времени при использовании Wi-Fi или при выключенном экране, добавьте следующее необязательное разрешение:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Отчет о сбоях
Если вы хотите отключить отчеты о сбоях, добавьте следующий код (между тегами `<application>` и `</application>`):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Пороговое значение пакета
По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение очень часто отправляет отчеты журналов, лучше заносить их в буфер и передавать все вместе через определенные промежутки времени (это называется пакетным режимом). Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы советуем использовать пороговое значение пакета не более чем 30 000 (30 с).

### <a name="session-timeout"></a>Время ожидания сеанса
По умолчанию сеанс завершается через 10 секунд после последнего действия (что обычно происходит при нажатии кнопки Home или Назад, при переводе телефона в ждущий режим или при переходе к другому приложению). Это позволяет избежать разбиения сеанса каждый раз, когда пользователь выходи из приложения и возвращается в него очень быстро (это может наблюдаться при выборе изображения, проверке уведомления и т.д.). Вам может потребоваться изменить данный параметр. Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Выключение отчетов журналов
### <a name="using-a-method-call"></a>Использование вызова метода
Если необходимо, чтобы служба Engagement перестала отправлять журналы, можно вызвать:

            EngagementAgent.getInstance(context).setEnabled(false);

Этот вызов является постоянным: он использует файл общих параметров.

Если служба Engagement активна, вызывается эта функция. Для остановки службы может потребоваться около 1 минуты. Однако в этом случае служба совсем не будет запускаться при следующем запуске приложения.

Вы можете снова включить журнал отчетов путем вызова той же функции с `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Интеграция в собственное `PreferenceActivity`
Вместо вызова этой функции вы можете интегрировать данный параметр непосредственно в существующее `PreferenceActivity`.

Можно настроить Engagement для использования файла настроек (с нужным режимом) в файле `AndroidManifest.xml` с `application meta-data`:

* Ключ `engagement:agent:settings:name` используется для определения имени общего файла настроек.
* Ключ `engagement:agent:settings:mode` определяет режим общего файла настроек. Следует использовать тот же режим, что и в `PreferenceActivity`. Режим должен передаваться в виде числа: при использовании сочетания постоянных флагов в коде необходимо проверить общее значение.

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

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
