<properties
	pageTitle="Подключение к Базе данных SQL Azure с помощью Excel"
	description="Электронная таблица Excel для Базы данных SQL Azure для создания отчетов и просмотра данных."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Подключение к Базе данных SQL Azure с помощью Excel
Подключение Excel к Базе данных SQL Azure и создание отчета по данным в базе данных.

## Предварительные требования
- Подготовленная и запущенная База данных SQL Azure. Информацию о создании базы данных читайте в статье [Начало работы с Базой данных SQL Microsoft Azure](sql-database-get-started.md).
- [Microsoft Excel 2013](https://products.office.com/en-US/) (или Microsoft Excel 2010)

## Подключение к Базе данных SQL и создание отчета
1.	Откройте Excel.
2.	На панели меню вверху страницы щелкните **Данные**.
3.	Щелкните **Из других источников**, а затем **Из SQL Server**. Откроется **Мастер подключения данных**.

	![Мастер подключения данных][1]
4.	В поле **Имя сервера** введите имя сервера Базы данных SQL Azure. Пример:

	 	adventureserver.database.windows.net
5.	В разделе **Учетные данные для входа** выберите **Использовать следующее имя пользователя и пароль**, а затем введите соответствующие учетные данные для сервера Базы данных SQL. Нажмите кнопку **Далее**.

	Примечание. Надстройки [Сводная диаграмма](https://www.microsoft.com/download/details.aspx?id=102) и [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) для Excel очень похожи в работе.

6. В диалоговом окне **Выбор базы данных и таблицы** выберите базу данных **AdventureWorks** в раскрывающемся меню и **vGetAllCategories** в списке таблиц и представлений, а затем нажмите кнопку **Далее**.

	![Выберите базу данных и таблицу][5]
7. В диалоговом окне **Сохранение файла подключения данных и завершение** нажмите кнопку **Завершить**.
8. В диалоговом окне **Импорт данных** выберите **Сводная диаграмма** и нажмите кнопку **ОК**.

	![Выберите данные для импорта][2]
9. В диалоговом окне **Поля сводной диаграммы** выберите следующую конфигурацию, чтобы создать отчет по числу продуктов на категорию.

	![Конфигурация][3]
10.	У вас должен получиться следующий результат:

	![Успешное завершение][4]

## Дальнейшие действия

Если вы разрабатываете программное обеспечение как услугу (SaaS), узнайте подробнее о [пулах эластичных баз данных](sql-database-elastic-pool.md). Вы легко можете управлять крупными коллекциями баз данных с помощью [заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO3-->