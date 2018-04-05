---
title: Приступая к работе со Службами мобильного взаимодействия Azure для веб-приложений | Документация Майкрософт
description: Узнайте, как использовать Службы мобильного взаимодействия Azure с аналитическими функциями и push-уведомлениями для веб-приложений.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.openlocfilehash: bb7a0e61b6d29c292642fd950aed7617de9b340d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Приступая к работе со Службами мобильного взаимодействия Azure для веб-приложений
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как с помощью Служб мобильного взаимодействия Azure понять тенденции использования веб-приложения.

> [!NOTE]
> Мы прекратим использование Служб мобильного взаимодействия Azure в марте 2018 г. Сейчас они доступны только существующим клиентам. Дополнительные сведения см. на странице [Службы мобильного взаимодействия](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Для работы с данным учебником требуется следующее:

* Visual Studio 2015 или другой редактор по вашему выбору;
* [веб-пакет SDK](http://aka.ms/P7b453)

Сейчас веб-пакет SDK находится в предварительной версии и поддерживает только функцию аналитики. Пока он не поддерживает отправку push-уведомлений в приложении или браузере. 

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>Настройка Служб мобильного взаимодействия для веб-приложения
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных.

Мы создадим базовое веб-приложение с помощью Visual Studio, чтобы продемонстрировать интеграцию, хотя вы можете выполнить эти действия для любого веб-приложения, созданного вне Visual Studio. 

### <a name="create-a-new-web-app"></a>Создание нового веб-приложения
Указанные ниже действия предполагают использование Visual Studio 2015, хотя они и аналогичны действиям с использованием более ранних версий Visual Studio. 

1. Запустите Visual Studio и на **начальном экране** выберите пункт **Создать проект**.
2. Во всплывающем окне последовательно выберите пункты **Веб** -> **Веб-приложение ASP.NET**. Введите необходимые значения в полях **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.
3. Во всплывающем окне **Выбор шаблона** выберите **пустой шаблон** в списке **шаблонов ASP.Net 4.5** и нажмите кнопку **ОК**. 

Вы создали пустой проект веб-приложения, в которое мы интегрируем веб-пакет SDK для Служб мобильного взаимодействия Azure.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
1. Создайте новую папку с именем **javascript** в решении и добавьте в нее JS-файл веб-пакета SDK с именем **azure-engagement.js**. 
2. Добавьте в папку javascript новый файл с именем **main.js** , содержащий следующий код. Обязательно обновите строку подключения. Этот объект `azureEngagement` будет использоваться для доступа к методам веб-пакета SDK. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Использование JS-файлов в Visual Studio][1]

## <a name="enable-real-time-monitoring"></a>Включение мониторинга в режиме реального времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере одно действие в серверную часть Служб мобильного взаимодействия. Действие в контексте веб-приложения — это веб-страница. 

1. Создайте страницу с именем **home.html** в решении и настройте ее в качестве начальной страницы для веб-приложения. 
2. Включите два сценария JavaScript, добавленных ранее на этой странице. Для этого добавьте в тег body следующий код. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. Обновите тег body для вызова метода `startActivity` EngagementAgent.
   
        <body onload="engagement.agent.startActivity('Home')">
4. Вот как будет выглядеть страница **home.html** :
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>Подключение приложения с возможностью его отслеживания в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>Расширение аналитики
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

