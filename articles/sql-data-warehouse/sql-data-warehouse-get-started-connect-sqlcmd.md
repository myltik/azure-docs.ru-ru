---
title: Подключение к хранилищу данных SQL Azure (sqlcmd) | Документация Майкрософт
description: Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью служебной программы командной строки [sqlcmd][sqlcmd].
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a4399382445cef5732262520986281ea440f7d51
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600708"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Подключение к хранилищу данных SQL с помощью sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью служебной программы командной строки [sqlcmd][sqlcmd].  

## <a name="1-connect"></a>1. Подключение
Чтобы начать использовать [sqlcmd][sqlcmd], откройте командную строку и введите **sqlcmd** и строку подключения к базе данных хранилища данных SQL. В строке подключения обязательно укажите следующие параметры.

* **Server (-S)**  — сервер в формате `<`имя_сервера`>`.database.windows.net
* **Database (-D)**  — имя базы данных.
* **Enable Quoted Identifiers (-I)** — для подключения к экземпляру хранилища данных SQL необходимо включить заключенные в кавычки идентификаторы.

Чтобы использовать проверку подлинности SQL Server, необходимо добавить параметры имени пользователя и пароля.

* **User (-U)**  — пользователь сервера в формате `<`Пользователь`>`.
* **Password (-P)** — пароль, связанный с пользователем.

Например, строка подключения может выглядеть так:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Чтобы использовать встроенную проверку подлинности Azure Active Directory, необходимо добавить параметры Azure Active Directory.

* **Проверки подлинности Azure Active Directory (-G):** — использовать Azure Active Directory для проверки подлинности.

Например, строка подключения может выглядеть так:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Необходимо [включить проверку подлинности Azure Active Directory](sql-data-warehouse-authentication.md) , чтобы выполнять проверку подлинности с помощью Active Directory.
> 
> 

## <a name="2-query"></a>2. Запрос
После подключения можно подавать любые поддерживаемые инструкции Transact-SQL для экземпляра.  В этом примере запросы отправляются в интерактивном режиме.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

В следующих примерах показано, как выполнить запросы в пакетном режиме, используя параметр -Q или передав SQL программе sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о параметрах, доступных в sqlcmd, см. в [документации по sqlcmd][sqlcmd].

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
