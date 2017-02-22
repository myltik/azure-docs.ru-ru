---
title: "Подключение к базе данных SQL с помощью Python | Документация Майкрософт"
description: "В статье представлен пример кода Python, который можно использовать для подключения к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2793ddb1c903f6732a193276a2d804192b7ab53b
ms.openlocfilehash: 86524dd1a73df3b60245cb664c0e17a63df00763


---
# <a name="connect-to-sql-database-by-using-python"></a>Подключение к базе данных SQL с помощью Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе описывается подключение и запрос к базе данных SQL Azure с использованием Python. Приведенный пример можно выполнить в операционной системе Windows, Ubuntu Linux или Mac.

## <a name="step-1-create-a-sql-database"></a>Шаг 1. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**. После создания базы данных не забудьте разрешить доступ к вашему IP-адресу, включив правила брандмауэра. Это описано в разделе [Начало работы](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Шаг 2. Настройка среды разработки
### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды, чтобы установить **brew**, **драйвер Microsoft ODBC для Mac** и **pyodbc**. pyodbc использует драйвер Microsoft ODBC в Linux для подключения к базам данных SQL.

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды, чтобы установить **драйвер Microsoft ODBC для Linux** и **pyodbc**. pyodbc использует драйвер Microsoft ODBC в Linux для подключения к базам данных SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Установите [драйвер Microsoft ODBC 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339). pyodbc использует драйвер Microsoft ODBC в Linux для подключения к базам данных SQL. 

Затем установите pyodbc с помощью pip

```
pip install pyodbc==3.1.1
```

Инструкции по установке pip см. [здесь](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3-run-sample-code"></a>Шаг 3. Выполнение примера кода
Создайте файл с именем **sql_sample.py** и вставьте в него следующий код. Это можно выполнить с помощью командной строки:

```
python sql_sample.py
```

### <a name="connect-to-your-sql-database"></a>Подключение к базе данных SQL
Функция [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) используется для подключения к Базе данных SQL.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
if row:
    print row
```

### <a name="execute-an-sql-select-statement"></a>Выполнение оператора SQL SELECT
Функция [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. Эта функция фактически принимает любой запрос и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html).

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1]) + " " + str(row[2])
    row = cursor.fetchone()
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Вставка строки, передача параметров и получение созданного первичного ключа
Для получения автоматически созданных значений [первичного ключа](https://msdn.microsoft.com/library/ms186775.aspx) в базе данных SQL можно использовать свойство [IDENTITY](https://msdn.microsoft.com/library/ff878058.aspx) и объект [SEQUENCE](https://msdn.microsoft.com/library/ms179610.aspx). 

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print "Inserted Product ID : " +str(row[0])
    row = cursor.fetchone()
```

### <a name="transactions"></a>Транзакции
Этот пример кода демонстрирует использование транзакций, в которых можно:

* начать транзакцию;
* вставить строку данных;
* откатить транзакцию для отмены вставки. 

Вставьте следующий код в файл sql_sample.py.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("BEGIN TRANSACTION")
cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
cnxn.rollback()
```

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft Python для SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
* Посетите [Центр разработчиков для Python](/develop/python/).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Feb17_HO2-->


