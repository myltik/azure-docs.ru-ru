---
title: Создание первой функции на портале Azure | Документация Майкрософт
description: Узнайте, как создать первую функцию Azure, выполняемую без сервера, с помощью портала Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/05/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 31437360396e220de24789fdff9d2602af385053
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Создание первой функции на портале Azure

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/overview/serverless-computing/) среде без необходимости создавать виртуальную машину или публиковать веб-приложение. В этой статье вы узнаете, как создать функцию Hello World на портале Azure с помощью Функций.

![Создание приложения-функции на портале Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу <http://portal.azure.com> с помощью своей учетной записи Azure.

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, позволяющее группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal-experiment.md)]

Затем создайте функцию в новом приложении-функции.

## <a name="create-function"></a>Создание функции, активируемой HTTP

1. Разверните новое приложение-функцию, нажмите кнопку **+** рядом с **Функции**.

2.  На странице **быстрого начала работы** щелкните **Веб-перехватчик + API**, **выберите язык для функции** и щелкните **Создать функцию**. 
   
    ![Быстрое начало работы с Функциями на портале Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

С помощью шаблона функции, активируемой HTTP, будет создана функция на выбранном вами языке. В этом разделе показана функция скрипта C# на портале, но вы можете создать функцию на любом [поддерживаемом языке](supported-languages.md). 

Теперь вы можете запустить новую функцию, отправив HTTP-запрос.

## <a name="test-the-function"></a>Проверка функции

1. В новой функции щелкните **</> Get function URL** (Получить URL-адрес функции), выберите **default (Function key)** (По умолчанию (ключ функции)) и щелкните **Копировать**. 

    ![Копирование URL-адреса функции с портала Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Вставьте URL-адрес функции в адресную строку браузера. Добавьте значение строки запроса `&name=<yourname>` в конец этого URL-адреса и нажмите клавишу `Enter` на клавиатуре, чтобы выполнить этот запрос. В браузере должен отобразиться ответ, возращенный функцией.  

    Ниже приведен пример ответа в браузере Microsoft Edge (другие браузеры могут отображать XML):

    ![Ответ функции в браузере.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    URL-адрес запроса включает ключ, который по умолчанию необходим для доступа к функции по протоколу HTTP.   

3. При выполнении функции сведения о трассировке записываются в журналы. Для просмотра выходных данных трассировки из предыдущего выполнения вернитесь к своей функции на портале и щелкните стрелку в нижней части экрана, чтобы развернуть раздел **Журналы**. 

   ![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы создали приложение-функцию с простой функцией, активируемой HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения см. в статье [Привязки HTTP и webhook в функциях Azure](functions-bindings-http-webhook.md).



