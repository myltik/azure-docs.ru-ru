---
title: "Создание обработчика webhook или функции API Azure | Документация Майкрософт"
description: "Используйте Функции Azure для создания функции, инициируемой обработчиком webhook или вызовом API."
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
ms.date: 08/30/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9484a637a1876f2cae644e43986bd5ef3201da1e


---
# <a name="create-a-webhook-or-api-azure-function"></a>Создание функции Azure объекта webhook или API
Функции Azure — это ориентированная на события среда вычислений по требованию, которая позволяет создавать выполняемые по расписанию или активируемые блоки кода, реализованные с помощью разных языков программирования. Дополнительные сведения о функциях Azure см. в статье [Обзор функций Azure](functions-overview.md).

В этом разделе демонстрируется создание функции Node.js, вызываемой объектом webhook GitHub. Новая функция создается на основе стандартного шаблона на портале функций Azure. Вы также можете ознакомиться с коротким видео, чтобы увидеть, как эти действия выполняются на портале.

## <a name="watch-the-video"></a>Просмотреть видео
В этом видео показано, как выполнять основные шаги, описанные в этом руководстве. 

>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="create-a-webhooktriggered-function-from-the-template"></a>Создание активируемой объектом webhook функции на основе шаблона
Выполнение функций в Azure происходит с помощью приложения функций. Для создания функций необходима активная учетная запись Azure. Если у вас ее нет, воспользуйтесь [бесплатной учетной записью Azure](https://azure.microsoft.com/free/). 

1. Перейдите на [портал функций Azure](https://functions.azure.com/signin) и войдите, используя свою учетную запись Azure.
2. Если у вас есть готовое приложение функций, выберите его из списка **приложений функций** и нажмите кнопку **Открыть**. Чтобы создать приложение-функцию, введите уникальное **имя** нового приложения-функции или воспользуйтесь созданным. Выберите предпочтительный **регион**, а затем щелкните **Создать и начать работу**. 
3. В приложении функций щелкните **+ Создать функцию** > **Объект Webhook GitHub - узел** > **Создать**. При этом будет создана функция с именем по умолчанию, основанная на указанном шаблоне. 
   
    ![Создание функции объекта webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 
4. На вкладке **Разработка** запишите пример функции express.js в окне **Код**. Эта функция получает запрос GitHub из объекта webhook примечания к вопросу, регистрирует текст вопроса в журнале и отправляет ответ в объект webhook как `New GitHub comment: <Your issue comment text>`.

    ![Создание функции объекта webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. Скопируйте значения **URL-адрес функции** и **Секрет GitHub**. Они потребуются вам при создании объекта webhook в GitHub. 
2. Прокрутите страницу до раздела **Запуск**, запишите стандартный текст JSON примечания к вопросу в тексте запроса, затем щелкните **Запустить**. 
   
    Новую функцию на основе шаблона всегда можно протестировать прямо на вкладке **Разработка**, указав любые ожидаемые данные текста запроса JSON и нажав кнопку **Запустить**. В этом случае шаблон включает стандартный текст примечания к вопросу. 

Затем потребуется создать фактический объект webhook в репозитории GitHub.

## <a name="configure-the-webhook"></a>Настройка объекта webhook
1. В GitHub перейдите в принадлежащий вам репозиторий (включая все разветвления репозитория).
2. Щелкните **Settings** (Параметры) > **Webhooks & services** (Объекты webhook и службы)  > **Add webhook** (Добавить объект webhook).
   
    ![Создание функции объекта webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   
3. Вставьте URL-адрес и секрет функции в поле **Payload URL** (URL-адрес полезных данных) и **Secret** (Секрет), а затем щелкните **Let me select individual events** (Выбрать отдельные события), выберите **Issue comment** (Примечание к вопросу) и щелкните **Add webhook** (Добавить объект webhook).
   
    ![Создание функции объекта webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

На этом этапе объект webhook GitHub настраивается для активации функции при добавлении нового примечания к вопросу.  
Теперь проверим его работу.

## <a name="test-the-function"></a>Проверка функции
1. В репозитории GitHub откройте вкладку **Issues** (Вопросы) в новом окне браузера, щелкните **New Issue** (Создать вопрос), введите название, а затем щелкните **Submit new issue** (Отправить новый вопрос). Можно также открыть существующий вопрос.
2. В вопросе введите комментарий и нажмите кнопку **Comment**(Добавить примечание). На этом этапе вы можете вернуться к новому объекту webhook в GitHub и в разделе **Recent Deliveries** (Последние доставленные объекты) вы увидите, что запрос объекта webhook был отправлен и текст запроса — `New GitHub comment: <Your issue comment text>`.
3. На портале функций прокрутите страницу до раздела журналов. Вы увидите, что функция была активирована и значение `New GitHub comment: <Your issue comment text>` записано в журналы потоковой передачи.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о функциях Azure см. в следующих статьях.

* [Справочник разработчика по функциям Azure](functions-reference.md)  
   Справочник программиста по созданию функций.
* [Testing Azure Functions](functions-test-a-function.md)  
   Описание различных средств и методов тестирования функций.
* [Масштабирование функций Azure](functions-scale.md)  
   Обсуждение планов обслуживания, доступных для использования с Функциями Azure (включая динамический план обслуживания), а также выбор подходящего плана.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


