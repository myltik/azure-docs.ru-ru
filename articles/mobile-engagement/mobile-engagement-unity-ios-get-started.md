<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement для развертывания Unity в iOS"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для развертывания приложений Unity на устройствах iOS."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Приступая к работе с Azure Mobile Engagement для развертывания Unity в iOS

[AZURE.INCLUDE [Выбор других руководств](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как с помощью Azure Mobile Engagement анализировать использование приложения и как отправлять push-уведомления сегментированным пользователям приложения Unity при его развертывании в на устройстве iOS. Это руководство основывается на классическом руководстве по создании игры в мяч в приложении Unity. Вам следует выполнить инструкции, приведенные в этом [руководстве](mobile-engagement-unity-roll-a-ball.md), прежде чем начинать интеграцию Mobile Engagement, пример которой приводится в настоящем руководстве.

Для работы с данным учебником требуется следующее:

+ приложение [Unity Editor](http://unity3d.com/get-unity);
+ [пакет SDK для Mobile Engagement Unity](https://aka.ms/azmeunitysdk);
+ редактор XCode.

> [AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS

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

1. В папке пакета SDK откройте файл сценария **EngagementConfiguration** и обновите **IOS\_CONNECTION\_STRING** с помощью строки подключения, которую вы получили ранее на портале Azure.  

	![][73]

2. Сохраните файл .

###Настройка базового отслеживания в приложении

1. Откройте сценарий **PlayerController**, вложенный для редактирования в объект Player. 

2. Добавьте следующую инструкцию using:

		using Microsoft.Azure.Engagement.Unity;

3. Добавьте следующую строку в метод `Start()`.
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Развертывание и запуск приложения

1. Подключите устройство iOS к компьютеру. 

2. Откройте **Файл -> Параметры сборки**.

	![][40]

3. Выберите **iOS** и щелкните **Переключить платформу**.

	![][41]

	![][42]

4. Щелкните **Настройка проигрывателя** и укажите допустимый идентификатор пакета.

	![][53]

5. Наконец, нажмите кнопку **Сборка и запуск**.

	![][54]

6. Возможно, вам потребуется указать имя папки для хранения пакета iOS.

	![][43]

7. Если все пойдет хорошо и проект будет скомпилирован, откройте его в приложении XCode.

8. Убедитесь, что в проекте указан правильный **идентификатор пакета**.

	![][75]

10. Теперь запустите приложение XCode, чтобы развернуть пакет на подключенном устройстве, и вы увидите игру Unity у себя на телефоне.

##<a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с мониторингом в реальном времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. Дополнительно настраивать в приложении получение уведомлений не нужно, так как эта функция уже настроена.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png

<!---HONumber=AcomDC_0330_2016-->