---
title: Использование машинного обучения Azure с хранилищем данных SQL | Документация Майкрософт
description: Учебник по использованию машинного обучения Azure с хранилищем данных SQL Azure для разработки решений.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: ''
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31435145"
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Использование машинного обучения Azure с хранилищем данных SQL
Машинное обучение Azure — это полностью управляемая служба прогнозной аналитики, которая позволяет создавать прогнозные модели с данными из хранилища данных SQL и публиковать их в виде готовых веб-служб. Ознакомиться с основами прогнозной аналитики и машинного обучения можно в статье [Введение в машинное обучение в облаке][Introduction to Machine Learning on Azure].  Сведения о создании, обучении, оценке и тестировании модели машинного обучения см. в статье [Руководство по машинному обучению. Создание первого эксперимента по обработке и анализу данных в Студии машинного обучения Azure][Create experiment tutorial].

В этой статье вы узнаете, как выполнить следующие задачи с помощью [Студии машинного обучения Microsoft Azure][Azure Machine Learning Studio]:

* Чтение данных из базы данных для создания, обучения и оценки прогнозной модели
* Запись данных в базу данных

## <a name="read-data-from-sql-data-warehouse"></a>Чтение данных из хранилища данных SQL
Данные будут получены из таблицы Product в базе данных AdventureWorksDW.

### <a name="step-1"></a>Шаг 1
Запустите новый эксперимент, щелкнув "+СОЗДАТЬ" в нижней части окна студии машинного обучения, и выберите "ЭКСПЕРИМЕНТ", а затем "Пустой эксперимент". В верхней части области выберите имя эксперимента по умолчанию и присвойте ему понятное имя, например "Прогнозирование цен на велосипеды".

### <a name="step-2"></a>Шаг 2.
Найдите модуль чтения в палитре наборов данных и модулей слева от области эксперимента. Перетащите модуль в область эксперимента.
![][drag_reader]

### <a name="step-3"></a>Шаг 3.
Выберите модуль чтения и заполните область свойств.

1. Выберите базу данных SQL Azure в качестве базы данных-источника.
2. Имя сервера баз данных: введите имя сервера. Эти сведения можно узнать на [портале Azure][Azure portal].

![][server_name]

1. Имя базы данных: введите имя базы данных на сервере, который только что был указан.
2. Имя учетной записи пользователя сервера: введите имя пользователя учетной записи, которая имеет разрешения на доступ к базе данных.
3. Пароль учетной записи пользователя сервера: введите пароль для учетной записи указанного пользователя.
4. Принимать любые сертификаты сервера: используйте этот параметр (не рекомендуется), если вы хотите пропускать просмотр сертификата веб-сайта перед чтением данных.
5. Запрос к базе данных: введите инструкцию SQL, описывающую данные, которые необходимо получить. В этом случае данные будут считаны из таблицы Product с помощью следующего запроса.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Шаг 4.
1. Запустите эксперимент, щелкнув "Пуск" под областью эксперимента.
2. После выполнения эксперимента у модуля чтения должен появиться зеленый флажок, означающий успешное завершение. Обратите также внимание на состояние Работа завершена в правом верхнем углу.

![][run]

1. Чтобы просмотреть импортированные данные, щелкните порт вывода в нижней части набора данных по автомобилям и выберите "Показать".

## <a name="create-train-and-score-a-model"></a>Создание, обучение и оценка модели
Теперь этот набор данных можно использовать для выполнения следующих задач.

* Создание модели: обработка данных и задание параметров
* Обучение модели: выбор и применение алгоритма обучения
* Оценка и тестирование модели: прогнозирование новой цены велосипедов

![][model]

Сведения о создании, обучении, оценке и тестировании модели машинного обучения см. в статье [Руководство по машинному обучению. Создание первого эксперимента по обработке и анализу данных в Студии машинного обучения Azure][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Запись данных в хранилище данных SQL Azure
Мы запишем результирующий набор в таблицу ProductPriceForecast в базе данных AdventureWorksDW.

### <a name="step-1"></a>Шаг 1
Найдите модуль записи в палитре наборов данных и модулей слева от области эксперимента. Перетащите модуль в область эксперимента.

![][drag_writer]

### <a name="step-2"></a>Шаг 2
Выберите модуль записи и заполните панель свойств.

1. Выберите базу данных SQL Azure в качестве целевой базы данных.
2. Имя сервера баз данных: введите имя сервера. Эти сведения можно узнать на [портале Azure][Azure portal].
3. Имя базы данных: введите имя базы данных на сервере, который только что был указан.
4. Имя учетной записи пользователя сервера: введите имя пользователя учетной записи, которая имеет разрешение на запись в базу данных.
5. Пароль учетной записи пользователя сервера: введите пароль для учетной записи указанного пользователя.
6. Принимать любые сертификаты сервера (не рекомендуется): выберите этот параметр, чтобы не просматривать сертификаты.
7. Разделенный запятыми список столбцов, которые нужно сохранить: список набора данных или результирующие столбцы, которые нужно вывести.
8. Имя таблицы данных: укажите имя таблицы данных.
9. Разделенный запятыми список столбцов таблицы данных: укажите имя столбца для использования в новой таблице. Имена столбцов должны отличаться от имен столбцов в исходном наборе данных, однако следует использовать такое же количество столбцов, что и в таблице вывода.
10. Количество строк, записываемых в одной операции SQL Azure: можно настроить количество строк, которые записываются в базу данных SQL в одной операции.

![][writer_properties]

### <a name="step-3"></a>Шаг 3.
1. Запустите эксперимент, щелкнув "Пуск" под областью эксперимента.
2. После выполнения эксперимента у всех модулей должен появиться зеленый флажок, означающий их успешное завершение.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
