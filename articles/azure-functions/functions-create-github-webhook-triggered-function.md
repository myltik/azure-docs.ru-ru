---
title: "Создание функции, активируемой объектом webhook GitHub, в Azure | Документация Майкрософт"
description: "Создавайте независимые от сервера функции, активируемые объектом webhook GitHub, с помощью Функций Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Создание функции, активируемой объектом webhook GitHub

Узнайте, как создать функцию, активируемую объектом webhook GitHub. 

![Создание приложения-функции на портале Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

Здесь требуются ресурсы, созданные при работе со статьей [Создание первой функции Azure](functions-create-first-azure-function.md).

Вам также необходима учетная запись GitHub. Если у вас ее нет, вы можете [зарегистрироваться для получения бесплатной учетной записи GitHub](https://github.com/join). 

Выполнение всех шагов в этой статье займет меньше пяти минут.

## <a name="find-your-function-app"></a>Поиск приложения-функции    

1. Войдите на [портал Azure](https://portal.azure.com/). 

2. На панели поиска в верхней части портала введите имя приложения-функции и выберите его в списке.

## <a name="create-function"></a>Создание функции, активируемой объектом webhook GitHub

1. В приложении-функции нажмите кнопку **+** рядом с пунктом **Функции**, щелкните шаблон **GitHubWebHook** для нужного языка и выберите **Создать**.
   
    ![Создание функции, активируемой объектом webhook GitHub, на портале Azure](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Щелкните **</> Get function URL** (</> Получить URL-адрес функции), а затем скопируйте и сохраните значения. Сделайте то же самое для **получения секрета GitHub**. Эти значения используются для настройки объекта webhook на сайте GitHub. 

    ![Просмотр кода функции](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Затем создайте объект webhook в репозитории GitHub. 

## <a name="configure-the-webhook"></a>Настройка объекта webhook
1. На сайте GitHub перейдите в свой репозиторий. Вы можете использовать любые репозитории, для которых создали ответвления.
 
2. Щелкните **Параметры**, **Веб-перехватчики**, а затем — **Добавить веб-перехватчик**.
   
    ![Добавление объекта webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Вставьте URL-адрес и секрет функции в поле **Payload URL** (URL-адрес полезных данных) и **Секрет**, а для параметра **Тип содержимого** выберите значение **application/json**.

4. Установите переключатель **Let me select individual events** (Выбрать отдельные события), выберите **Issue comment** (Примечание к вопросу) и щелкните **Add webhook** (Добавить объект webhook).
   
    ![Задание URL-адреса объекта webhook и секрета](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Теперь объект webhook настраивается для активации функции при добавлении нового примечания к вопросу. 

## <a name="test-the-function"></a>Проверка функции
1. В репозитории GitHub откройте вкладку **Issues** (Вопросы) в новом окне браузера,

2. щелкните **New Issue** (Создать вопрос), введите название, а затем щелкните **Submit new issue** (Отправить новый вопрос). 

2. В вопросе введите комментарий и нажмите кнопку **Comment**(Добавить примечание). 

3. В другом окне GitHub щелкните **Edit** (Изменить) рядом с новым объектом webhook, прокрутите к разделу **Recent Deliveries** (Последние доставленные объекты) и убедитесь, что текущая функция обработала запрос объекта webhook. 
 
    ![Задание URL-адреса объекта webhook и секрета](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   Ответ функции должен содержать `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


