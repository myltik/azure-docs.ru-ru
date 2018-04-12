---
title: Устранение неполадок аналитики в Azure Application Insights | Документация Майкрософт
description: 'Возникли проблемы с аналитикой Application Insights? Начните отсюда. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: 3ef9150cad62e9f9fc43f7afcbbbc01d89884a12
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Устранение неполадок аналитики в Application Insights
Возникли проблемы с [аналитикой Application Insights](app-insights-analytics.md)? Начните отсюда. Аналитика — мощный инструмент поиска Azure Application Insights.

## <a name="limits"></a>Ограничения
* В настоящее время результаты запросов ограничены данными за последнюю неделю.
* Тестируемые браузеры: последние выпуски Chrome, Edge и Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Известные несовместимые расширения браузеров
* Ghostery

Отключите это расширение или используйте другой браузер.

## <a name="e-a"></a> "Непредвиденная ошибка"
![Экран непредвиденной ошибки](./media/app-insights-analytics-troubleshooting/010.png)

Произошла внутренняя ошибка во время выполнения портала — необработанное исключение.

* Очистите кэш браузера. 

## <a name="e-b"></a>403… попробуйте перезагрузить
![403... Попробуйте перезагрузить](./media/app-insights-analytics-troubleshooting/020.png)

Произошла ошибка, связанная с проверкой подлинности (во время проверки подлинности или во время создания маркера доступа). Возможно, для восстановления портала потребуется изменение параметров браузера.

* Убедитесь, что в браузере [включены сторонние файлы cookie](#cookies) . 

## <a name="authentication"></a>403… проверьте зону безопасности
![403... Проверьте зону безопасности](./media/app-insights-analytics-troubleshooting/030.png)

Произошла ошибка, связанная с проверкой подлинности (во время проверки подлинности или во время создания маркера доступа). Возможно, для восстановления портала потребуется изменение параметров браузера.

1. Убедитесь, что в браузере [включены сторонние файлы cookie](#cookies) . 
2. Вы использовали элемент избранного, закладку или сохраненную ссылку для открытия портала аналитики? Вы вошли, используя не те учетные данные, которые использовались при сохранении ссылки?
3. Используйте окно браузера в закрытом или анонимном режиме (после закрытия всех окон). Необходимо будет ввести свои учетные данные. 
4. Откройте другое (стандартное) окно браузера и перейдите к [Azure](https://portal.azure.com). Выполните выход. Затем откройте ссылку и выполните вход, используя правильные учетные данные.
5. У пользователей браузеров Microsoft Edge и Internet Explorer также может возникать эта ошибка, если параметры доверенной зоны не поддерживаются.
   
    Убедитесь, что [портал аналитики](https://analytics.applicationinsights.io) и [портал Azure Active Directory](https://portal.azure.com) находятся в одной зоне безопасности.
   
   * В Internet Explorer щелкните **Свойства браузера**, **Безопасность**, **Надежные сайты**, **Узлы**.
     
     ![Диалоговое окно "Свойства браузера", добавление сайта в список надежных сайтов](./media/app-insights-analytics-troubleshooting/033.png)
     
     Если в списке веб-сайтов есть любой из следующих URL-адресов, убедитесь, чтобы и остальные адреса также были включены:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404... Ресурс не найден
![404... Ресурс не найден](./media/app-insights-analytics-troubleshooting/040.png)

Ресурс приложения был удален из Application Insights и больше не доступен. Это может произойти, если сохранить URL-адрес на странице аналитики.

## <a name="e-e"></a>403... Нет авторизации
![403... Нет авторизации](./media/app-insights-analytics-troubleshooting/050.png)

У вас нет разрешения на открытие этого приложения в аналитике.

* Вы получили ссылку от другого пользователя? Попросите, чтобы вас внесли в список [читателей или участников этой группы ресурсов](app-insights-resources-roles-access-control.md).
* Вы сохранили ссылку, используя другие учетные данные? Откройте [портал Azure](https://portal.azure.com), выполните выход, а затем повторно щелкните эту ссылку и введите правильные учетные данные.

## <a name="html-storage"></a>403 ... Хранилище HTML5
На нашем портале используются хранилища HTML5 — localStorage и sessionStorage.

* Chrome: Settings (Параметры), Privacy (Конфиденциальность), Content settings (Параметры содержимого).
* Internet Explorer: "Свойства браузера", вкладка "Дополнительно", "Безопасность", "Включить хранилище DOM".

![403... Попробуйте включить хранилище HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Подписка не найдена
![404... Подписка не найдена](./media/app-insights-analytics-troubleshooting/070.png)

Недопустимый URL-адрес. 

* Откройте ресурс приложения на [портале Application Insights](https://portal.azure.com). Затем используйте кнопку аналитики.

## <a name="e-h"></a>404… страница не существует
![404... Страница не существует](./media/app-insights-analytics-troubleshooting/080.png)

Недопустимый URL-адрес.

* Откройте ресурс приложения на [портале Application Insights](https://portal.azure.com). Затем используйте кнопку аналитики.

## <a name="cookies"></a>Включение сторонних файлов cookie
  Узнайте, [как отключить сторонние файлы cookie](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), но обратите внимание, что их необходимо **включить** .


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

