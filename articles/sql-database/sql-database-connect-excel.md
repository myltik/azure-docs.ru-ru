<properties
	pageTitle="Подключение Excel к базе данных SQL | Microsoft Azure"
	description="Узнайте, как подключить Microsoft Excel к базе данных SQL Azure в облаке. Импортируйте данные в Excel для создания отчетов и просмотра данных."
	services="sql-database"
	keywords="подключение excel к sql, импорт данных в excel"
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
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Подключение Excel к базе данных SQL Azure и создание отчета 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Узнайте, как подключить программу Excel к базе данных SQL для импорта данных в Excel. Затем создайте отчет с данными.

Для начала вам нужна база данных SQL. Если у вас нет базы данных, см. статью [Создание первой базы данных SQL](sql-database-get-started.md), чтобы настроить базу данных с демонстрационными данными и запустить ее в течение нескольких минут. Следуя инструкциям из этой статьи, вы импортируете демонстрационные данные в Excel, но те же действия можно выполнять и с собственными данными.

Вам также понадобится копия Excel. В этой статье используется [Microsoft Excel 2016](https://products.office.com/ru-RU/).

## Подключение Excel к базе данных SQL и создание отчета

1.	Чтобы подключить Excel к базе данных SQL, откройте программу Excel и создайте новую книгу. Или откройте существующую книгу Excel, которую вы хотите подключить к базе данных SQL.

2.	В строке меню в верхней части страницы последовательно щелкните элементы **Данные**, **Из других источников** и **С сервера SQL Server**.

	![Выберите источник данных: подключите Excel к базе данных SQL.](./media/sql-database-connect-excel/excel_data_source.png)

	Откроется мастер подключения данных.

3.	В диалоговом окне **Подключение к серверу базы данных** введите **имя сервера**, на котором размещен логический сервер, к которому нужно подключиться, в формате **<*имя\_сервера*>.database.windows.net**. Например, **adventureserver.database.windows.net**.

4.	В разделе **Учетные данные для входа** щелкните **Использовать следующие имя пользователя и пароль**, введите **имя пользователя** и **пароль**, которые вы задали для сервера базы данных SQL во время его создания, и нажмите кнопку **Далее**.

	> [AZURE.TIP] Надстройки [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) и [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) для Excel очень похожи в работе.

5. В диалоговом окне **Выбор базы данных и таблицы** выберите в раскрывающемся меню базу данных **AdventureWorks**, а в списке таблиц и представлений — элемент **vGetAllCategories**, после чего нажмите кнопку **Далее**.

	![Выберите базу данных и таблицу.][5]

6. В диалоговом окне **Сохранение файла подключения данных и завершение** нажмите кнопку **Завершить**.

7. В диалоговом окне **Импорт данных** выберите **Сводная диаграмма** и нажмите кнопку **ОК**.

	![Импортируйте данные в Excel: в диалоговом окне «Импорт данных» выберите «Сводная диаграмма».][2]

8. В диалоговом окне **Поля сводной диаграммы** выберите следующую конфигурацию, чтобы создать отчет по количеству продуктов в категории.

	![Настройте отчет базы данных.][3]

	У вас должен получиться следующий результат:

	![Программа Excel успешно подключена к базе данных SQL.][4]

## Дальнейшие действия

Если вы разрабатываете программное обеспечение как услугу (SaaS), ознакомьтесь с информацией о [пулах эластичных баз данных](sql-database-elastic-pool.md). Вы можете легко управлять крупными коллекциями баз данных с помощью [заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->