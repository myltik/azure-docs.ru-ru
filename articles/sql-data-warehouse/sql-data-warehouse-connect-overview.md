.<properties
   pageTitle="Подключение к хранилищу данных SQL Azure | Microsoft Azure"
   description="Общие сведения о подключении к хранилищу данных SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Подключение к хранилищу данных SQL Azure

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-connect-overview.md)
- [Аутентификация](sql-data-warehouse-authentication.md)
- [Драйверы](sql-data-warehouse-connection-strings.md)

Общие сведения о подключении к хранилищу данных SQL Azure.

## Строка подключения на портале

Ваше хранилище данных SQL связано с сервером SQL Server в Azure. Для подключения вам потребуется полное доменное имя сервера. Например, **имя\_сервера**.database.windows.net.

Чтобы найти полное имя сервера, сделайте следующее.

1. Перейдите на [портал Azure][].
2. Щелкните **Базы данных SQL** и выберите базу данных, к которой необходимо подключиться. В этом примере используется образец базы данных AdventureWorksDW.
3. Найдите полное имя сервера.

    ![Полное имя сервера][1]

## Параметры подключения

Хранилище данных SQL стандартизирует некоторые параметры при установке соединения и создании объектов. Переопределить эти параметры нельзя.

| Параметр базы данных | Значение |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ВКЛ |
| [QUOTED\_IDENTIFIERS][] | ВКЛ |
| [DATEFORMAT][] | мдг |
| [DATEFIRST][] | 7 |

## Мониторинг подключений и запросов

Как только соединение будет установлено, а сеанс запущен, можно приступать к записи и отправке запросов в хранилище данных SQL. Чтобы узнать, как отслеживать сеансы и запросы, см. статью [Мониторинг рабочей нагрузки с помощью динамических административных представлений][].

## Дальнейшие действия

Чтобы приступить к отправке запросов к хранилищу данных с помощью Visual Studio и других приложений, ознакомьтесь с разделом [Запросы к хранилищу данных SQL Azure (Visual Studio)][].

<!--Articles-->
[Запросы к хранилищу данных SQL Azure (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Мониторинг рабочей нагрузки с помощью динамических административных представлений]: ./sql-data-warehouse-manage-monitor.md

.<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

.<!--Other-->
[портал Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->