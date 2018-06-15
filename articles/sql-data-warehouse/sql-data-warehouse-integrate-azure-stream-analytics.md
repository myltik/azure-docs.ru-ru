---
title: Работа со службой Azure Stream Analytics и хранилищем данных SQL | Документация Майкрософт
description: Советы по использованию службы Azure Stream Analytics и хранилища данных SQL для разработки решений.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 76cbbddca70d3bc8091dbea383213446adac533e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600314"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Работа со службой Azure Stream Analytics и хранилищем данных SQL
Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Основные сведения см. в статье [Что такое Stream Analytics?][Introduction to Azure Stream Analytics] Вы также можете узнать о создании комплексного решения с помощью Stream Analytics в руководстве [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени][Get started using Azure Stream Analytics].

В этой статье вы научитесь выводить данные для заданий Stream Analytics с помощью базы данных хранилища данных SQL Azure.

## <a name="prerequisites"></a>предварительным требованиям
Сначала необходимо выполнить приведенные ниже шаги, описанные в руководстве [Приступая к работе с Azure Stream Analytics][Get started using Azure Stream Analytics].  

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
* *Имя сервера*: укажите имя сервера для указанной базы данных. Эти сведения можно узнать на портале Azure.

![][server-name]

* *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
* *Пароль*: укажите пароль для учетной записи указанного пользователя.
* *Таблица*: укажите имя целевой таблицы в базе данных.

![][add-database]

### <a name="step-4"></a>Шаг 4.
Нажмите кнопку "Проверка", чтобы добавить выходные данные этого задания и убедиться, что Stream Analytics может подключиться к базе данных.

![][test-connection]

После подключения к базе данных в нижней части портала появится уведомление. Нажмите кнопку "Проверка соединения" внизу, чтобы проверить соединение с базой данных.

## <a name="next-steps"></a>Дополнительная информация
Общие сведения об интеграции см. в статье [Интеграция других служб с хранилищем данных SQL][SQL Data Warehouse integration overview].

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
