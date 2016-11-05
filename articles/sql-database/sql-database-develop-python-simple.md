---
title: Подключение к базе данных SQL с помощью Python | Microsoft Docs
description: В статье представлен пример кода Python, который можно использовать для подключения к базе данных SQL Azure.
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/05/2016
ms.author: meetb

---
# <a name="connect-to-sql-database-by-using-python"></a>Подключение к базе данных SQL с помощью Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе описывается подключение и запрос к базе данных SQL Azure с использованием Python. Приведенный пример можно выполнить в операционной системе Windows, Ubuntu Linux или Mac.

## <a name="step-1:-create-a-sql-database"></a>Шаг 1. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**. После создания базы данных не забудьте разрешить доступ к вашему IP-адресу, включив правила брандмауэра. Это описано в разделе [Начало работы](sql-database-get-started.md).

## <a name="step-2:-configure-development-environment"></a>Шаг 2. Настройка среды разработки
### <a name="**mac-os**"></a>**Mac OS**
### <a name="install-the-required-modules"></a>Установка необходимых модулей
Откройте терминал и установите:

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="**linux-(ubuntu)**"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды для установки **FreeTDS** и **pymssql**. Драйвер pymssql использует FreeTDS для подключения к базам данных SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1

### <a name="**windows**"></a>**Windows**
Установите pymssql [**отсюда**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Убедитесь, что выбран правильный файл WHL. Например, если вы используете Python 2.7 на 64-разрядном компьютере, выберите файл pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. После загрузки файла WHL разместите его в папке C:/Python27.

Теперь установите драйвер pymssql с помощью pip из командной строки. Перейдите в папку C:/Python27 и выполните следующее.

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Инструкции по установке pip см. [здесь](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3:-run-sample-code"></a>Шаг 3. Выполнение примера кода
Создайте файл с именем **sql_sample.py** и вставьте в него следующий код. Это можно выполнить с помощью командной строки:

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Подключение к базе данных SQL
Функция [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) используется для подключения к базе данных SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Выполнение оператора SQL SELECT
Функция [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. Эта функция фактически принимает любой запрос и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row,-pass-parameters,-and-retrieve-the-generated-primary-key"></a>Вставка строки, передача параметров и получение созданного первичного ключа
Для получения автоматически созданных значений [первичного ключа](https://msdn.microsoft.com/library/ms186775.aspx) в базе данных SQL можно использовать свойство [IDENTITY](https://msdn.microsoft.com/library/ff878058.aspx) и объект [SEQUENCE](https://msdn.microsoft.com/library/ms179610.aspx). 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Транзакции
Этот пример кода демонстрирует использование транзакций, в которых можно:

* начать транзакцию;
* вставить строку данных;
* откатить транзакцию для отмены вставки. 

Вставьте следующий код в файл sql_sample.py.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft Python для SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Посетите [Центр разработчиков для Python](/develop/python/).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)

<!--HONumber=Oct16_HO2-->


