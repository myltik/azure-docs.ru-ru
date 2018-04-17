---
title: Использование API службы Engagement в Android
description: Последний пакет Android SDK - Использование API службы Engagement в Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 79d0652be227dd6703a35d31409cf8e0d9c59519
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-android"></a>Использование API службы Engagement в Android
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Этот документ представляет собой дополнение к статье [Reporting Options with Engagement on Android](mobile-engagement-android-advanced-reporting.md)(Параметры отчетов для Служб мобильного взаимодействия на устройствах Android). В нем рассказывается о том, как с помощью Engagement API предоставлять статистику по приложению.

Следует иметь в виду, что если вы хотите использовать службу Engagement только для получения отчетов о сеансах, действиях, сбоях и технической информации, проще всего сделать так, чтобы все подклассы `Activity` наследовались из класса `EngagementActivity`.

Если вы хотите, чтобы служба делала что-то еще, например сообщала о событиях, ошибках и заданиях приложения или сообщала о действиях приложения способом, отличным от реализованного в классах `EngagementActivity`, необходимо использовать API Engagement.

API Engagement предоставляется в классе `EngagementAgent` . Экземпляр этого класса можно получить, вызвав статический метод `EngagementAgent.getInstance(Context)` (обратите внимание на то, что объект `EngagementAgent` возвращается в виде singleton).

## <a name="engagement-concepts"></a>Основные понятия Engagement
В следующих подразделах дано более подробное объяснение [общих понятий Служб мобильного взаимодействия](mobile-engagement-concepts.md) для платформы Android.

### <a name="session-and-activity"></a>`Session` и `Activity`
Если пользователь неактивен между двумя *действиями* больше чем несколько секунд, то последовательность его *действий* разбивается на два отдельных *сеанса*. Эти несколько секунд называются «время ожидания сеанса».

*Действие*, как правило, связано с одним экраном приложения, т. е. *действие* начинается при отображении экрана и завершается при его закрытии. Именно в таком случае пакет SDK для Engagement интегрируется с помощью класса `EngagementActivity`.

*Действиями* можно также управлять вручную с помощью API Engagement. Это позволяет разделить экран на несколько частей, чтобы получить дополнительную информацию об использовании этого экрана (например, информацию о частоте и продолжительности использования диалоговых окон на этом экране).

## <a name="reporting-activities"></a>Уведомление о действиях
> [!IMPORTANT]
> В подготовке отчетов о действиях, как это описывается в данном разделе, нет необходимости, если используется класс `EngagementActivity` и его варианты, что рассматривается подробно в документе «Интеграция службы Engagement на Android».
> 
> 

### <a name="user-starts-a-new-activity"></a>Запуск нового действия пользователем
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

При каждом изменении пользовательского действия необходимо вызывать `startActivity()` . При первом вызове этой функции начинается новый сеанс пользователя.

Лучше всего вызывать данную функцию в обратном вызове каждого действия `onResume` .

### <a name="user-ends-his-current-activity"></a>Завершение текущего действия пользователем
            EngagementAgent.getInstance(this).endActivity();

Необходимо вызвать `endActivity()` как минимум один раз, когда пользователь заканчивает свое последнее действие. В результате пакет SDK для Engagement получает информацию о том, что пользователь неактивен и его сеанс необходимо закрыть после истечения времени ожидания (если вызов `startActivity()` сделан до этого момента, то сеанс просто возобновляется).

Лучше всего вызывать данную функцию в обратном вызове каждого действия `onPause` .

## <a name="reporting-events"></a>Уведомление о событиях
### <a name="session-events"></a>События сеанса
События сеанса обычно используются для уведомления о действиях, выполняемых пользователем во время сеанса.

**Пример без дополнительных данных:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Пример с дополнительными данными:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Изолированные события
В отличие от событий сеанса изолированные события могут происходить вне контекста сеанса.

**Пример.**

Предположим, что требуется отчет о событиях, возникающих при активации получателя рассылки:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Уведомление об ошибках
### <a name="session-errors"></a>Ошибки сеанса
Ошибки сеанса обычно используются для уведомления об ошибках, влияющих на пользователя во время сеанса.

**Пример**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Изолированные ошибки
В отличие от ошибок сеанса изолированные ошибки могут возникать вне контекста сеанса.

**Пример.**

В следующем примере показано, как создать отчет об ошибке при нехватке памяти на телефоне во время выполнения процесса приложения.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Уведомление о заданиях
### <a name="example"></a>Пример
Предположим, что вам необходимо сообщить о продолжительности процесса входа в систему:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Уведомление об ошибках при выполнении задания
Ошибки могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

**Пример.**

Предположим, что нужно включить в отчет ошибку, которая возникает во время процесса входа в систему:

[...] public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Отчеты о событиях во время выполнения задания
События могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

**Пример.**

Предположим, что у нас есть социальная сеть, и мы используем задание, чтобы сообщить об общем времени подключения пользователя к серверу. Пользователь может оставаться подключенных в фоновом режиме, даже если он использует другое приложение или телефон находится в спящем режиме, и поэтому сеанс отсутствует.

Пользователь может получить сообщения от своих друзей. Это и есть событием задания.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>Дополнительные параметры
Произвольные данные можно прикрепить к событиям, ошибкам, действиям и заданиям.

Эти данные могут быть структурированы, здесь используется класс пакета Android (на самом деле он работает в качестве дополнительных параметров в целях Android). Обратите внимание, что пакет может содержать массивы или экземпляры другого пакета.

> [!IMPORTANT]
> Если вы добавляете разбиваемые на пакеты или сериализуемые параметры, необходимо обеспечить реализацию их метода `toString()`, чтобы он возвращал понятную пользователю строку. Сериализуемые классы, содержащие непереходные поля, которые не сериализуются, приведут к сбою Android при вызове `bundle.putSerializable("key",value);`
> 
> [!WARNING]
> Разреженные массивы в дополнительных параметрах не поддерживаются, то есть не сериализуется как массив. Их следует преобразовать в стандартные массивы перед использованием в дополнительных параметрах.
> 
> 

### <a name="example"></a>Пример
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Ограничения
#### <a name="keys"></a>ключей
Каждый ключ в `Bundle` должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### <a name="size"></a>Размер
Длина дополнений ограничена **1024** знаками на один вызов (после кодирования в JSON-файле службой Engagement).

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 58 знаков:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Уведомление о данных приложения
С помощью функции `sendAppInfo()` вы можете вручную сообщать о данных отслеживания (или любых других данных о приложении).

Обратите внимание, что такие данные можно отправлять поэтапно, так как для заданного устройства будет сохраняться только последнее значение заданного ключа.

как и при дополнительных событиях класс пакета используется для получения абстрактных сведений о приложении. Отметим, что массивы или вложенные пакеты будут считаться плоскими строками (с использованием сериализации JSON).

### <a name="example"></a>Пример
Ниже приведен пример кода для отправки пола и даты рождения пользователя.

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Ограничения
#### <a name="keys"></a>ключей
Каждый ключ в `Bundle` должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### <a name="size"></a>Размер
Данные приложения ограничены **1024** знаками на один вызов (после кодирования в JSON-файле службой Engagement).

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 44 знака:

            {"expiration":"2016-12-07","status":"premium"}
