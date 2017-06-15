---
title: "Подключение к базе данных SQL Azure с помощью Python | Документация Майкрософт"
description: "В этой статье представлен пример кода Python, который можно использовать для подключения и выполнения запросов к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5fae11119500fd3be3af3e573d45f6cc5880e037
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017

---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью Python

 В этом кратком руководстве показано, как, используя [Python](https://python.org), подключиться к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных на платформах Mac OS, Ubuntu Linux и Windows.

## <a name="prerequisites"></a>Предварительные требования

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
- [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Установка библиотек связи Python и базы данных

В этом разделе предполагается, что у вас уже есть опыт разработки на Python и вы только начали работу с Базой данных SQL Azure. Если вы новичок в разработке на Python, перейдите на страницу [создания приложения с помощью SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/), выберите язык **Python** и операционную систему.

### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды, чтобы установить **brew**, **драйвер Microsoft ODBC для Mac** и **pyodbc**. pyodbc использует драйвер Microsoft ODBC в Linux для подключения к базам данных SQL.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды, чтобы установить **драйвер Microsoft ODBC для Linux** и **pyodbc**. pyodbc использует драйвер Microsoft ODBC в Linux для подключения к базам данных SQL.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Установите [драйвер Microsoft ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (обновите драйвер при появлении соответствующего запроса). Модуль pyodbc использует драйвер Microsoft ODBC в Linux для подключения к базам данных SQL. 

Затем установите **pyodbc** с помощью pip.

```cmd
pip install pyodbc==3.1.1
```

Инструкции по установке pip см. [здесь](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер, перейдите на соответствующую страницу, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.     
   
## <a name="select-data"></a>Выбор данных

Используйте следующий код, чтобы запросить 20 наиболее популярных продуктов по категории, используя функцию [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) в инструкции Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Функция [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) используется для извлечения результирующего набора из запроса к базе данных SQL. Эта функция принимает запрос и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>Добавление данных
Используйте указанный ниже код, чтобы вставить новый продукт в таблицу SalesLT.Product с помощью функции [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) и инструкции Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>Обновление данных
Используйте следующий код, чтобы обновить новый продукт, добавленный ранее, с помощью функции [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) и инструкции Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>Удаление данных
Используйте следующий код, чтобы удалить новый продукт, добавленный ранее, с помощью функции [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) и инструкции Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>Дальнейшие действия

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft Python Drivers for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) (Драйверы Microsoft Python для SQL Server)
- [Центр по разработке для Python](/develop/python/)


