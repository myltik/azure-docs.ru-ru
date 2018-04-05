---
title: Интеграция пакета Android SDK для Служб мобильного взаимодействия Azure
description: Последние обновления и процедуры пакета Android SDK для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 15e71d8aeb8c8060161ade4262e0be0a093b5650
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Интеграция Engagement Reach в Android
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!IMPORTANT]
> Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе "Интеграция Engagement на платформе Android".
> 
> 

## <a name="standard-integration"></a>Стандартная интеграция

Скопируйте файлы ресурсов Reach из SDK в проект:

* Скопируйте файлы из папки `res/layout`, входящей в состав SDK, в папку `res/layout` приложения.
* Скопируйте файлы из папки `res/drawable`, входящей в состав SDK, в папку `res/drawable` приложения.

Отредактируйте файл `AndroidManifest.xml`:

* Добавьте следующий раздел (между тегами `<application>` и `</application>`):
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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
* Это разрешение потребуется для воспроизведения системных уведомлений, которые пользователь не щелкнул при загрузке (иначе они сохранятся на диске, но больше не будут отображаться).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Укажите значок для уведомлений (и в приложении, и системных), скопировав и отредактировав следующий раздел (между тегами `<application>` и `</application>`):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Этот раздел **обязателен** , если вы планируете использовать системные уведомления при создании кампаний Reach. В Android системные уведомления без значков не отображаются. Поэтому, если не добавить этот раздел, пользователи не смогут получать уведомления.
> 
> 

* При создании кампаний с системными уведомлениями необходимо добавить следующие разрешения (после тега `</application>` ), если их нет:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * Если ваше приложение ориентировано на API Android уровня 23 или более высокого, на Android M для разрешения ``WRITE_EXTERNAL_STORAGE`` требуется утверждение пользователя. Ознакомьтесь с [этим разделом](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Для системных уведомлений можно также указать в кампаниях Reach, должно ли устройство звонить или вибрировать. Для этого необходимо объявить следующее разрешение (после тега `</application>` ):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Без этого разрешения Android не допустит отображения системных уведомлений, если в диспетчере кампаний Reach установлен флажок "Звонок" или "Вибросигнал".

## <a name="native-push"></a>Системные push-уведомления
Теперь, когда модуль Reach настроен, необходимо настроить системные push-уведомления, чтобы можно было получать уведомления в рамках кампаний на устройстве.

Для Android поддерживаются две службы:

* Устройства Google Play: используйте [Google Cloud Messaging], следуя инструкциям в руководстве по [интеграции GCM с помощью службы Mobile Engagement](mobile-engagement-android-gcm-integrate.md).
* Устройства Amazon: используйте [Amazon Device Messaging], следуя инструкциям в руководстве по интеграции [ADM с платформой Engagement](mobile-engagement-android-adm-integrate.md).

Если нужно задействовать и устройства Amazon, и устройства Google, можно поместить все в один файл AndroidManifest.xml/APK для разработки. Но при отправке в Amazon приложение может быть отвергнуто, если в нем будет обнаружен код GCM.

В этом случае следует использовать несколько APK.

**Теперь приложение готово к получению и отображению кампаний.**

## <a name="how-to-handle-data-push"></a>Обработка данных push-уведомлений
### <a name="integration"></a>Интеграция
Если нужно, чтобы приложение могло принимать push-обновления данных Reach, нужно создать подкласс `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` и указать ссылку на него в файле `AndroidManifest.xml` (между тегами `<application>` или `</application>`):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Затем можно переопределить обратные вызовы `onDataPushStringReceived` и `onDataPushBase64Received`. Вот пример: 

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Категория
При создании кампании отправки данных параметр "Категория" является необязательным. Он позволяет фильтровать отправленные данные. Это полезно при наличии нескольких широковещательных получателей, обрабатывающих разные типы push-обновлений, или если нужно распространять разные виды данных `Base64` и определять их тип перед анализом.

### <a name="callbacks-return-parameter"></a>Параметр возврата обратных вызовов
Вот некоторые правила обработки параметра возврата `onDataPushStringReceived` и `onDataPushBase64Received`:

* Широковещательный получатель должен возвращать `null` в обратном вызове, если он не знает, как обрабатывать данные push-уведомления. Используйте эту категорию, чтобы определить, должен ли широковещательный получатель обрабатывать данные push-уведомления.
* Один из широковещательных получателей должен возвращать `true` в обратном вызове, если он получает данные push-уведомления.
* Один из широковещательных получателей должен возвращать `false` в обратном вызове, если он распознает данные push-уведомления, но по какой-либо причине отбрасывает их. Например, можно возвращать `false` , если принятые данные недействительны.
* Если один широковещательный получатель возвращает `true`, а другой возвращает `false` для одного и того же push-обновления, то поведение не определено. Таких ситуаций следует избегать.

Возвращаемый тип используется только для статистики Reach:

* `Replied` увеличивается, если один из широковещательных получателей возвратил `true` или `false`.
* `Actioned` увеличивается, если один из широковещательных получателей возвратил `true`.

## <a name="how-to-customize-campaigns"></a>Как настраивать кампании
Для настройки кампаний можно изменять разметки, входящие в состав Reach SDK.

Следует сохранить все идентификаторы, используемые в разметках, и типы представлений, используемые в идентификаторах, особенно для текстовых представлений и представлений рисунков. Некоторые представления используются только для того, чтобы скрывать или отображать определенные области, поэтому их тип можно изменить. Если вы собираетесь изменить тип представления в предоставленной разметке, проверьте исходный код.

### <a name="notifications"></a>Уведомления
Существует два типа уведомлений: системные уведомления и уведомления приложения, использующие разные файлы разметки.

#### <a name="system-notifications"></a>Системные уведомления
Для настройки системных уведомлений необходимо использовать **категории**. Можно перейти в раздел [Категории](#categories).

#### <a name="in-app-notifications"></a>Уведомления в приложении
По умолчанию уведомление в приложении — это представление, которое динамически добавляется в пользовательский интерфейс текущего действия с помощью метода Android `addContentView()`. Это называется наложением уведомлений. Наложения уведомлений удобный для быстрой интеграции, поскольку для них не требуется изменять разметку приложений.

Чтобы изменить внешний вид наложения уведомлений, вы можете просто изменить файл `engagement_notification_area.xml` нужным образом.

> [!NOTE]
> Файл `engagement_notification_overlay.xml` используется для создания наложения уведомлений, он включает файл `engagement_notification_area.xml`. Его также можно настроить в соответствии с потребностями (например, для расположения область уведомлений в области наложения).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Включите разметку уведомления в состав разметки действия
Наложения удобны для быстрой интеграции, но в определенных случаях их использование может быть неудобно или может вызывать нежелательные побочные эффекты. Систему наложений можно настроить на уровне действия в базе данных, предотвратив нежелательные эффекты.

Используя оператор Android **include** , вы можете включить разметку уведомления в существующую разметку. Ниже приведен пример измененной разметки `ListActivity`, содержащей только `ListView`.

**До интеграции с Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**После интеграции с Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

В этом примере мы добавили родительский контейнер, поскольку исходная разметка использовала представление списка в качестве элемента верхнего уровня. Мы также добавили `android:layout_weight="1"`, чтобы иметь возможность просматривать список, настроенный с помощью `android:layout_height="fill_parent"`.

Пакет Engagement Reach SDK автоматически обнаруживает наличие разметки уведомления в этом действии и не будет добавлять наложение для этого действия.

> [!TIP]
> Если использовать ListActivity в приложении, то видимое наложение Reach не позволит реагировать на элементы в представлении списка с помощью щелчков. Это известная проблема. Чтобы обойти эту проблему, рекомендуем встроить разметку уведомления в ваше собственное действие списка, как в приведенном выше примере.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Отключение уведомлений приложения для определенных действий
Если не нужно, чтобы в действие было добавлено наложение, и если вы не включаете разметку уведомления в вашу собственную разметку, можно отключить наложение для этого действия в `AndroidManifest.xml`, добавив раздел `meta-data`, как в следующем примере:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### Категории <a name="categories"></a>
При изменении предоставленных разметок изменяется внешний вид всех уведомлений. С помощью категорий можно определять различные типы внешнего вида (возможно, виды поведения) для уведомлений. Категорию можно указать при создании рекламной кампании. Учтите, что категории также позволяют настраивать объявления и опросы. Это описано далее в этом документе.

Чтобы зарегистрировать обработчик категорий для уведомлений, нужно добавить вызов при инициализации приложения.

> [!IMPORTANT]
> Перед продолжением прочтите предупреждение об атрибуте android:process \<android-sdk-engagement-process\> в статье об интеграции Engagement на платформе Android.
> 
> 

В следующем примере предполагается, что вы прочли это предупреждение и используете подкласс `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

Объект `MyNotifier` является реализацией обработчика категорий уведомлений. Это либо реализация интерфейса `EngagementNotifier`, либо подкласс реализации по умолчанию: `EngagementDefaultNotifier`.

Обратите внимание, что один и тот же обработчик может обрабатывать несколько категорий. Их можно зарегистрировать так:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Чтобы заменить реализацию категорий по умолчанию, можно зарегистрировать вашу собственную реализацию, как в следующем примере:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

Текущую категорию, используемую в обработчике и передаваемую в виде параметра, в большинстве методов можно переопределить в `EngagementDefaultNotifier`.

Она передается либо как параметр `String`, либо опосредованно в объекте `EngagementReachContent`, содержащем метод `getCategory()`.

Вы можете изменить большую часть процесса создания уведомления путем переопределения методов в `EngagementDefaultNotifier`. Для более широких возможностей настройки см. техническую документацию и исходный код.

##### <a name="in-app-notifications"></a>Уведомления в приложении
Если нужно просто использовать дополнительные разметки для определенной категории, можно сделать как в следующем примере:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Пример `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Как видите, идентификатор представления наложения отличается от стандартного. Важно, чтобы у каждой разметки использовался уникальный идентификатор для наложений.

**Пример `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Как видите, идентификатор области уведомлений отличается от стандартного. Важно, чтобы у каждой разметки использовался уникальный идентификатор областей уведомления.

В этом простом примере категория управляет отображением уведомлений приложения в верхней части экрана. Мы не изменяли стандартные идентификаторы, используемые в самой области уведомлений.

Если вы хотите это изменить, придется заново определить метод `EngagementDefaultNotifier.prepareInAppArea` . Рекомендуем ознакомиться с технической документацией и с исходным кодом в `EngagementNotifier` и `EngagementDefaultNotifier`, если требуется такая расширенная настройка.

##### <a name="system-notifications"></a>Системные уведомления
За счет расширения `EngagementDefaultNotifier` можно переопределить `onNotificationPrepared`, чтобы изменить уведомление, которое было предусмотрено в реализации по умолчанию.

Например: 

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

В этом примере системное уведомление отображается как текущее событие для содержимого, когда используется категория "ongoing".

Если вы хотите создать объект `Notification` с нуля, можно вернуть `false` в метод и вызвать `notify` для `NotificationManager`. В этом случае важно сохранить `contentIntent`, `deleteIntent` и идентификатор уведомления, используемый в `EngagementReachReceiver`.

Вот правильный пример такой реализации:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Объявления только для уведомлений
Можно настроить управление щелчками объявлений об уведомлениях путем переопределения `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` для изменения `Intent`. Использование этого метода позволяет удобно настраивать флаги.

Например, чтобы добавить флаг `SINGLE_TOP` :

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Пользователям прежних версий Engagement следует учитывать, что системные уведомления без URL-адреса действия теперь запускают приложение, если оно было в фоновом режиме, поэтому можно вызывать этот метод в объявлении без URL-адреса действия. Это следует учитывать при настройке намерений.

Вы также можете реализовать `EngagementNotifier.executeNotifAnnouncementAction` с нуля.

##### <a name="notification-life-cycle"></a>Жизненный цикл уведомления
При использовании категории по умолчанию некоторые методы жизненного цикла вызываются для объекта `EngagementReachInteractiveContent`, чтобы предоставить статистику и обновить состояние кампании:

* Когда уведомление отображается в приложении или в строке состояния, метод `displayNotification` (он передает статистику) вызывается в `EngagementReachAgent`, если `handleNotification` возвращает `true`.
* При закрытии уведомления вызывается метод `exitNotification` и предоставляется статистика, после чего можно обрабатывать следующие кампании.
* При щелчке уведомления вызывается `actionNotification` , передается статистика и запускается связанное намерение.

Если ваша реализация `EngagementNotifier` обходит поведение по умолчанию, необходимо вызывать эти методы жизненного цикла самостоятельно. В следующих примерах показаны некоторые случаи обхода поведения по умолчанию.

* Вы не расширяли `EngagementDefaultNotifier`, например реализовали обработку категорий с нуля.
* Для системных уведомлений вы переопределили `onNotificationPrepared` и изменили `contentIntent` или `deleteIntent` в объекте `Notification`.
* Для уведомлений в приложениях вы переопределили `prepareInAppArea`. Не забудьте сопоставить по крайней мере `actionNotification` с одним из элементов управления пользовательского интерфейса.

> [!NOTE]
> Если `handleNotification` выдает исключение, содержимое удаляется и вызывается `dropContent`. Это отражается в статистике, после этого можно обрабатывать последующие кампании.
> 
> 

### <a name="announcements-and-polls"></a>Объявления и опросы
#### <a name="layouts"></a>Макеты
Можно изменить файлы `engagement_text_announcement.xml`, `engagement_web_announcement.xml` и `engagement_poll.xml`, чтобы настроить текстовые объявления, веб-объявления и опросы.

У этих файлов общие разметки области заголовка и области кнопок. Разметка заголовка — `engagement_content_title.xml` , в качестве фона используется одноименный файл рисунка. Разметка кнопок действия и выхода — `engagement_button_bar.xml` , в качестве фона используется одноименный файл рисунка.

В опросах разметка вопросов и вариантов ответа динамически берется из файла разметки `engagement_question.xml` для вопросов и `engagement_choice.xml` для вариантов ответа.

#### <a name="categories"></a>Категории
##### <a name="alternate-layouts"></a>Альтернативные макеты
Так же, как и категорию уведомлений, категорию кампании можно использовать для получения альтернативных макетов для объявлений и опросов.

Например, чтобы создать категорию для текстового объявления, вы можете расширить `EngagementTextAnnouncementActivity` и добавить ссылку на этот элемент в файле `AndroidManifest.xml`:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Обратите внимание, что категория в фильтре намерений используется для обозначения различия от действия объявления по умолчанию.

В Reach SDK применяется система намерений для сопоставления нужных действий с определенными категориями. Если сопоставление не удается, то используется категория по умолчанию.

Необходимо реализовать `MyCustomTextAnnouncementActivity`, если требуется изменить разметку (но сохранить идентификаторы представлений). Достаточно определить класс, как в следующем примере:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Чтобы заменить стандартную категорию текстовых объявлений, просто замените `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` вашей реализацией.

Аналогичным образом можно настраивать веб-объявления и опросы.

Для веб-объявлений вы можете расширить `EngagementWebAnnouncementActivity` и объявить ваше действие в `AndroidManifest.xml`, как в следующем примере:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Для опросов можно расширить `EngagementPollActivity` и объявить действие в `AndroidManifest.xml`, как в следующем примере:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Реализация с нуля
Вы можете реализовать категории для действий объявлений (и опросов), не расширяя один из классов `Engagement*Activity` , входящих в состав SDK Reach. Это полезно в случае, если вам нужно определить разметку, не использующую такие же представления, как в стандартных разметках.

Так же, как и для расширенной настройки уведомлений, мы советуем просмотреть исходный код в стандартной реализации.

При этом нужно помнить о следующем: Reach запустит действие с определенным намерением (в соответствии с фильтром намерений) и с дополнительным параметром, который и является идентификатором содержимого.

Для получения объекта содержимого, содержащего поля, указанные при создании компании на веб-сайте, можно сделать следующее:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Для статистики следует отправлять содержимое, отображаемое в событии `onResume`:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Не забудьте вызвать `actionContent(this)` или `exitContent(this)` для объекта содержимого перед переходом действия в фоновый режим.

Если не вызвать `actionContent` или `exitContent`, то статистика не будет отправляться (т. е. кампания будет проводиться без аналитики) и, что важнее, не будут создаваться уведомления о последующих кампаниях до перезапуска процесса приложения.

При изменении ориентации и других параметров конфигурации становится непросто определить, переключается ли действие в фоновый режим. При стандартной реализации фиксируется выход из содержимого при выходе пользователя из действия (путем нажатия `HOME` или `BACK`), но не при изменении ориентации.

Вот интересная часть реализации:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Как видите, если вызвать `actionContent(this)`, а затем завершить действие, то можно безопасно вызывать `exitContent(this)` без нежелательных последствий.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
