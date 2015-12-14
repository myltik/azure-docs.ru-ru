<properties
   pageTitle="Работа со службой Azure Stream Analytics и хранилищем данных SQL | Microsoft Azure"
   description="Советы по использованию службы Azure Stream Analytics и хранилища данных SQL для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/30/2015"
   ms.author="sahajs;twounder"/>

# Работа со службой Azure Stream Analytics и хранилищем данных SQL

Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Базовую информацию см. в разделе [Основные сведения о службе Azure Stream Analytics][]. Вы также можете узнать о создании комплексного решения с помощью Stream Analytics в учебнике [Приступая к работе с Azure Stream Analytics][].

В этой статье вы научитесь выводить данные для заданий Stream Analytics с помощью базы данных хранилища данных SQL Azure.

## Предварительные требования

Сначала необходимо выполнить следующие шаги, описанные в учебнике [Приступая к работе с Azure Stream Analytics][].

1. Создание ввода концентратора событий
2. Настройка и запуск приложения генератора событий
3. Подготовка задания Stream Analytics
4. Указание входных данных задания и запроса

Затем создание базы данных хранилища данных SQL Azure

## Указание выходных данных задания: база данных хранилища данных SQL Azure

### Шаг 1.

В задании Stream Analytics щелкните **ВЫХОДНЫЕ ДАННЫЕ** в верхней части страницы, а затем выберите пункт **ДОБАВИТЬ ВЫХОДНЫЕ ДАННЫЕ**.

### Шаг 2

Выберите базу данных SQL и нажмите кнопку «Далее».

![][add-output]

### Шаг 3.
Введите следующие значения на следующей странице:

- *Псевдоним выходных данных*: введите понятное имя для выходных данных этого задания.
- *Подписка*:
	- Если ваша база данных хранилища данных SQL принадлежит к той же подписке, что и задание Stream Analytics, выберите параметр "Использовать базу данных SQL из текущей подписки".
	- Если ваша база данных находится в другой подписке, выберите параметр "Использовать базу данных SQL из другой подписки".
- *База данных*: укажите имя целевой базы данных.
- *Имя сервера*: укажите имя сервера для указанной базы данных. Эту информацию можно получить на классическом портале Azure.

![][server-name]

- *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
- *Пароль*: укажите пароль для учетной записи указанного пользователя.
- *Таблица*: укажите имя целевой таблицы в базе данных.

![][add-database]

### Шаг 4.

Нажмите кнопку "Проверка", чтобы добавить выходные данные этого задания и убедиться, что Stream Analytics может подключиться к базе данных.

![][test-connection]

После подключения к базе данных в нижней части портала появится уведомление. Нажмите кнопку "Проверка соединения" внизу, чтобы проверить соединение с базой данных.

## Дальнейшие действия

Общие сведения об интеграции см. в разделе [Обзор интеграции хранилища данных SQL][].

Дополнительные советы по разработке см. в разделе [Общие сведения о разработке для хранилища данных SQL][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Основные сведения о службе Azure Stream Analytics]: stream-analytics-introductiond.md
[Приступая к работе с Azure Stream Analytics]: stream-analytics-get-started.md
[Общие сведения о разработке для хранилища данных SQL]: sql-data-warehouse-overview-develop.md
[Обзор интеграции хранилища данных SQL]: sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=AcomDC_1203_2015-->