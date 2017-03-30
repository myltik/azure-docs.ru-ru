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
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Создание первой функции на портале Azure

В этом разделе объясняется, как создать простую функцию Azure под названием hello world, которая вызывается с помощью HTTP-запроса. Перед созданием функции на портале Azure необходимо создать приложение-функцию в службе приложений Azure, чтобы выполнять в нем свою функцию.

Для работы с этим кратким руководством необходима учетная запись Azure. Вы можете воспользоваться [бесплатной учетной записью](https://azure.microsoft.com/free/). Также вы можете [работать с Функциями Azure](https://azure.microsoft.com/try/app-service/functions/) без регистрации в Azure.

## <a name="create-a-function-app"></a>Создание приложения-функции

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Дополнительные сведения см. в разделе [Создание приложения-функции на портале Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Создание функции
Следуйте инструкциям ниже, чтобы создать функцию в новом приложении-функции с помощью вкладки "Быстрый запуск" в Функциях Azure.

1. На вкладке **Быстрый запуск** щелкните **WebHook + API**, выберите язык для функции, а затем щелкните **Создать функцию**. Будет создана предопределенная функция на выбранном вами языке.  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (Необязательно) На этом этапе быстрой настройки вы можете бегло ознакомиться с возможностями функций Azure на портале. Завершив (или пропустив) этот шаг, вы можете проверить новую функцию, отправив HTTP-запрос.


## <a name="test-the-function"></a>Проверка функции
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


