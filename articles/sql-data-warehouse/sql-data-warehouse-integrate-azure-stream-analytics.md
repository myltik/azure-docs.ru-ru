---
title: "Работа со службой Azure Stream Analytics и хранилищем данных SQL | Документация Майкрософт"
description: "Советы по использованию службы Azure Stream Analytics и хранилища данных SQL для разработки решений."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f0d7faede5df839c699f562b3aa21b8bb9836a3b


---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Работа со службой Azure Stream Analytics и хранилищем данных SQL
Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Основные сведения см. в статье [Введение в Azure Stream Analytics][Введение в Azure Stream Analytics] Вы также можете узнать о создании комплексного решения с помощью Stream Analytics в руководстве [Приступая к работе с Azure Stream Analytics][Приступая к работе с Azure Stream Analytics].

В этой статье вы научитесь выводить данные для заданий Stream Analytics с помощью базы данных хранилища данных SQL Azure.

## <a name="prerequisites"></a>Предварительные требования
Сначала необходимо выполнить следующие шаги, описанные в руководстве [Приступая к работе с Azure Stream Analytics][Приступая к работе с Azure Stream Analytics].  

1. Создание ввода концентратора событий
2. Настройка и запуск приложения генератора событий
3. Подготовка задания Stream Analytics
4. Указание входных данных задания и запроса

Затем создание базы данных хранилища данных SQL Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Указание выходных данных задания: база данных хранилища данных SQL Azure
### <a name="step-1"></a>Шаг 1
В задании Stream Analytics щелкните **Выходные данные** в верхней части страницы, а затем выберите пункт **Добавить выходные данные**.

### <a name="step-2"></a>Шаг 2
Выберите базу данных SQL и нажмите кнопку «Далее».

![][add-output]

### <a name="step-3"></a>Шаг 3.
Введите следующие значения на следующей странице:

* *Псевдоним выходных данных*: введите понятное имя для выходных данных этого задания.
* *Подписка*:
  * Если ваша база данных хранилища данных SQL принадлежит к той же подписке, что и задание Stream Analytics, выберите параметр "Использовать базу данных SQL из текущей подписки".
  * Если ваша база данных находится в другой подписке, выберите параметр "Использовать базу данных SQL из другой подписки".
* *База данных*: укажите имя целевой базы данных.
* *Имя сервера*: укажите имя сервера для указанной базы данных. Эту информацию можно получить на классическом портале Azure.

![][server-name]

* *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
* *Пароль*: укажите пароль для учетной записи указанного пользователя.
* *Таблица*: укажите имя целевой таблицы в базе данных.

![][add-database]

### <a name="step-4"></a>Шаг 4.
Нажмите кнопку "Проверка", чтобы добавить выходные данные этого задания и убедиться, что Stream Analytics может подключиться к базе данных.

![][test-connection]

После подключения к базе данных в нижней части портала появится уведомление. Нажмите кнопку "Проверка соединения" внизу, чтобы проверить соединение с базой данных.

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения об интеграции см. в статье [Интеграция других служб с хранилищем данных SQL][Интеграция других служб с хранилищем данных SQL].

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][Проектные решения и методики программирования для хранилища данных SQL].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Введение в Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Приступая к работе с Azure Stream Analytics]: ../stream-analytics/stream-analytics-get-started.md
[Проектные решения и методики программирования для хранилища данных SQL]:  ./sql-data-warehouse-overview-develop.md
[Интеграция других служб с хранилищем данных SQL]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Документация по Stream Analytics]: http://azure.microsoft.com/documentation/services/stream-analytics/



<!--HONumber=Nov16_HO3-->


