<properties 
	pageTitle="Подключение к Базе данных SQL с помощью Ruby с TinyTDS в Ubuntu" 
	description="Пример кода Ruby, который можно выполнять как клиент в Ubuntu Linux для подключения к Базе данных SQL Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="ajlam" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="andrela"/>


# Подключение к Базе данных SQL с помощью Ruby в ОС Ubuntu Linux

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Этот раздел содержит пример кода Ruby, выполняемого на клиентском компьютере Ubuntu Linux для подключения к Базе данных SQL Azure.

## Установка необходимых модулей

Откройте терминал и, если вы еще не установили библиотеку FreeTDS, установите ее.
	
    sudo apt-get --assume-yes update 
    sudo apt-get --assume-yes install freetds-dev freetds-bin

После настройки компьютера для работы с FreeTDS, если вы еще не установили Ruby, установите его.
    
    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev 
    curl -L https://get.rvm.io | bash -s stable

Если у вас возникли проблемы с подписями, выполните следующую команду.

    command curl -sSL https://rvm.io/mpapis.asc | gph --import - 

Если проблем с подписями нет, выполните следующие команды.

    source ~/.rvm/scripts/rvm 
    rvm install 2.1.2 
    rvm use 2.1.2 --default 
    ruby -v 

Используйте только версию 2.1.2 или виртуальную машину Ruby.

Затем установите TinyTDS.

    gem install tiny_tds

## Создание базы данных и получение строки подключения

Пример Ruby основывается на примере базы данных AdventureWorks. Если у вас еще нет базы данных AdventureWorks, инструкции по ее созданию вы можете найти в следующем разделе: [Создание первой базы данных SQL Azure](sql-database-get-started.md)

В этом разделе также объясняется, как получить строку подключения к базе данных.

## Подключение к базе данных SQL

Функция [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) используется для подключения к базе данных SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Выполнение инструкции SELECT и получение результирующего набора

Функция [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) используется для извлечения результирующего набора из запроса к Базе данных SQL. Эта функция принимает запрос и возвращает результирующий набор. По результирующему набору выполняется итерация с помощью [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## Вставка строки, передача параметров и получение автоматически созданного значения первичного ключа

Пример кода:

- Передает параметры для значений, которые требуется вставить в строку.
- Вставляет строку.
- Получает значение, созданное для первичного ключа.

Для получения автоматически созданных [значений первичного ключа](http://msdn.microsoft.com/library/ms179610.aspx) в базе данных SQL можно использовать свойство [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) и объект [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx).

Чтобы использовать TinyTDS с Azure, рекомендуем выполнить несколько инструкций `SET`, чтобы изменить способ обработки определенной информации в текущем сеансе. Рекомендуемые инструкции `SET` предоставлены в примере кода. Например, инструкция `SET ANSI_NULL_DFLT_ON` позволяет использовать нулевые значения в новых столбцах, даже если допустимость нулевых значений в столбце не указана явным образом.

Для согласования с форматом [datetime](http://msdn.microsoft.com/library/ms187819.aspx) Microsoft SQL Server, используйте функцию [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) для преобразования в соответствующий формат даты и времени.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L") 
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row| 
    puts row
    end 

<!---HONumber=July15_HO4-->