---
title: "Создание первой функции на портале Azure | Документация Майкрософт"
description: "Вас приветствует Azure. Создайте свою первую функцию Azure на портале Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/10/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7542280ca6dbe1a8d110155e521228d675c0d994
ms.lasthandoff: 04/12/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Создание первой функции на портале Azure

В этой статье описывается использование Функций Azure для создания функции hello world, которая вызывается с помощью HTTP-запроса. Прежде чем создавать функцию на портале Azure, вы должны создать приложение-функцию для размещения выполнения функции без обращения к серверу.

Для работы с этим кратким руководством необходима учетная запись Azure. Вы можете воспользоваться [бесплатной учетной записью](https://azure.microsoft.com/free/). Также вы можете [работать с Функциями Azure](https://azure.microsoft.com/try/app-service/functions/) без регистрации в Azure.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Создание приложения-функции

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Дополнительные сведения см. в разделе [Создание приложения-функции на портале Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Создание функции
Следуйте инструкциям ниже, чтобы создать функцию в новом приложении-функции с помощью вкладки "Быстрый запуск" в Функциях Azure.

1. Нажмите кнопку **Создать**, затем щелкните **Webhook + API**, выберите язык для функции и щелкните **Create a function** (Создать функцию). С помощью шаблона функции, активируемой HTTP, будет создана функция на выбранном языке.  
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

После создания функции вы можете проверить ее, отправив HTTP-запрос.

## <a name="test-the-function"></a>Проверка функции

Так как шаблоны функции содержат рабочий код, вы можете сразу же проверить созданную функцию прямо на портале.

1. Щелкните созданную функцию в приложении-функции и просмотрите код из шаблона. Обратите внимание, что функция ожидает HTTP-запрос со значением свойства *name*, которое передается либо в тексте сообщения, либо в строке запроса. При выполнении функции это значение возвращается в сообщении с ответом. В примере ниже представлена функция JavaScript.
   
2. Нажмите кнопку **Выполнить**, чтобы выполнить функцию. Вы увидите, что выполнение активируется тестовым HTTP-запросом, данные записываются в журналы, а ответ hello... отображается в окне **выходных данных** на вкладке **проверки**.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

3. В текстовом поле **Текст запроса** измените значение свойства *name* на свое имя и снова нажмите кнопку **Выполнить**. На этот раз ответ в окне **выходных данных** будет содержать ваше имя.   

4. Чтобы запустить выполнение этой же функции в инструменте тестирования HTTP или в другом окне браузера, щелкните **</> Get function URL** (Получить URL-адрес функции), скопируйте URL-адрес запроса и вставьте его в адресную строку браузера или средства. Добавьте значение строки запроса `&name=yourname` в URL-адрес и выполните запрос. В журналы записывается та же информация, а текст ответного сообщения содержит аналогичную строку.

    ![](./media/functions-create-first-azure-function/function-app-browser-testing.png)


## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


