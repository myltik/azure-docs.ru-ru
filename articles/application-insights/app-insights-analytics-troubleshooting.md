<properties 
	pageTitle="Устранение неполадок аналитики — эффективного инструмента поиска Application Insights | Microsoft Azure" 
	description="Возникли проблемы с аналитикой Application Insights? Начните отсюда." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2016" 
	ms.author="awills"/>


# Устранение неполадок аналитики в Application Insights


Возникли проблемы с [аналитикой Application Insights](app-insights-analytics.md)? Начните отсюда. Аналитика — мощный инструмент поиска Visual Studio Application Insights.



## Ограничения

* В настоящее время результаты запросов ограничены данными за последнюю неделю.
* Тестируемые браузеры: последние выпуски Chrome, Edge и Internet Explorer.

## "Непредвиденная ошибка"

![Экран непредвиденной ошибки](./media/app-insights-analytics-troubleshooting/010.png)

Произошла внутренняя ошибка во время выполнения портала — необработанное исключение.

* Очистите кэш браузера. 

## 403\... Попробуйте перезагрузить

![403\... Попробуйте перезагрузить](./media/app-insights-analytics-troubleshooting/020.png)

Произошла ошибка, связанная с проверкой подлинности (во время проверки подлинности или во время создания маркера доступа). Возможно, для восстановления портала требуется изменение параметров браузера.

* Убедитесь, что в браузере включены сторонние файлы cookie. 

    См. раздел [how to disable third party cookies ](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers) (Отключение сторонних файлов cookie), но обратите внимание, что их необходимо **включить**.

## 403\... Проверьте зону безопасности

![403\... Проверьте зону безопасности](./media/app-insights-analytics-troubleshooting/030.png)

Произошла ошибка, связанная с проверкой подлинности (во время проверки подлинности или во время создания маркера доступа). Возможно, для восстановления портала требуется изменение параметров браузера.

1. Убедитесь, что в браузере включены сторонние файлы cookie. 

    См. раздел [how to disable third party cookies ](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers) (Отключение сторонних файлов cookie), но обратите внимание, что их необходимо **включить**.

2. Вы использовали элемент избранного, закладку или сохраненную ссылку для открытия портала аналитики? Вы вошли, используя не те учетные данные, которые использовались при сохранении ссылки?

 2. Используйте окно браузера в закрытом или анонимном режиме (после закрытия всех окон). Необходимо будет ввести свои учетные данные.

 2. Откройте другое (стандартное) окна браузера и перейдите к [Azure](https://portal.azure.com). Выполните выход. Затем откройте ссылку и выполните вход, используя правильные учетные данные.

2. У пользователей браузеров Edge и Internet Explorer также может возникать эта ошибка, если параметры доверенной зоны не поддерживаются.

	Убедитесь, что [портал аналитики](https://analytics.applicationinsights.io) и [портал Azure Active Directory](https://portal.azure.com) находятся в одной зоне безопасности:

 * В Internet Explorer откройте **Свойства браузера**, **Безопасность**, **Надежные сайты**, **Сайты**:

    ![Диалоговое окно "Свойства браузера", добавление сайта в список надежных сайтов](./media/app-insights-analytics-troubleshooting/033.png)

    Если в списке веб-сайтов есть любой из следующих URL-адресов, убедитесь, чтобы и остальные адреса также были включены:

    https://analytics.applicationinsights.io<br/> https://login.microsoftonline.com<br/> https://login.windows.net


## 404\... Ресурс не найден

![404\... Ресурс не найден](./media/app-insights-analytics-troubleshooting/040.png)

Ресурс приложения был удален из Application Insights и больше не доступен. Это может произойти, если сохранить URL-адрес на странице аналитики.


## 403\... Нет авторизации

![403\... Нет авторизации](./media/app-insights-analytics-troubleshooting/050.png)

У вас нет разрешения на открытие этого приложения в аналитике.

* Вы получили ссылку от другого пользователя? Попросите, чтобы вас внесли в список [читателей или участников этой группы ресурсов](app-insights-resources-roles-access-control.md).
* Вы сохранили ссылку, используя другие учетные данные? Откройте [портал Azure](https://portal.azure.com), выполните выход, а затем повторно щелкните эту ссылку и введите правильные учетные данные.

## 403\... Хранилище HTML5

На нашем портале используются хранилища HTML5 — localStorage и sessionStorage.

* Chrome: Settings (Параметры), Privacy (Конфиденциальность), Content settings (Параметры содержимого).
* Internet Explorer: "Свойства браузера", вкладка "Дополнительно", "Безопасность", "Включить хранилище DOM".


![403\... Попробуйте включить хранилище HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## 404\... Подписка не найдена


![404\... Подписка не найдена](./media/app-insights-analytics-troubleshooting/070.png)

Недопустимый URL-адрес.

* Откройте ресурс приложения на [портале Application Insights](https://portal.azure.com). Затем используйте кнопку аналитики.

## 404\... Страница не существует

![404\... Страница не существует](./media/app-insights-analytics-troubleshooting/080.png)

Недопустимый URL-адрес.

* Откройте ресурс приложения на [портале Application Insights](https://portal.azure.com). Затем используйте кнопку аналитики.

## Если ничто из предложенного не помогает    

Откройте [запрос в службу поддержки](app-insights-get-dev-support.md).
 
[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0622_2016-->