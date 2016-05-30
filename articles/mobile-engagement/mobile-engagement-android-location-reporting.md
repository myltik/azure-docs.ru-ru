<properties
	pageTitle="Создание отчетов о расположении для пакета SDK для Android в Azure Mobile Engagement"
	description="Сведения о создании отчетов о расположении для пакета SDK для Android в Azure Mobile Engagement."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/12/2016"
	ms.author="piyushjo;ricksal" />

# Создание отчетов о расположении для пакета SDK для Android в Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

В этой статье описывается, как создавать отчеты о расположении для приложения Android.

## Предварительные требования

[AZURE.INCLUDE [Предварительные требования](../../includes/mobile-engagement-android-prereqs.md)]

## Отчеты о расположении

Для того чтобы создавались отчеты о расположениях, необходимо добавить несколько строк конфигурации (между тегами `<application>` и `</application>`).

### Отчеты о расположении отложенной области

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

### Отчет о расположении в реальном времени

Отчет о расположении в реальном времени позволяет включать в отчет широту и долготу, связанные с устройствами. По умолчанию такой тип отчета о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Этот отчет активен только тогда, когда приложение выполняется в фоновом режиме (т.е во время сеанса).

Расположения в реальном времени *НЕ* используются для вычисления статистических данных. Единственное их назначение — дать возможность использовать критерий геозоны реального времени <Reach-Audience-geofencing> в рекламных кампаниях.

Чтобы включить функцию отчетов о расположении в реальном времени, добавьте строку кода туда, где указана строка подключения Engagement в действии запуска. Результат должен выглядеть так:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Также необходимо добавить следующее разрешение, если оно отсутствует:

	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Вы также можете продолжить использовать команду ``ACCESS_FINE_LOCATION``, если она уже используется в приложении.

#### Отчеты на базе GPS

По умолчанию отчеты о расположении в реальном времени используют только сетевые расположения. Чтобы включить использование расположений на базе GPS (которые значительно точнее), используйте следующий объект конфигурации:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Также необходимо добавить следующее разрешение, если оно отсутствует:

	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Отчет в фоновом режиме

По умолчанию отчеты о расположении в реальном времени активны только тогда, когда приложения выполняются в фоновом режиме (т.е. во время сеанса). Чтобы включить отчеты в фоновом режиме, используйте следующий объект конфигурации:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Когда приложение выполняется в фоновом режиме, в отчете показываются только расположения на основе сети, даже если включен GPS.

Отчет о расположении в фоновом режиме будет остановлен, если пользователь перезагружает устройство. Можно добавить следующий код, чтобы обеспечить его автоматический перезапуск во время загрузки:

	<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
		   android:exported="false">
		<intent-filter>
		    <action android:name="android.intent.action.BOOT_COMPLETED" />
		</intent-filter>
	</receiver>

Также необходимо добавить следующее разрешение, если оно отсутствует:

	<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## Разрешения в Android M

Начиная с Android M, управление некоторыми разрешениями осуществляется в среде выполнения и требует согласия пользователя.

Разрешения среды выполнения будут по умолчанию отключены для новых установленных приложений, если планируется использовать API Android уровня 23. В противном случае они будут включены по умолчанию.

Пользователь может включить или отключить эти разрешения в меню параметров устройства. Отключение разрешений в системном меню приводит к завершению фоновых процессов приложения. Такое поведение системы не влияет на возможность получения push-уведомлений в фоновом режиме.

В контексте отчетов о расположении Mobile Engagement утверждения в среде выполнения требуют следующие разрешения.

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Разрешения для пользователя следует запрашивать с помощью стандартного диалогового окна системы. В случае утверждения пользователем вам необходимо указать классу ``EngagementAgent`` о необходимости принятия этого изменения во внимание в реальном времени (в противном случае изменение будет обработано, когда пользователь в следующий раз запустит приложение).

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

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

<!---HONumber=AcomDC_0518_2016-->