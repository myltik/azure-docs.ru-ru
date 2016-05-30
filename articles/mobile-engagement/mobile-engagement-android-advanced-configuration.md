<properties
	pageTitle="Расширенная конфигурация для пакета SDK Android для Azure Mobile Engagement"
	description="Описание дополнительных параметров конфигурации, включая манифест Android, в пакете SDK для Android в службе Azure Mobile Engagement."
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

# Расширенная конфигурация для пакета SDK Android для Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-logging.md)

В этой статье описано, как настроить различные параметры конфигурации для приложений Android в службе Azure Mobile Engagement.

## Предварительные требования

[AZURE.INCLUDE [Предварительные требования](../../includes/mobile-engagement-android-prereqs.md)]

## Требования к разрешениям
Для некоторых параметров необходимы специальные разрешения, которые здесь перечислены для справки, а также включены в некоторые компоненты. Добавьте эти разрешения в файл AndroidManifest.xml проекта непосредственно перед тегом `<application>` или после него:

После того как вы вставите соответствующее разрешение из таблицы ниже, код должен выглядеть следующим образом.

	<uses-permission android:name="android.permission.[specific permission]"/>


| Разрешение | Применение |
| ---------- | --------- |
| ИНТЕРНЕТ | Обязательный параметр. Базовые отчеты |
| ACCESS\_NETWORK\_STATE | Обязательный параметр. Базовые отчеты |
| RECEIVE\_BOOT\_COMPLETED | Обязательный параметр. Отображение центра уведомлений после перезагрузки устройства |
| WAKE\_LOCK | Настоятельно рекомендуется. Включает сбор данных при использовании Wi-Fi или при выключенном экране |
| VIBRATE | необязательный параметр. Включает вибрацию при получении уведомления |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | необязательный параметр. Включает общие уведомления Android |
| WRITE\_EXTERNAL\_STORAGE | необязательный параметр. Включает общие уведомления Android |
| ACCESS\_COARSE\_LOCATION | необязательный параметр. Включает отчет о расположении в реальном времени |
| ACCESS\_FINE\_LOCATION | необязательный параметр. Включает отчет о расположении на основе GPS |

Начиная с Android M [управление некоторыми разрешениями осуществляется в среде выполнения](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Если вы уже используете разрешение ``ACCESS_FINE_LOCATION``, вам не нужно использовать ``ACCESS_COARSE_LOCATION``.

## Варианты настройки манифеста для Android

### Отчет о сбоях

Если вы хотите отключить отчеты о сбоях, добавьте следующий код (между тегами `<application>` и `</application>`):

	<meta-data android:name="engagement:reportCrash" android:value="false"/>

### Пороговое значение пакета

По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение очень часто отправляет отчеты журналов, лучше заносить их в буфер и передавать все вместе через определенные промежутки времени (это называется пакетным режимом). Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`):

	<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы советуем использовать пороговое значение пакета не более чем 30 000 (30 с).

### Время ожидания сеанса

По умолчанию сеанс завершается через 10 секунд после последнего действия (что обычно происходит при нажатии кнопки Home или Назад, при переводе телефона в ждущий режим или при переходе к другому приложению). Это позволяет избежать разбиения сеанса каждый раз, когда пользователь выходи из приложения и возвращается в него очень быстро (это может наблюдаться при выборе изображения, проверке уведомления и т.д.). Вам может потребоваться изменить данный параметр. Чтобы это сделать, добавьте следующий код (между тегами `<application>` и `</application>`):

	<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## Выключение отчетов журналов

### Использование вызова метода

Если необходимо, чтобы служба Engagement перестала отправлять журналы, можно вызвать:

	EngagementAgent.getInstance(context).setEnabled(false);

Этот вызов является постоянным: он использует файл общих параметров.

Если служба Engagement активна, вызывается эта функция. Для остановки службы может потребоваться около 1 минуты. Однако в этом случае служба совсем не будет запускаться при следующем запуске приложения.

Вы можете снова включить журнал отчетов путем вызова той же функции с `true`.

### Интеграция в собственное `PreferenceActivity`

Вместо вызова этой функции вы можете интегрировать данный параметр непосредственно в существующее `PreferenceActivity`.

Можно настроить Engagement для использования файла настроек (с нужным режимом) в файле `AndroidManifest.xml` с `application meta-data`:

-   Ключ `engagement:agent:settings:name` используется для определения имени общего файла настроек.
-   Ключ `engagement:agent:settings:mode` определяет режим общего файла настроек. Следует использовать тот же режим, что и в `PreferenceActivity`. Режим должен передаваться в виде числа: при использовании сочетания постоянных флагов в коде необходимо проверить общее значение.

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

<!---HONumber=AcomDC_0518_2016-->