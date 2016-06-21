<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement для веб-приложений | Microsoft Azure"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для веб-приложений."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="js"
	ms.topic="hero-article"
	ms.date="06/01/2016"
	ms.author="piyushjo" />

# Приступая к работе с Azure Mobile Engagement для веб-приложений

[AZURE.INCLUDE [Выбор других руководств](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как с помощью Azure Mobile Engagement понять тенденции использования веб-приложения.

Для работы с данным учебником требуется следующее:

+ Visual Studio 2015 или другой редактор по вашему выбору;
+ [веб-пакет SDK](http://aka.ms/P7b453). 

Сейчас веб-пакет SDK находится в предварительной версии и поддерживает только функцию аналитики. Пока он не поддерживает отправку push-уведомлений в приложении или браузере.

> [AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##Настройка Mobile Engagement для веб-приложения

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных.

Мы создадим базовое веб-приложение с помощью Visual Studio, чтобы продемонстрировать интеграцию, хотя вы можете выполнить эти действия для любого веб-приложения, созданного вне Visual Studio.

###Создание нового веб-приложения

Указанные ниже действия предполагают использование Visual Studio 2015, хотя они и аналогичны действиям с использованием более ранних версий Visual Studio.

1. Запустите Visual Studio и на **начальном** экране выберите пункт **Создать проект**.

2. Во всплывающем окне последовательно выберите пункты **Веб** -> **Веб-приложение ASP.NET**. Введите необходимые значения в полях **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.

3. Во всплывающем окне **Выбор шаблона** выберите элемент **Пусто** в разделе **ASP.Net 4.5 Templates** (Шаблоны ASP.Net 4.5) и нажмите кнопку **ОК**.

Вы создали пустой проект веб-приложения, в которое мы интегрируем веб-пакет SDK для Azure Mobile Engagement.

###Подключите приложение к серверной части Mobile Engagement.

1. Создайте новую папку с именем **javascript** в решении и добавьте в нее JS-файл веб-пакета SDK с именем **azure engagement.js**. 

2. Добавьте в папку javascript новый файл с именем **main.js**, содержащий следующий код. Обязательно обновите строку подключения. Этот объект `azureEngagement` будет использоваться для доступа к методам веб-пакета SDK.

		var azureEngagement = {
		    debug: true,
		    connectionString: 'xxxxx'
		};

	![Использование JS-файлов в Visual Studio][1]

##Включение мониторинга в режиме реального времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере одно действие в серверную часть Mobile Engagement. Действие в контексте веб-приложения — это веб-страница.

1. Создайте страницу с именем **home.html** в решении и настройте ее в качестве начальной страницы для веб-приложения. 
2. Включите два сценария JavaScript, добавленных ранее на этой странице. Для этого добавьте в тег body следующий код. 

	    <script type="text/javascript" src="javascript/main.js"></script>
	    <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Обновите тег body для вызова метода `startActivity` EngagementAgent.
		
		<body onload="engagement.agent.startActivity('Home')">

4. Вот как будет выглядеть страница **home.html**:
		
		<html>
		<head>
			...
		</head>
		<body onload="engagement.agent.startActivity('Home')">
		    <script type="text/javascript" src="javascript/main.js"></script>
		    <script type="text/javascript" src="javascript/azure-engagement.js"></script>
		</body>
		</html>

##Подключение приложения с возможностью его отслеживания в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с возможностью его отслеживания в режиме реального времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##Расширение аналитики

Здесь приведены все доступные методы для веб-пакета SDK, которые можно использовать для аналитики:

1. Действия и веб-страницы:

		engagement.agent.startActivity(name);
		engagement.agent.endActivity();

2. События
		
		engagement.agent.sendEvent(name, extras);

3. Ошибки

		engagement.agent.sendError(name, extras);

4. Задания

		engagement.agent.startJob(name);
		engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

<!---HONumber=AcomDC_0615_2016-->