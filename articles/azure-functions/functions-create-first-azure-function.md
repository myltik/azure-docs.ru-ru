---
title: "Создание первой функции на портале Azure | Документация Майкрософт"
description: "Узнайте, как создать первую функцию Azure, выполняемую без сервера, с помощью портала Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: abd508631787ba5f839a4ae2ea82e76c4bfab425
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Создание первой функции на портале Azure

Функции Azure позволяют вам выполнять свой код в бессерверной среде без необходимости создавать виртуальную машину или публиковать веб-приложение. В этой статье вы узнаете, как создать функцию Hello World на портале Azure с помощью Функций.

![Создание приложения-функции на портале Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Выполнение всех шагов в этой статье займет меньше пяти минут.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, позволяющее группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Затем создайте функцию в новом приложении-функции.

## <a name="create-function"></a>Создание функции, активируемой HTTP

1. Разверните новое приложение-функцию, нажмите кнопку **+** рядом с **Функции**.

2.  На странице **Get started quickly** (Быстрое начало работы) щелкните **Webhook + API**, выберите язык для функции и щелкните **Создать функцию**. 
   
    ![Быстрое начало работы с Функциями на портале Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

С помощью шаблона функции, активируемой HTTP, будет создана функция на выбранном вами языке. Вы можете запустить новую функцию, отправив HTTP-запрос.

## <a name="test-the-function"></a>Проверка функции

1. В новой функции щелкните **< /> Получить URL-адрес функции** и скопируйте **URL-адрес функции**. 

    ![Копирование URL-адреса функции с портала Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `&name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на запрос GET, возвращаемый функцией:

    ![Ответ функции в браузере.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    URL-адрес запроса включает ключ, который по умолчанию необходим для доступа к функции по протоколу HTTP.   

## <a name="view-the-function-logs"></a>Просмотр журналов функции 

При выполнении функции сведения о трассировке записываются в журналы. Для просмотра выходных данных трассировки из предыдущего выполнения вернитесь к своей функции на портале и щелкните стрелку вверх в нижней части экрана, чтобы развернуть **Журналы**. 

![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы создали приложение-функцию с простой функцией, активируемой HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения см. в статье [Привязки HTTP и webhook в функциях Azure](functions-bindings-http-webhook.md).




