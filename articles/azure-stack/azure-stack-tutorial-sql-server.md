---
title: Обеспечение доступности баз данных SQL для пользователей Azure Stack | Документация Майкрософт
description: Учебное пособие по установке поставщика ресурсов SQL Server и созданию предложений, позволяющих пользователям Azure Stack создавать базы данных SQL.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2017
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: f8d2dd65d9d427872fe78508ed0bcc61e644fdb0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Обеспечение доступности баз данных SQL для пользователей Azure Stack
В качестве администратора облака Azure Stack вы можете создавать предложения, позволяющие пользователям (клиентам) создавать базы данных SQL, которые они смогут использовать с собственными облачными приложениями, веб-сайтами и рабочими нагрузками. Предоставляя пользователям такие настраиваемые облачные базы данных по запросу, вы помогаете им экономить время и ресурсы. Для этого вам потребуется выполнить следующие действия:

> [!div class="checklist"]
> * Развертывание поставщика ресурсов SQL Server.
> * Создание предложения
> * Тестирование предложения.

## <a name="deploy-the-sql-server-resource-provider"></a>Развертывание поставщика ресурсов SQL Server.

Процесс развертывания подробно описан в статье [Использование баз данных SQL в Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) и состоит из следующих основных шагов.

1. [Развертывание поставщика ресурсов SQL]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [Проверка развертывания]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Указание емкости путем подключения к серверу размещения SQL.

## <a name="create-an-offer"></a>Создание предложения

1.  [Задайте квоту](azure-stack-setting-quotas.md) и присвойте ей имя *SQLServerQuota*. Выберите **Microsoft.SQLAdapter** в поле **Пространство имен**.
2.  [Создайте план](azure-stack-create-plan.md). Присвойте ему имя *TestSQLServerPlan*, выберите службу **Microsoft.SQLAdapter** и квоту **SQLServerQuota**.

    > [!NOTE]
    > Чтобы позволить пользователям создавать другие приложения, в плане могут потребоваться другие службы. Например, для Функций Azure необходимо, чтобы план содержал службу **Microsoft.Storage**, а для Wordpress — **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Создайте предложение](azure-stack-create-offer.md), назовите его **TestSQLServerOffer** и выберите план **TestSQLServerPlan**.

## <a name="test-the-offer"></a>Тестирование предложения

Теперь, когда поставщик ресурсов SQL Server развернут и приложение создано, можно войти как пользователь, подписаться на предложение и создать базу данных.

### <a name="subscribe-to-the-offer"></a>Оформление подписки на предложение
1. Войдите на портал Azure Stack (https://portal.local.azurestack.external) как клиент.
2. Нажмите кнопку **Получить подписку**, а затем введите **TestSQLServerSubscription** в поле **Отображаемое имя**.
3. Нажмите **Выберите предложение** > **TestSQLServerOffer** > **Создать**.
4. Нажмите **Больше служб** > **Подписки** > **TestSQLServerSubscription** > **Поставщики ресурсов**.
5. Нажмите кнопку **Зарегистрировать** рядом с поставщиком **Microsoft.SQLAdapter**.

### <a name="create-a-sql-database"></a>Создание базы данных SQL

1. Нажмите **+** > **Данные+хранилище** > **База данных SQL**.
2. Оставьте в полях значения по умолчанию. Можно также использовать следующие примеры:
    - **Имя базы данных**: SQLdb.
    - **Максимальный размер (в МБ)**: 100.
    - **Подписка**: TestSQLOffer.
    - **Группа ресурсов**: SQL-RG.
3. Нажмите кнопку **Login Settings (Параметры входа)**, введите учетные данные для базы данных и нажмите кнопку **ОК**.
4. Щелкните **SKU**, выберите номер SKU SQL, созданный для сервера размещения SQL, и нажмите кнопку **ОК**.
5. Нажмите кнопку **Создать**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Развертывание поставщика ресурсов SQL Server.
> * Создание предложения
> * Тестирование предложения.

Перейдите к следующему руководству, чтобы изучить дальнейшие действия:

> [!div class="nextstepaction"]
> [Обеспечение доступности веб-приложений, мобильных приложений и приложений API для пользователей Azure Stack]( azure-stack-tutorial-app-service.md)

