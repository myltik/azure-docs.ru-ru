---
title: Начало работы с Azure Data Lake Analytics с помощью портала Azure | Документация Майкрософт
description: 'Узнайте, как использовать портал Azure для создания учетной записи Data Lake Analytics, создания задания Data Lake Analytics с помощью U-SQL и его отправки. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: saveenr
ms.openlocfilehash: 34967a9853f907c61494e72229d75af1c625ea8f
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Начало работы с Azure Data Lake Analytics с помощью портала Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как с помощью портала Azure создавать учетные записи Azure Data Lake Analytics, определять задания в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять их в службу Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем приступать к изучению этого руководства, необходимо оформить **подписку Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных

Теперь мы одновременно создадим учетные записи Data Lake Analytics и Data Lake Store.  Этот простой шаг занимает около минуты.

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Последовательно выберите **Создать ресурс** >  **Данные и аналитика** > **Data Lake Analytics**.
3. Выберите значения для следующих параметров:
   * **Имя**: имя учетной записи Data Lake Analytics (разрешены только строчные буквы и цифры).
   * **Подписка**: выберите подписку Azure, которая используется для учетной записи аналитики.
   * **Группа ресурсов**: выберите существующую группу ресурсов Azure или создайте новую группу. Обычно приложения состоят из множества компонентов, например веб-приложения, базы данных, сервера базы данных, хранилища и служб сторонних поставщиков.
   * **Расположение.** выберите центр обработки данных Azure для учетной записи аналитики озера данных.
   * **Data Lake Store.** Следуйте инструкциям для создания учетной записи Data Lake Store или выберите имеющуюся. 
4. При необходимости выберите ценовую категорию для учетной записи Data Lake Analytics.
5. Нажмите кнопку **Создать**. 


## <a name="your-first-u-sql-script"></a>Первый скрипт U-SQL

Ниже приводится очень простой скрипт U-SQL. Он определяет небольшой набор данных (в рамках скрипта) и записывает его в Azure Data Lake Store по умолчанию как файл с именем `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Отправка задания U-SQL

1. В учетной записи Data Lake Analytics щелкните **Новое задание**.
2. Вставьте в текст скрипт U-SQL, представленный выше. 
3. Щелкните **Отправить задание**.   
4. Подождите, пока состояние задания не изменится на **Успешно**.
5. Перейдите на вкладку **Выходные данные** и щелкните `data.csv`. 

## <a name="see-also"></a>См. также

* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
