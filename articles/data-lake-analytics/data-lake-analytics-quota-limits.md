---
title: "Квоты Azure Data Lake Analytics | Документация Майкрософт"
description: "Узнайте, как настроить и увеличить квоту в учетной записи Azure Data Lake Analytics (ADLA)."
services: data-lake-analytics
keywords: "Аналитика озера данных Azure"
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Квота Azure Data Lake Analytics
Узнайте, как настроить и увеличить квоту в учетной записи Azure Data Lake Analytics (ADLA). Зная ее ограничения, вы сможете понять поведение задания U-SQL. Эти ограничения являются мягкими. Вы всегда можете увеличить максимальную квоту, обратившись к нам.

## <a name="azure-subscriptions-limits"></a>Ограничения подписок Azure

**Максимальное количество учетных записей Azure Data Lake Analytics на подписку:** 5. Это максимальное число учетных записей Azure Data Lake Analytics, которые можно создать, на подписку. Если попытаться создать шестую учетную запись Azure Data Lake Analytics, возникнет ошибка "Достигнуто максимальное число учетных записей Data Lake Analytics (5), разрешенных в этом регионе, в этой подписке". Это можно легко исправить, удалив использованные учетные записи Azure Data Lake Analytics в рамках подписки или обратиться к нам, открыв запрос в службу поддержки.

## <a name="adla-account-limits"></a>Ограничения учетной записи Azure Data Lake Analytics

**Максимальное количество единиц аналитики на учетную запись:** 250. 

Это максимальное число единиц аналитики, которые могут выполняться параллельно в вашей учетной записи. Общее число единиц аналитики, выполняемых во всех заданиях, не должно превышать это значение. Превышение этого значения приведет к тому, что новые задания будут автоматически помещаться в очередь. Например:

  * У вас может быть всего одно задание с 250 единицами аналитики. При отправке второго задания оно будет помещено в очередь до завершения первого.
  * У вас уже может быть 5 заданий с 50 единицами аналитики в каждом. При отправке шестого задания с 20 единицами аналитики оно будет ожидать в очереди заданий и запустится, когда освободятся 20 единиц аналитики.


**Максимальное число одновременно выполняемых заданий U-SQL на учетную запись:** 20. 

Это максимальное число заданий, которые могут выполняться параллельно в вашей учетной записи. Превышение этого значения приведет к тому, что новые задания будут автоматически помещаться в очередь.

## <a name="adjust-the-adla-quota-limits-per-account"></a>Настройка квот Azure Data Lake Analytics для учетной записи

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Выберите созданную учетную запись Azure Data Lake Analytics.
3. Щелкните **Свойства**.
4. Настройте значения параметров **Параллелизм** и **Параллельные задания** с учетом своих потребностей.

    ![Аналитика озера данных Azure: колонка на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>Чтобы увеличить максимальную квоту:

1. Откройте запрос на поддержку на портале Azure.

    ![Аналитика озера данных Azure: колонка на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Аналитика озера данных Azure: колонка на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Для типа проблемы укажите **Квота**.
3. Выберите свою **подписку**. Убедитесь, что это не пробная подписка.
4. Выберите тип квоты **Data Lake Analytics**.

    ![Аналитика озера данных Azure: колонка на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. В колонке "Проблема" укажите данные, касающиеся запроса на повышение квоты, и **сведения** относительно причины, по которой вам нужна дополнительная емкость.

    ![Аналитика озера данных Azure: колонка на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Проверьте свои контактные данные и создайте запрос на поддержку.

Корпорация Майкрософт проверит запрос и удовлетворит потребности вашего бизнеса как можно скорее.

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Управление аналитикой озера данных Azure с помощью Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

