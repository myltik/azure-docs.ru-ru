<properties 
	pageTitle="Интеграция пакета Android SDK для Azure Mobile Engagement" 
	description="Последние обновления и процедуры пакета Android SDK для Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Магазин Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" class="current">Android</a></div>


#Интеграция службы Engagement на Android

> [AZURE.IMPORTANT] Минимальный уровень пакета API SDK Android должен быть 10 или выше (Android 2.3.3 ил выше).

Эта процедура описывает самый простой способ активации функции аналитики и мониторинга службы Engagement в приложении Android.

Следующих действий достаточно для активации отчета журналов, который необходим при вычислении всех статистических данных о пользователях, сеансах, действиях сбоях и технических проблемах. Отчет журналов, необходимый для вычисления других статистических данных таких как события, ошибки и задания, необходимо делать вручную с помощью API Engagement (см. android-sdk-engagement-advanced), поскольку эта статистика зависит от приложения.

##Внедрение пакета SDK Engagement и службы в проект Android

Получите `mobile-engagement-VERSION.jar` и поместите их в папку `libs` проекта Android (создайте папку библиотек, если она еще не создана).

> [AZURE.IMPORTANT]
> При создании пакета приложения с помощью ProGuard необходимо сохранить некоторые классы. Можно использовать следующие фрагменты кода конфигурации:
>
> 
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

Укажите строку подключения Engagement, вызвав следующий метод в операции запуска.

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

Строка подключения для приложения отображается на портале Azure.

-   Если она отсутствует, добавьте следующие разрешения Android (перед тегом `<application>`):

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   На некоторых моделях устройств н е удается создать идентификатор устройства Engagement из ANDROID\_ID (оно может быть дефектным или недоступным). В этом случае пакет SDK создает случайный идентификатор устройства и пытается сохранить его во внешнем хранилище устройства для того, чтобы другие приложения Engagement могли совместно использовать один и тот же идентификатор устройства (он также сохраняется как общее предпочтение, гарантирующее, что приложение само всегда использует один и тот же идентификатор вне зависимости от того, что происходит с внешним хранилищем) Для правильной работы этого механизма необходимо добавить следующее разрешение, если он отсутствует (перед тегом `<application>`):

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

-   Добавьте следующий раздел (между тегами `<application>` и `</application>`):

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   Замените `<Your application name>` на имя вашего приложения.

> [AZURE.TIP] Атрибут `android:label` позволяет выбрать имя службы Engagement в том виде. в каком оно будет появляться для конечных пользователей на экране "Работающие службы" их телефонов. Рекомендуется задать этому атрибуту значение `"<Your application name>Service"` (например, `"AcmeFunGameService"`).

Задание атрибута `android:process` гарантирует, что служба Engagement будет запускаться в своем собственном процессе (запуск службы Engagement в одном процессе с приложением может привести к тому, что потенциально основной поток и поток пользовательского интерфейса будут хуже реагировать).

> [AZURE.NOTE] Любой код, помещаемый в `Application.onCreate()` и другие обратные вызовы приложения будут выполняться для всех процессов приложения, включая службу Engagement. При этом возможны нежелательные побочные эффекты (например, выделение ненужной памяти и потоки в процессе Engagement, повторяющиеся получатели рассылки или службы).

При переопределении `Application.onCreate()` рекомендуется добавить следующий фрагмент кода в начале функции `Application.onCreate()`:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;
			
			   ... Your code...
			 }

Это же можно проделать для `Application.onTerminate()`, `Application.onLowMemory()` и `Application.onConfigurationChanged(...)`.

Можно также расширить `EngagementApplication` вместо расширения `Application`: обратный вызов `Application.onCreate()` обрабатывает проверку и вызовы `Application.onApplicationProcessCreate()` только, если текущий процесс не размещает службу Engagement. Эти же правила применяются к другим обратным вызовам.

##Базовые отчеты

### Рекомендуется использовать метод перегрузки классов `Activity`

Для активации отчетов всех журналов, которые требуются службе Engagement для сравнения статистических данных пользователей, действий, сбоев и технических проблем, достаточно просто сделать все подклассы `*Activity` наследуемыми из соответствующих классов `Engagement*Activity` (например, если действие прежних версий расширяет `ListActivity`, нужно сделать , чтобы оно расширяло `EngagementListActivity`).

**Без Engagement :**

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

**С Engagement :**

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

> [AZURE.IMPORTANT] При использовании `EngagementListActivity` или `EngagementExpandableListActivity` необходимо обеспечить, чтобы любой вызов `requestWindowFeature(...);` выполнялся до вызова `super.onCreate(...);`, в противном случае произойдет сбой.

Мы предоставляем подклассы `FragmentActivity` и `MapActivity`, но для того, чтобы избежать проблем с приложениями при использовании **ProGuard**, они не включаются в `engagement.jar`.

Эти классы можно найти в папке `src` и скопировать их в проект. Данные классы также представлены в **JavaDoc**.

### Альтернативный метод: вызов `startActivity()` и `endActivity()` вручную

Если невозможно или нежелательно перегружать классы `Activity`, вместо этого можно запускать и останавливать действия с помощью прямого вызова методов `EngagementAgent`.

> [AZURE.IMPORTANT] Пакет Android SDK никогда не вызывает метод `endActivity()`, даже если приложение закрыто (фактически, на Android приложения никогда не закрываются). Таким образом, *НАСТОЯТЕЛЬНО* рекомендуется вызывать метод `startActivity()` в обратном вызове `onResume`  *ВСЕХ* действий, а метод `endActivity()` в обратном вызове `onPause()`  *ВСЕХ* действий. Это единственный способ гарантировать отсутствие утечки сеансов. Если наблюдается утечка сеанса, служба Engagement никогда не отключится от внутреннего сервера Engagement (так как служба остается подключенной до тех пор, пока сеанс находится в состоянии ожидания).

Пример:

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

Этот пример очень похож на класса `EngagementActivity` и его варианты, исходный код которых предоставляется в папке `src`.

##Тест

Теперь следует проверить интеграцию, ознакомившись с разделом "Выполнение теста интеграции со службой Engagement на Android".

Следующие разделы необязательны.

##Расположение отчетов

Для того, чтобы выполнялись отчеты о расположениях необходимо добавить несколько строк конфигурации (между тегами `<application>` и `</application>`).

### Отчеты о расположении отложенной области

Отчеты о расположении отложенной области позволяют включать в отчеты страну, область и населенный пункт, связанные с устройствами. Этот тип отчетов о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Отчеты об области устройства выполняются максимум один раз за сеанс. GPS никогда не используется, и в результате этот тип отчета о расположении оказывает исключительно небольшое (если не сказать вообще не оказывает) воздействие на батарею.

Области в отчетах используются для вычисления географических статистических данных о пользователях, сеансах, событиях и ошибках. Они также могут использоваться в качестве критерия для рекламных компаний Reach. Последнюю зафиксированную область в отчете для устройства можно извлечь [API
устройства].

Чтобы включить отчет о расположении отложенной области, добавьте:

			<meta-data android:name="engagement:locationReport:lazyArea" android:value="true"/>

Также необходимо добавить следующее разрешение, если оно отсутствует:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### Отчет о расположении в реальном времени

Отчет о расположении в реальном времени позволяет включать в отчет широту и долготу, связанные с устройствами. По умолчанию такой тип отчета о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Этот отчет активен только тогда, когда приложение выполняется в фоновом режиме (т.е во время сеанса).

Расположения в реальном времени *НЕ* используются для вычисления статистических данных. Единственное их назначение - дать возможность использовать критерий
географического разграничения \<Reach-Audience-geofencing\> в реальном времени в рекламных кампаниях Reach.

Чтобы включить отчеты о расположении в реальном времени, добавьте:

			<meta-data android:name="engagement:locationReport:realTime" android:value="true" />

Также необходимо добавить следующее разрешение, если оно отсутствует:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

#### Отчеты на базе GPS

По умолчанию отчеты о расположении в реальном времени используют только сетевые расположения. Чтобы включить использование расположений на базе GPS (которые значительно точнее), добавьте:

			<meta-data android:name="engagement:locationReport:realTime:fine" android:value="true" />

Также необходимо добавить следующее разрешение, если оно отсутствует:

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Отчет в фоновом режиме

По умолчанию отчеты о расположении в реальном времени активны только тогда, когда приложения выполняются в фоновом режиме (т.е. во время сеанса). Чтобы включить отчеты в фоновом режиме, добавьте:

			<meta-data android:name="engagement:locationReport:realTime:background" android:value="true" />

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

##Расширенные отчеты

При необходимости получения отчетов о событиях, ошибках и заданиях, имеющих отношение к приложению, следует использовать API службы Engagement при помощи методов класса `EngagementAgent`. Объект этого класса можно получить с помощью вызова статического метода `EngagementAgent.getInstance()`.

API службы Engagement позволяет использовать все расширенные возможности этой службы и подробно описывается в разделе "Использование
API службы Engagement на Android" (а также в технической документации класса `EngagementAgent`).

##Расширенная конфигурация (в AndroidManifest.xml)

Если необходимо гарантировать отправку статистики в реальном времени при использовании Wi-Fi или при выключенном экране, добавьте следующее необязательное разрешение:

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

При необходимости выключить отчеты о сбоях, добавьте следующий код (между тегами `<application>` и `</application>`):

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

По умолчанию служба Engagement создает отчеты журналов в реальном времени. Если приложение очень часто отправляет отчеты журналов, лучше заносить их в буфер и передавать все вместе через определенные промежутки времени (это называется пакетным режимом). Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`):

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

Пакетный режим немного увеличит время работы от батареи, но оказывает воздействие на монитор службы Engagement: продолжительность всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания с меньшей продолжительностью, чем пороговое значение пакета, могут не отображаться). Рекомендуется использовать пороговое значение пакета не более 30 000 (30 секунд).

ПО умолчанию служба Engagement устанавливает подключение к нашим серверам при доступности сети. Если необходимо отложить подключение, добавьте следующий код (между тегами `<application>` и `</application>`):

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

По умолчанию сеанс завершается через 10 секунд после последнего действия (что обычно происходит при нажатии кнопки Home или Назад, при переводе телефона в ждущий режим или при переходе к другому приложению). Это позволяет избежать разбиения сеанса каждый раз, когда пользователь выходи из приложения и возвращается в него очень быстро (это может наблюдаться при выборе изображения, проверке уведомления и т.д.). Вам может потребоваться изменить данный параметр. Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`):

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##Выключение отчетов журналов

### Использование вызова метода

Если необходимо, чтобы служба Engagement перестала отправлять журналы, можно вызвать:

			EngagementAgent.getInstance(context).setEnabled(false);

Этот вызов является постоянным: он использует файл общих параметров.

Если служба Engagement активна, вызывается эта функция. Для остановки службы может потребоваться около 1 минуты. Однако в этом случае служба совсем не будет запускаться при следующем запуске приложения.

Можно снова включить отчеты журналов, вызвав ту же функцию со значением `true`.

### Интеграция в свой собственный `PreferenceActivity`

Вместо вызова данной функции можно также интегрировать данный параметр прямо в существующий `PreferenceActivity`.

Можно настроить службу Engagement для использования вашего файла параметров (в нужном режиме) в файле `AndroidManifest.xml` с помощью `application meta-data`:

-   Ключ `engagement:agent:settings:name` используется для определения имени файла общих параметров.
-   Ключ `engagement:agent:settings:mode` используется для определения режима файла общих параметров. необходимо использовать тот же режим, что и в `PreferenceActivity`. Режим должен передаваться в виде числа: при использовании сочетания постоянных флагов в коде необходимо проверить общее значение.

Служба Engagement всегда использует логический ключ `engagement:key` в файле параметров для управления данным параметром.

В следующем примере `AndroidManifest.xml` показаны значения по умолчанию:

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Далее можно добавить `CheckBoxPreference` в макет параметров, подобный приведенному ниже:

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[API устройства]: http://go.microsoft.com/?linkid=9876094

<!--HONumber=47-->
