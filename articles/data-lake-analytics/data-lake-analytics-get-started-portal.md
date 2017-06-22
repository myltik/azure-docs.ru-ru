---
title: "Начало работы с Azure Data Lake Analytics с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как использовать портал Azure для создания учетной записи Data Lake Analytics, создания задания Data Lake Analytics с помощью U-SQL и его отправки. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как с помощью портала Azure создавать учетные записи Azure Data Lake Analytics, определять задания в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять их в службу Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступать к изучению этого руководства, необходимо оформить **подписку Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных

Теперь мы одновременно создадим учетные записи Data Lake Analytics и Data Lake Store.  Этот простой шаг занимает около минуты.

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Щелкните **Создать** >  **Аналитика** > **Data Lake Analytics**.
3. Выберите значения для следующих параметров:
   * **Имя**: имя учетной записи Data Lake Analytics (разрешены только строчные буквы и цифры).
   * **Подписка**: выберите подписку Azure, которая используется для учетной записи аналитики.
   * **Группа ресурсов**: выберите существующую группу ресурсов Azure или создайте новую группу. Обычно приложения состоят из множества компонентов, например веб-приложения, базы данных, сервера базы данных, хранилища и служб сторонних поставщиков.
   * **Расположение.** выберите центр обработки данных Azure для учетной записи аналитики озера данных.
   * **Data Lake Store.** Следуйте инструкциям для создания учетной записи Data Lake Store или выберите имеющуюся. 
4. При необходимости выберите ценовую категорию для учетной записи Data Lake Analytics.
5. Щелкните **Создать**. 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Создание и отправка заданий аналитики озера данных
После подготовки исходных данных можно приступать к разработке скрипта U-SQL.  

**Отправка задания**

1. В учетной записи Data Lake Analytics щелкните **Новое задание**.
2. Введите **Имя задания**и следующий скрипт U-SQL:

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


Этот сценарий U-SQL считывает файл исходных данных с помощью **Extractors.Tsv()**, а затем создает CSV-файл с помощью **Outputters.Csv()**.

1. Щелкните **Отправить задание**.   
2. Подождите, пока состояние задания не изменится на **Успешно**.
3. Если задание завершилось сбоем, см. статью [Устранение неполадок с заданиями Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
4. Перейдите на вкладку **Выходные данные** и щелкните `SearchLog-from-Data-Lake.csv`. 

## <a name="see-also"></a>См. также

* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).

