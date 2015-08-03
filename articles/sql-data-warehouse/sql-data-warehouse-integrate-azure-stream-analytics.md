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
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Работа со службой Azure Stream Analytics и хранилищем данных SQL

Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Базовую информацию см. в разделе [Основные сведения о службе Azure Stream Analytics][]. Вы также можете узнать о создании комплексного решения с помощью Stream Analytics в учебнике [Начало работы][].

В этой статье вы научитесь выводить данные для заданий Stream Analytics с помощью базы данных хранилища данных SQL Azure.

## Предварительные требования

Сначала выполните следующие действия в учебнике [Начало работы][].

1. Создание ввода концентратора событий
2. Настройка и запуск приложения генератора событий
3. Подготовка задания Stream Analytics
4. Указание входных данных задания и запроса

Затем создание базы данных хранилища данных SQL Azure

## Указание выходных данных задания: база данных хранилища данных SQL Azure

### Шаг 1.
В задании Stream Analytics щелкните **ВЫХОДНЫЕ ДАННЫЕ** в верхней части страницы, а затем выберите пункт **ДОБАВИТЬ ВЫХОДНЫЕ ДАННЫЕ**.

### Шаг 2.
Выберите базу данных SQL и нажмите кнопку "Далее". ![][Add Output]

### Шаг 3.
На следующей странице введите следующие значения. Псевдоним выходных данных: укажите понятное имя для выходных данных этого задания. Подписка: если база данных хранилища данных SQL находится в той же подписке, что и задание Stream Analytics, выберите "Использовать базу данных SQL из текущей подписки". Если база данных находится в другой подписке, выберите "Использовать базу данных SQL из другой подписки". База данных: укажите имя целевой базы данных. Имя сервера: укажите имя сервера для выбранной базы данных. Эти сведения можно узнать на портале Azure.

![][Server Name]

- Имя пользователя: укажите имя пользователя учетной записи, которая имеет разрешение на запись в базе данных.
- Пароль: пароль для учетной записи указанного пользователя.
- Таблица: укажите имя целевой таблицы в базе данных.

![][Add Database]

### Шаг 4.
Нажмите кнопку "Проверка", чтобы добавить выходные данные этого задания и убедиться, что Stream Analytics может подключиться к базе данных.

![][Test Connection]

После подключения к базе данных в нижней части портала появится уведомление. Нажмите кнопку "Проверка соединения" внизу, чтобы проверить соединение с базой данных.




## Дальнейшие действия
Общие сведения об интеграции см. в разделе [Обзор интеграции хранилища данных SQL][]. Дополнительные советы по разработке см. в разделе [Общие сведения о разработке для хранилища данных SQL][].

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Основные сведения о службе Azure Stream Analytics]: ./stream-analytics-introduction/
[Начало работы]: ./articles/stream-analytics-get-started/
[Общие сведения о разработке для хранилища данных SQL]: ./sql-data-warehouse-overview-develop/
[Обзор интеграции хранилища данных SQL]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO4-->