---
title: "Создание первой функции в Azure с помощью Visual Studio | Документация Майкрософт"
description: "Создание и публикация в Azure простой функции, активируемой HTTP, с помощью инструментов функций Azure для Visual Studio."
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, вычисления, независимая архитектура"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/05/2017
ms.author: rachelap, glenga
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: be7a9979ba7e6aa26c60b24bcc892ca35af3c1fc
ms.contentlocale: ru-ru
ms.lasthandoff: 07/19/2017

---
# <a name="create-your-first-function-using-visual-studio"></a>Создание первой функции с помощью Visual Studio

Функции Azure позволяют вам выполнять свой код в бессерверной среде без необходимости создавать виртуальную машину или публиковать веб-приложение.

> [!IMPORTANT]
> Чтобы выполнить эти действия, в этой статье используется предварительная версия Visual Studio. Прежде чем приступить к работе убедитесь, что у вас установлена [Visual Studio 2017 (предварительная версия 15.3)](https://www.visualstudio.com/vs/preview/).

Изучив эту статью, вы научитесь использовать инструменты функций Azure для Visual Studio 2017 и локально тестировать функцию hello world. Затем вы опубликуете код функции в Azure.

![Код функций Azure в проекте Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством установите следующие компоненты.

* [Visual Studio 2017 (предварительная версия 15.3)](https://www.visualstudio.com/vs/preview/), включая рабочую нагрузку **разработки в Azure**.

    ![Установка Visual Studio 2017 с рабочей нагрузкой разработки в Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-functions-tools-for-visual-studio-2017"></a>Установка инструментов функций Azure для Visual Studio 2017

Перед началом работы скачайте и установите инструменты функций Azure для Visual Studio 2017. Эти инструменты могут использоваться только с Visual Studio 2017 (предварительная версия 15.3) или более поздней версией. Если вы уже установили инструменты функций Azure, этот раздел можно пропустить.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Создание проекта функций Azure в Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

После создания проекта можно приступать к созданию первой функции.

## <a name="create-the-function"></a>Создание функции

Щелкните правой кнопкой мыши узел проекта в **обозревателе решений** и выберите **Добавить** > **Новый элемент**. Выберите **Функция Azure** и нажмите кнопку **Добавить**.

Выберите **HttpTrigger**, укажите **имя функции**, выберите для параметра **Права доступа** значение **Анонимно** и нажмите кнопку **Создать**. Созданная функция доступна для HTTP-запроса из любого клиента. 

![Создание функции Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Созданную функцию, активируемую HTTP, можно протестировать на локальном компьютере.

## <a name="test-the-function-locally"></a>Локальное тестирование функции

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.  

![Локальная среда выполнения Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

 Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `&name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на локальный запрос GET, возвращаемый функцией: 

![Ответ функции localhost в браузере](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

Чтобы остановить отладку, нажмите кнопку **Остановить** на панели инструментов Visual Studio.

Убедитесь, что функция правильно работает на локальном компьютере. Затем опубликуйте проект в Azure.

## <a name="publish-the-project-to-azure"></a>Публикация проекта в Azure

Перед публикацией проекта убедитесь, что в вашей подписке Azure есть приложения-функция. Можно создать приложение-функцию непосредственно в Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Тестирование функции в Azure

Скопируйте базовый URL-адрес приложения-функции на странице профиля публикации. Замените часть `localhost:port` URL-адреса, который использовался при локальной проверке функции новым базовым URL-адресом. Как и ранее, добавьте строку запроса `&name=<yourname>` в этот URL-адрес и выполните запрос.

URL-адрес, который вызывает функцию, активируемую HTTP, выглядит так:

    http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. Ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией: 

![Ответ функции в браузере](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>Дальнейшие действия

С помощью Visual Studio вы создали приложение-функцию C# с простой функцией, активируемой HTTP. 

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о локальном тестировании и отладке с помощью основных инструментов функций Azure см. в статье [Как программировать и тестировать функции Azure в локальной среде](functions-run-local.md). Дополнительные сведения о разработке функций, таких как библиотек классов .NET, см. в статье [Using .NET class libraries with Azure Functions](functions-dotnet-class-library.md) (Использование библиотек классов .NET с помощью Функций Azure). 


