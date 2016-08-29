<properties
	pageTitle="Дополнительные возможности создания отчетов пакета SDK для Android в Azure Mobile Engagement"
	description="Описание создания расширенных отчетов для записи данных аналитики для пакета SDK для Android в Azure Mobile Engagement."
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
	ms.date="08/10/2016"
	ms.author="piyushjo;ricksal" />

# Создание расширенных отчетов с помощью службы Engagement в Android

> [AZURE.SELECTOR]
- [Универсальная платформа Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

В этой статье описаны дополнительные сценарии создания отчетов в приложении Android. Эти параметры можно применить для приложения, созданного в учебнике [по началу работы](mobile-engagement-android-get-started.md).

## Предварительные требования

[AZURE.INCLUDE [Предварительные требования](../../includes/mobile-engagement-android-prereqs.md)]

Для изученного учебника были намеренно подобраны понятные и простые задания, однако для выбора доступны и дополнительные параметры.

## Изменение классов `Activity`

В [учебнике по началу работы](mobile-engagement-android-get-started.md) достаточно было обеспечить наследование подклассов `*Activity` из соответствующих классов `Engagement*Activity`. Например, если устаревшее действие расширяло `ListActivity`, нужно было обеспечить, чтобы оно расширяло и `EngagementListActivity`.

> [AZURE.IMPORTANT] При использовании `EngagementListActivity` или `EngagementExpandableListActivity` необходимо обеспечить, чтобы любой вызов `requestWindowFeature(...);` выполнялся до вызова `super.onCreate(...);`, в противном случае произойдет сбой.

Эти классы можно найти в папке `src` и скопировать их в проект. Данные классы также представлены в **JavaDoc**.

## Альтернативный метод: вызов `startActivity()` и `endActivity()` вручную

Если вы не можете перегружать классы `Activity` или не хотите этого делать, можно запускать и завершать действия, вызывая методы `EngagementAgent` напрямую.

> [AZURE.IMPORTANT] Пакет SDK для Android никогда не вызывает метод `endActivity()`, даже если приложение закрыто (на Android приложения никогда не закрываются). Поэтому *НАСТОЯТЕЛЬНО* рекомендуется вызывать метод `startActivity()` в обратном вызове `onResume` *ВСЕХ* действий, а метод `endActivity()` в обратном вызове `onPause()` *ВСЕХ* действий. Это единственный способ гарантировать отсутствие утечки сеансов. Если наблюдается утечка сеанса, служба Engagement никогда не отключится от внутреннего сервера Engagement (так как служба остается подключенной до тех пор, пока сеанс находится в состоянии ожидания).

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

Этот пример похож на класс `EngagementActivity` и его варианты, исходный код которых предоставляется в папке `src`.

## Использование Application.onCreate()

Любой код, помещаемый в `Application.onCreate()` и другие обратные вызовы приложения, выполняется для всех процессов приложения, включая службу Engagement. При этом возможны нежелательные побочные эффекты, например выделение ненужной памяти и потоки в процессе Engagement, повторяющиеся получатели рассылки или службы.

При переопределении `Application.onCreate()` рекомендуется добавить следующий фрагмент кода в начало функции `Application.onCreate()`.

	 public void onCreate()
	 {
	   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
	     return;

	   ... Your code...
	 }

То же самое можно сделать для `Application.onTerminate()`, `Application.onLowMemory()` и `Application.onConfigurationChanged(...)`.

Вы можете также расширить `EngagementApplication` вместо расширения `Application`: обратный вызов `Application.onCreate()` проверяет процесс и вызывает `Application.onApplicationProcessCreate()` только в том случае, если текущий процесс не размещает службу Engagement. Эти же правила применяются к другим обратным вызовам.

## Теги в файле AndroidManifest.xml

Атрибут `android:label` тега службы в файле AndroidManifest.xml позволяет выбрать имя службы Engagement в том виде, в каком оно выводится для конечных пользователей на экране "Running Services" (Работающие службы) их телефонов. Рекомендуется задать для этого атрибута значение `"<Your application name>Service"` (например, `"AcmeFunGameService"`).

Задание атрибута `android:process` гарантирует, что служба Engagement будет запускаться в своем собственном процессе (запуск службы Engagement в одном процессе с приложением может привести к тому, что основной поток (поток пользовательского интерфейса) станет хуже реагировать).

## Создание приложения при помощи ProGuard

При создании пакета приложения с помощью ProGuard необходимо сохранить некоторые классы. Можно использовать следующие фрагменты кода конфигурации:

	-keep public class * extends android.os.IInterface
	-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
	<methods>;
 	}

<!---HONumber=AcomDC_0817_2016-->