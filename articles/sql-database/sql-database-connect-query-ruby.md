---
title: "Подключение к базе данных SQL Azure с помощью Ruby | Документация Майкрософт"
description: "В этой статье представлен пример кода Ruby, который можно использовать для подключения и выполнения запросов к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2d1092af00c1bce3e47d980a512a25eff1f0d9b6
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью Ruby

В этом кратком руководстве показано, как, используя [Ruby](https://Ruby.org), подключиться к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных на платформах Mac OS и Ubuntu Linux.

## <a name="prerequisites"></a>Предварительные требования

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
- [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>Установка библиотек связи Ruby и базы данных

В этом разделе предполагается, что у вас уже есть опыт разработки на Ruby и вы только начали работу с Базой данных SQL Azure. Если вы новичок в разработке на Ruby, перейдите на страницу [создания приложения с помощью SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/), выберите язык **Ruby** и операционную систему.

### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Ruby. Введите следующие команды для установки **brew**, **FreeTDS** и **TinyTDS**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Ruby. Введите следующие команды для установки **FreeTDS** и **TinyTDS**.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер, перейдите на соответствующую страницу, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.
    

## <a name="select-data"></a>Выбор данных
Используйте следующий код, чтобы запросить 20 наиболее популярных продуктов по категории, используя функцию [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) в инструкции Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Эта функция принимает запрос и возвращает результирующий набор. По результирующему набору выполняется итерация с помощью [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
Используйте указанный ниже код, чтобы вставить новый продукт в таблицу SalesLT.Product, используя функцию [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) и инструкцию Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

В приведенном примере показано, как безопасно выполнить инструкцию INSERT, передать параметры для защиты от атак путем [внедрения кода SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) и извлечь автоматически созданные значения [первичного ключа](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints).    
  
Чтобы использовать TinyTDS с Azure, рекомендуем выполнить несколько инструкций `SET` , чтобы изменить способ обработки определенной информации в текущем сеансе. Рекомендуемые инструкции `SET` предоставлены в примере кода. Например, инструкция `SET ANSI_NULL_DFLT_ON` позволяет использовать нулевые значения в новых столбцах, даже если допустимость нулевых значений в столбце не указана явным образом.  
  
Для согласования с форматом [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) Microsoft SQL Server используйте функцию [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime), чтобы привести дату и время к соответствующему формату.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
Используйте следующий код, чтобы обновить новый продукт, добавленный ранее, с помощью функции [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) и инструкции Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
Используйте следующий код, чтобы удалить новый продукт, добавленный ранее, с помощью функции [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) и инструкции Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Просмотрите репозиторий GitHub для TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Сообщите о проблеме или задайте вопросы](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby Drivers for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/) (Драйверы Ruby для SQL Server).


