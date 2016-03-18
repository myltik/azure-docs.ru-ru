<properties
	pageTitle="Подключение к Базе данных SQL с помощью Ruby с TinyTDS в Mac OS X (Yosemite)"
	description="Пример кода Ruby, который можно выполнять в Mac OS X (Yosemite) для подключения к базе данных SQL Azure."
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
	ms.date="12/17/2015"
	ms.author="andrela"/>


# Подключение к базе данных SQL с помощью Ruby в ОС Mac OS X (Yosemite)


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Этот раздел содержит пример кода Ruby, выполняемого на клиентском компьютере Mac, где выполняется Yosemite, для подключения к базе данных SQL Azure.

## Предварительные требования

### Установка необходимых модулей

Откройте терминал и установите следующее:

**1) Homebrew**: выполните указанную ниже команду из терминала. Она запустит загрузку диспетчера пакетов Homebrew на компьютер.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS:** выполните указанную ниже команду из терминала. На компьютере будет установлен FreeTDS, что необходимо для работы TinyTDS.

    brew install FreeTDS

**3) TinyTDS:** выполните указанную ниже команду из терминала. На компьютере установится TinyTDS.

    gem install tiny_tds

### База данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Шаг 1. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 2. Подключение

Функция [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) используется для подключения к Базе данных SQL.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Шаг 3. Выполнение запроса

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

## Шаг 4. Вставка строки

В приведенном примере показано, как выполнять инструкцию [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), передавать параметры в режиме защиты от [внедрения кода SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) и извлекать автоматически созданные значения [первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx).


Чтобы использовать TinyTDS с Azure, рекомендуем выполнить несколько инструкций `SET`, чтобы изменить способ обработки определенной информации в текущем сеансе. Рекомендуемые инструкции `SET` предоставлены в примере кода. Например, инструкция `SET ANSI_NULL_DFLT_ON` позволяет использовать нулевые значения в новых столбцах, даже если допустимость нулевых значений в столбце не указана явным образом.

Для согласования с форматом [datetime](http://msdn.microsoft.com/library/ms187819.aspx) Microsoft SQL Server используйте функцию [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) для преобразования в соответствующий формат даты и времени.

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

<!---HONumber=AcomDC_0107_2016-->
<!---Line 81, add "[]"-->