---
title: Квота Azure Data Lake Analytics
description: Узнайте, как настроить или увеличить квоту в учетных записях Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Аналитика озера данных Azure
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: c6c39fb0810a7ea8b6facec1ca80da25d2253329
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311137"
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Квота Azure Data Lake Analytics

Узнайте, как настроить или увеличить квоту в учетных записях Azure Data Lake Analytics (ADLA). Зная ее ограничения, вы сможете понять поведение задания U-SQL. Все квоты являются мягкими, то есть максимальное ограничение можно увеличить, обратившись в службу поддержки Azure.

## <a name="azure-subscriptions-limits"></a>Ограничения подписок Azure

**Максимальное количество учетных записей Azure Data Lake Analytics в одной подписке:** 5

Если вы попытаетесь создать шестую учетную запись Azure Data Lake Analytics, то увидите сообщение о том, что достигнуто максимальное количество учетных записей Data Lake Analytics (5) для текущего региона и подписки. 

Если вы хотите выйти за рамки этого ограничения, можно воспользоваться этими вариантами:
* Выберите другой подходящий регион.
* [Отправьте запрос в службу поддержки Azure](#increase-maximum-quota-limits) с запросом на увеличение квоты.

## <a name="adla-account-limits"></a>Ограничения учетной записи Azure Data Lake Analytics

**Максимальное количество единиц аналитики на учетную запись:** 250.

Это максимальное число единиц аналитики, которые могут выполняться параллельно в вашей учетной записи. Если общее количество единиц аналитики по всем выполняемым заданиям превысит это ограничение, новые задания будут автоматически помещаться в очередь. Например: 

* Если выполняется только одно задание на 250 единиц аналитики, то при отправке второго задания оно будет помещено в очередь, пока не завершится первое задание.
* Если выполняются пять заданий, каждое из которых использует по 50 единиц аналитики, то при отправке шестого задания, на которое нужно 20 единиц аналитики, оно будет ожидать в очереди заданий, пока не освободятся эти 20 единиц аналитики.

**Максимальное число одновременно выполняемых заданий U-SQL на учетную запись:** 20.

Это максимальное число заданий, которые могут выполняться параллельно в вашей учетной записи. При достижении этого значения новые задания автоматически помещаются в очередь.

## <a name="adjust-adla-quota-limits-per-account"></a>Корректировка квот Azure Data Lake Analytics для учетной записи

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Выберите существующую учетную запись ADLA.
3. Щелкните **Свойства**.
4. Настройте значения параметров **Параллелизм** и **Параллельные задания** с учетом своих потребностей.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Увеличение максимальной квоты

1. Создайте запрос на поддержку на портале Azure.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Для типа проблемы укажите **Квота**.
3. Выберите **подписку**, которая не является пробной.
4. Выберите тип квоты **Data Lake Analytics**.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. На странице проблемы опишите необходимое увеличение квоты, а в поле **Сведения** укажите причины для такого увеличения.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Проверьте введенные контактные данные и создайте запрос в службу поддержки.

Корпорация Майкрософт проверит запрос и удовлетворит потребности вашего бизнеса как можно скорее.

## <a name="next-steps"></a>Дополнительная информация

* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Управление аналитикой озера данных Azure с помощью Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
