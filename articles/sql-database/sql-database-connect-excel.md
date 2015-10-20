<properties
	pageTitle="Подключение к Базе данных SQL Azure с помощью Excel"
	description="Электронная таблица Excel для Базы данных SQL Azure для создания отчетов и просмотра данных."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="joseidz"/>


# Подключение к базе данных SQL Azure с помощью Excel

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

В этой статье описывается процесс подключения Excel к базе данных SQL Azure и создания отчета по данным в базе данных. Для начала вам нужна база данных SQL. Если у вас нет базы данных, см. статью [Создание первой базы данных SQL](sql-database-get-started.md), чтобы настроить базу данных с демонстрационными данными и запустить ее в течение нескольких минут. В этой статье используются демонстрационные данные из статьи «Создание первой базы данных SQL», но вы можете выполнять те же действия, используя собственные данные.

Вам также понадобится копия Excel. В этой статье используется [Microsoft Excel 2016](https://products.office.com/ja-jp/).

## Подключение и создания отчета

1.	Откройте Excel и создайте книгу или откройте книгу, к которой вы хотите подключиться.

2.	В строке меню в верхней части страницы последовательно щелкните элементы **Данные**, **Из других источников** и **Из SQL Server**.
	
	![Выберите источник данных](./media/sql-database-connect-excel/excel_data_source.png)

	Откроется мастер подключения данных.

3.	В диалоговом окне **Подключение к серверу базы данных** введите **Имя сервера**, на котором находится логический сервер, в формате **<*имя\_сервера*>.database.windows.net**. Например, **adventureserver.database.windows.net**.

4.	В разделе **Учетные данные для входа** щелкните параметр **Использовать следующие имя пользователя и пароль**, введите **Имя пользователя** и **Пароль**, которые вы установили для базы данных во время ее создания, и нажмите кнопку **Далее**.

	> [AZURE.TIP]Надстройки [Сводная диаграмма](https://www.microsoft.com/download/details.aspx?id=102) и [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) для Excel очень похожи в работе.

5. В диалоговом окне **Выбор базы данных и таблицы** выберите в раскрывающемся меню базу данных **AdventureWorks**, а в списке таблиц и представлений элемент **vGetAllCategories**, после чего нажмите кнопку **Далее**.

	![Выберите базу данных и таблицу][5]

6. В диалоговом окне **Сохранение файла подключения данных и завершение** нажмите кнопку **Завершить**.

7. В диалоговом окне **Импорт данных** выберите **Сводная диаграмма** и нажмите кнопку **ОК**.

	![Выберите данные для импорта][2]

8. В диалоговом окне **Поля сводной диаграммы** выберите следующую конфигурацию, чтобы создать отчет по количеству продуктов в категории.

	![Конфигурация][3]

	У вас должен получиться следующий результат:

	![Успешное завершение][4]

## Дальнейшие действия

Если вы разрабатываете программное обеспечение как услугу (SaaS), ознакомьтесь с информацией о [пулах эластичных баз данных](sql-database-elastic-pool.md). Вы легко можете управлять крупными коллекциями баз данных с помощью [заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Oct15_HO3-->