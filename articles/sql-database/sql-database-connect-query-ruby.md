---
title: "Подключение к базе данных SQL Azure с помощью Ruby | Документация Майкрософт"
description: "Используйте приведенный в этом кратком руководстве пример кода, чтобы с помощью Базы данных SQL Azure разработать современное приложение на Ruby на основе мощной облачной реляционной базы данных."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 03/28/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8e01a717cbef6b5f6d26a8191c44e249bf7a9567
ms.lasthandoff: 03/30/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью Ruby

С помощью [Ruby](https://Ruby.org) вы можете подключаться и отправлять запросы к базе данных SQL Azure. В этом кратком руководстве объясняется, как с помощью Ruby подключаться к базе данных Azure SQL, а также выполнять запросы и использовать инструкции вставки, обновления и удаления.

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>Настройка среды разработки

В следующих разделах подробно описывается настройка существующих сред разработки Mac OS и Linux (Ubuntu) для работы с базой данных SQL Azure.

### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Ruby. Введите следующие команды для установки **brew**, **FreeTDS** и **TinyTDS**.

```ruby
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Ruby. Введите следующие команды для установки **FreeTDS** и **TinyTDS**.

```ruby
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите строку подключения на портале Azure. Используйте строку подключения, чтобы подключиться к базе данных SQL Azure.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На панели **Основные компоненты** базы данных просмотрите полное имя сервера.

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
    

## <a name="select-data"></a>Выбор данных
Используйте функцию [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) с инструкцией Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx), чтобы запросить данные из базы данных SQL Azure. Эта функция принимает запрос и возвращает результирующий набор. По результирующему набору выполняется итерация с помощью [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>Добавление данных
Используйте функцию [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) с инструкцией Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), чтобы вставить данные в базу данных SQL Azure.

В приведенном примере показано, как безопасно выполнить инструкцию [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), передать параметры для защиты от [внедрения кода SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) и извлечь автоматически созданные значения [первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx).    
  
Чтобы использовать TinyTDS с Azure, рекомендуем выполнить несколько инструкций `SET` , чтобы изменить способ обработки определенной информации в текущем сеансе. Рекомендуемые инструкции `SET` предоставлены в примере кода. Например, инструкция `SET ANSI_NULL_DFLT_ON` позволяет использовать нулевые значения в новых столбцах, даже если допустимость нулевых значений в столбце не указана явным образом.  
  
Для согласования с форматом [datetime](http://msdn.microsoft.com/library/ms187819.aspx) Microsoft SQL Server используйте функцию [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime), чтобы привести дату и время к соответствующему формату.

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON"")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>Обновление данных
Используйте функцию [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) с инструкцией Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms189835.aspx), чтобы обновить данные в базе данных SQL Azure.

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>Удаление данных
Используйте функцию [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) с инструкцией Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx), чтобы удалить данные из базы данных SQL Azure.

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md).
* Получите дополнительные сведения о [драйвере Ruby для SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/).

