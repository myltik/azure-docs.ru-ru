<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement для развертывания Unity в Android"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для развертывания приложений Unity на устройствах iOS."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Приступая к работе с Azure Mobile Engagement для развертывания Unity в Android

[AZURE.INCLUDE [Выбор других руководств](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как с помощью Azure Mobile Engagement анализировать использование приложения и как отправлять push-уведомления сегментированным пользователям приложения Unity при его развертывании на устройстве Android. Это руководство основывается на классическом руководстве по создании игры в мяч в приложении Unity. Вам следует выполнить инструкции, приведенные в этом [руководстве](mobile-engagement-unity-roll-a-ball.md), прежде чем начинать интеграцию Mobile Engagement, пример которой приводится в настоящем руководстве.

Для работы с данным учебником требуется следующее:

+ приложение [Unity Editor](http://unity3d.com/get-unity);
+ [пакет SDK для Mobile Engagement Unity](https://aka.ms/azmeunitysdk);
+ пакет SDK для Google Android.

> [AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения Android

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

###Импорт пакета Unity

1. Загрузите пакет [Mobile Engagement Unity](https://aka.ms/azmeunitysdk) и сохраните его на локальном компьютере. 

2. Последовательно выберите пункты **Ресурсы -> Импорт пакета -> Пользовательский пакет** и выберите пакет, который вы скачали на предыдущем этапе.

	![][70]

3. Убедитесь, что выбраны все файлы, а затем нажмите кнопку **Импорт**.

	![][71]

4. Когда импорт будет завершен, в вашем проекте отобразятся импортированные файлы пакета SDK.

	![][72]

###Обновление конфигурации EngagementConfiguration

1. В папке пакета SDK откройте файл сценария **EngagementConfiguration** и обновите **ANDROID\_CONNECTION\_STRING** с помощью строки подключения, которую вы получили ранее на портале Azure.  

	![][73]

2. Сохраните файл.

3. Последовательно щелкните **Файл -> Engagement -> Создание манифеста Android**. Это подключаемый модуль, добавленный в ваш пакет SDK для Mobile Engagement. Если его щелкнуть, параметры проекта будут автоматически обновлены.

	![][74]

> [AZURE.IMPORTANT] Его нужно запускать всегда, когда вы обновляете файл **EngagementConfiguration**. В противном случае изменения не отражаются в приложении.

###Настройка базового отслеживания в приложении

1. Откройте сценарий **PlayerController**, вложенный для редактирования в объект Player. 

2. Добавьте следующую инструкцию using:

		using Microsoft.Azure.Engagement.Unity;

3. Добавьте следующую строку в метод `Start()`.
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Развертывание и запуск приложения
Прежде чем развертывать это приложение Unity у себя на устройстве, убедитесь, что на компьютере установлен пакет SDK для Android.

1. Подключите устройство Android к компьютеру. 

2. Откройте **Файл -> Параметры сборки**.

	![][40]

3. Выберите **Android** и щелкните **Переключить платформу**.

	![][51]

	![][52]

4. Щелкните **Настройка проигрывателя** и укажите допустимый идентификатор пакета.

	![][53]

5. Наконец, нажмите кнопку **Сборка и запуск**.

	![][54]

6. Возможно, вам потребуется указать имя папки для хранения пакета Android.

7. Если процесс пройдет успешно и пакет будет развернут на подключенном устройстве, вы увидите игру Unity у себя на телефоне.

##<a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с мониторингом в реальном времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

[AZURE.INCLUDE [Включение Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Обновление конфигурации EngagementConfiguration

1. В папке пакета SDK откройте файл сценария **EngagementConfiguration** и обновите **ANDROID\_GOOGLE\_NUMBER** с помощью **номера проекта Google**, который вы получили ранее на портале разработчиков облака Google. Это строковое значение, поэтому обязательно заключите его в двойные кавычки. 

	![][75]

2. Сохраните файл .

3. Последовательно щелкните **Файл -> Engagement -> Создание манифеста Android**. Это подключаемый модуль, добавленный в ваш пакет SDK для Mobile Engagement. Если его щелкнуть, параметры проекта будут автоматически обновлены.

	![][74]

###Настройка в приложении получения уведомлений

1. Откройте сценарий **PlayerController**, вложенный для редактирования в объект Player. 

2. Добавьте следующую строку в метод `Start()`.

		EngagementReachAgent.Initialize();

3. Теперь, когда приложение обновлено, разверните и запустите его на устройстве согласно приведенным ниже инструкциям.

[AZURE.INCLUDE [Отправка уведомления с портала](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

<!---HONumber=AcomDC_0330_2016-->