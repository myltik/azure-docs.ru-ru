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
	ms.date="05/04/2015" 
	ms.author="kapiteir" />


#Пакет Android SDK для Azure Mobile Engagement

Начните здесь, чтобы получить все сведения об интеграции службы Azure Mobile Engagement в приложение Android. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-android-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-android-sdk-content.md).

##Процедуры по интеграции
1. Начните с этого раздела: [Интеграция службы Mobile Engagement в приложение Android](mobile-engagement-android-integrate-engagement.md)

2. Сведения об уведомлениях: [Интеграция модуля Reach (уведомления) в приложение Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Интеграция GCM с помощью службы Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Интеграция ADM с помощью службы Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Реализация плана добавления тегов: [Использование расширенного API добавления тегов службы Mobile Engagement в приложении Android](mobile-engagement-android-use-engagement-api.md)


##Заметки о выпуске

###4.0.0 (07/06/2015)

-   Внутренние изменения протокола для повышения надежности аналитики и push-уведомлений.
-   Системные push-уведомления (GCM/ADM) теперь также используются для уведомлений из приложений, поэтому их учетные данные необходимо задавать для всех типов кампаний push-уведомлений.
-   Исправлены ошибки общих уведомлений: ранее они отображались только 10 секунд.
-   Исправлена ошибка, связанная с возможностью выбора используемого по умолчанию URL-адреса действия в веб-объявлении.
-   Исправлена ошибка, связанная с редкими сбоями при управлении локальным хранилищем.
-   Исправлена ошибка, связанная с динамическим управлением строкой конфигурации.
-   Обновлено лицензионное соглашение с пользователем.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-android-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию пакета SDK, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить ряд процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-android-upgrade-procedure.md). Например, при миграции с версии 1.4.0 на версию 1.6.0 необходимо сначала выполнить процедуру «Из версии 1.4.0 в 1.5.0», а затем процедуру «Из версии 1.5.0 в 1.6.0».

Независимо от того, с какой версии выполняется обновление, необходимо заменить `mobile-engagement-VERSION.jar` на новую версию.

###С версии 3.0.0 до версии 4.0.0

#### Системные push-уведомления

Системные push-уведомления (GCM/ADM) теперь также используются для уведомлений из приложений, поэтому их учетные данные необходимо задавать для всех типов кампаний push-уведомлений.

Если вы еще не сделали этого, следуйте [этой процедуре](mobile-engagement-android-integrate-engagement-reach.md#native-push).

#### AndroidManifest.xml

Возможности интеграции с Reach были изменены в ``AndroidManifest.xml``.

Замените это:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

на

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Теперь при выборе объявления (текстового или с веб-содержимым) или опроса может отображаться экран загрузки. Чтобы кампании работали в версии 4.0.0, необходимо добавить этот код:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### Ресурсы

Внедрите новый файл `res/layout/engagement_loading.xml` в проект.

<!---HONumber=July15_HO4-->