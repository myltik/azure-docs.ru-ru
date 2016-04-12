
###Обновление файла манифеста для включения уведомлений

Скопируйте ресурсы обмена сообщениями внутри приложения в файл Manifest.xml между тегами `<application>` и `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
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

###Добавление значка для уведомлений

Вставьте следующий фрагмент XML-кода в файл Manifest.xml между тегами `<application>` и `</application>`.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Так вы определите значок, используемый в системных уведомлениях и уведомлениях в приложении. Это необязательно для уведомлений в приложении, но обязательно для системных уведомлений. Система Android отклоняет системные уведомления с недопустимыми значками.

Используйте значок, который хранится в одной из папок **drawable** (например, ``engagement_close.png``). Папка **mipmap** не поддерживается.

>[AZURE.NOTE] Не следует использовать значок **запуска**. Он имеет другое разрешение и, как правило, находится в неподдерживаемых папках MIP-карт.

В реальных приложениях следует использовать значок, который подходит для уведомлений в соответствии с [рекомендациями по разработке для Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP] Чтобы правильно выбрать разрешение значка, ознакомьтесь с [этими примерами](https://www.google.com/design/icons). Прокрутите страницу вниз до раздела **Notification** (Уведомления), щелкните значок и выберите команду `PNGS`, чтобы загрузить набор рисунков. Вы увидите, какие папки рисунков и с каким разрешением следует использовать для каждой версии значка.

###Настройка приложения для приема push-уведомлений GCM

1. После замены номера `project number`, полученного из консоли Google Play, вставьте следующий код в файл Manifest.xml между тегами `<application>` и `</application>`. Перенос строки (\\n) указан преднамеренно, поэтому добавьте его в конец номера проекта.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Вставьте приведенный ниже код в Manifest.xml между тегами `<application>` и `</application>`. Замените имя пакета <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Добавьте последний выделенный набор разрешений перед тегом `<application>`. Замените `<Your package name>` фактическим именем пакета вашего приложения.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

<!---HONumber=AcomDC_0330_2016-->