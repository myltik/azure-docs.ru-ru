<properties 
	pageTitle="Использование API службы Engagement в Android" 
	description="Последний пакет Android SDK - Использование API службы Engagement в Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Использование API службы Engagement в Android

Этот документ представляет собой добавление к документу [Интеграция службы Engagement в Android](mobile-engagement-android-integrate-engagement.md): он предоставляет подробные сведения об использовании API службы Engagement для получения отчета со статистическими данными приложения.

Следует иметь в виде, что если требуется использовать службу Engagement для получения отчетов только о сеансах, действиях, сбоях приложения, а также технических сведений, проще всего выполнить наследование подклассов `Activity` из класса `EngagementActivity`.

Если необходимо большее, например, если необходимо получить отчет об определенных событиях, ошибках и заданиях приложения, или, если требуется получить отчет о действиях приложения иным способом, а не тем, которые реализуется в классах `EngagementActivity`, нужно использовать API службы Engagement.

API службы Engagement предоставляется классом `EngagementAgent`. Экземпляр этого класса может быть получен путем вызова статического метода `EngagementAgent.getInstance(Context)` (отметим, что возвращенный объект `EngagementAgent` одноэлементный).

##Основные понятия службы Engagement

В следующих частях уточняются основные [понятия службы Mobile Engagement](mobile-engagement-concepts.md)для платформы Android.

### `Сеанс` и `Действие`

Если пользователь в течение нескольких секунд остается в состоянии простоя между двумя *действиями*, то его последовательность *действий* разбивается на два отдельных *сеанса*. Эти несколько секунд называются "время ожидания сеанса".

 *Действие* обыкновенно связано с одним экраном приложения, то есть можно сказать, что  *действие* начинается, когда отображается экран и заканчивается, когда экран закрывается. Именно так и происходит  при интеграции пакета SDK для службы Engagement с помощью классов `EngagementActivity`.

Однако *действиями* также можно управлять вручную при использовании API Engagement. Это позволяет разбить данный экран на несколько подчастей для получения дополнительных сведений об использовании данного экрана (например, для получения сведений, как часто и как долго диалоговые окна используются внутри этого экрана).

##Отчеты о действиях

> [AZURE.IMPORTANT] В подготовке отчетов о действиях, как это описывается в данном разделе, нет необходимости, если используется класс `EngagementActivity` и его варианты, что рассматривается подробно в документе "Интеграция службы Engagement в Android".

### Пользователь запускает новое действие

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Необходимо вызывать `startActivity()` при каждом изменении действия пользователя. Первый вызов этой функции запускает новый сеанс пользователя.

Лучше всего вызывать данную функцию в обратном вызове `onResume` каждого действия.

### Пользователь заканчивает свое текущее действие

			EngagementAgent.getInstance(this).endActivity();

Необходимо вызвать `endActivity()` как минимум один раз, когда пользователь заканчивает свое последнее действие. В результате пакет SDK Engagement получает информацию о том, что пользователь в настоящее время простаивает и, что сеанс пользователя необходимо закончить после окончания времени ожидания сеанса (если вызов `startActivity()` сделан до окончания срока ожидания сеанса, то сеанс просто возобновляется).

Лучше всего вызывать данную функцию в обратном вызове `onPause` каждого действия.

##Отчеты о событиях

### События сеанса

События сеанса обыкновенно используются для отчетов о действиях, выполненных пользователем во время сеанса.

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

### Изолированный события

В отличие от событий сеанса изолированные события могут происходить вне контекста сеанса.

**Пример:**

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

##Отчеты об ошибках

### Ошибки сеанса

Ошибки сеанса обычно используются для отчетов об ошибках, оказывающих влияние на пользователя во время его сеанса.

**Пример:**

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

### Изолированные ошибки

В отличие от ошибок сеанса изолированные ошибки могут возникать вне контекста сеанса.

**Пример:**

В следующем примере показано, как создать отчет об ошибке при нехватке памяти на телефоне во время выполнения процесса приложения.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##Задания создания отчетов

### Пример

Предположим, что необходимо создать отчет о продолжительности процесса входа в систему.
			
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

### Сообщения об ошибках во время выполнения задания

Ошибки могут быть связаны с выполнением задания, а не с текущим сеансом пользователя.

**Пример:**

Предположим, что нужно включить в отчет ошибку, которая возникает во время процесса входа в систему:

[...]
public void signIn(Context context, ...) {

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

### Отчеты о событиях во время выполнения задания

События могут быть связаны с выполнением задания, а не с текущим сеансом пользователя.

**Пример:**

Предположим, что у нас есть социальная сеть, и мы используем задание для создания отчета об общем времени, в течение которого пользователь подключен к серверу. Пользователь может оставаться подключенных в фоновом режиме, даже если он использует другое приложение или телефон находится в спящем режиме, и поэтому сеанс отсутствует.

Пользователь может получать сообщения от своих друзей, это событие задания.
			
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

##Дополнительные параметры

К событиям, ошибкам, действиям и заданиям могут прикрепляться произвольные данные.

Эти данные могут быть структурированы, здесь используется класс пакета Android (на самом деле он работает в качестве дополнительных параметров в целях Android). Обратите внимание, что пакет может содержать массивы или экземпляры другого пакета.

> [AZURE.IMPORTANT] Если помещаются разбиваемые на пакеты или сериализуемые параметры, необходимо обеспечить реализацию их метода `toString()`, чтобы он возвращал понятную пользователю строку. Сериализуемые классы, содержащие непереходные поля, которые не сериализуются, приведут к сбою Android при вызове `bundle.putSerializable("key",value);`

> [AZURE.WARNING] Разреженные массивы в дополнительных параметрах не поддерживаются, то есть не сериализуется как массив. Их следует преобразовать в стандартные массивы перед использованием в дополнительных параметрах.

### Пример

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Ограничения

#### ключей

Каждый ключ в `Bundle` должен соответствовать следующему регулярному выражению

`^[a-zA-Z][a-zA-Z_0-9]*`

Это означает, что ключи должны начинаться с по крайней мере с одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### Размер

Вспомогательные элементы ограничены **1024** символами на вызов (один раз закодированный в JSON службой Engagement).

В предыдущем примере JSON отправил серверу элемент длиной 58 символов.

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##Отчеты о сведениях приложения

Можно вручную сообщить сведения отслеживания (или любые другие сведения о приложении) с помощью функции `sendAppInfo()`.

Обратите внимание, что эти сведения могут отправляться постепенно: сохраняется только последнее значение для заданного ключа для данного устройства.

как и при дополнительных событиях класс пакета используется для получения абстрактных сведений о приложении. Отметим, что массивы или вложенные пакеты будут считаться плоскими строками (с использованием сериализации JSON).

### Пример

Ниже приведен пример кода для отправки пола и даты рождения пользователя.

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Ограничения

#### ключей

Каждый ключ в `Bundle` должен соответствовать следующему регулярному выражению

`^[a-zA-Z][a-zA-Z_0-9]*`

Это означает, что ключи должны начинаться с по крайней мере с одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### Размер

Сведения о приложении ограничены **1024** символами на вызов (один раз  закодированный в JSON службой Engagement).

В предыдущем примере JSON отправил серверу элемент длиной 44 символов.

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=47-->
