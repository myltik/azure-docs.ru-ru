<properties
	pageTitle="Подключение к Базе данных SQL с помощью Ruby с TinyTDS в Windows"
	description="Пример кода Ruby, который можно выполнять в Windows для подключения к Базе данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="mebha"/>


# Подключение к Базе данных SQL с помощью Ruby в операционной системе Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Этот раздел содержит пример кода Ruby, выполняемого на клиентском компьютере под управлением Windows 8.1, для подключения к Базе данных SQL Azure.

## Предварительные требования

###Установка необходимых модулей

Откройте терминал и установите следующее:

**1) Ruby:** если на вашем компьютере не установлен язык Ruby, установите его. Новым пользователям Ruby рекомендуем использовать установщики Ruby 2.1.X. Они предоставляют стабильный язык, а также обширный список совместимых и обновленных пакетов. [Перейдите на страницу загрузки Ruby](http://rubyinstaller.org/downloads/) и скачайте соответствующий установщик для версии 2.1.x. Например, если вы пользуетесь 64-разрядным компьютером, скачайте установщик **Ruby 2.1.6 (x64)**. <br/><br/>Скачав установщик, сделайте следующее:


- Дважды щелкните файл, чтобы запустить установщик.

- Выберите язык и примите условия.

- На экране параметров установки установите флажки *Добавить исполняемые файлы Ruby в PATH* и *Связать файлы .rb и .rbw с этой установкой Ruby*.


**2) DevKit:** скачайте DevKit со [страницы RubyInstaller](http://rubyinstaller.org/downloads/)

После завершения загрузки сделайте следующее:


- Дважды щелкните файл. Вам будет предложено указать путь для распаковывания файлов.

- Нажмите кнопку "..." и выберите "C:\\DevKit". Возможно, вам потребуется сначала создать эту папку, нажав кнопку "Создать папку".

- Нажмите кнопку ОК, а затем "Извлечь", чтобы извлечь файлы.


Теперь откройте командную строку и введите следующие команды:

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Теперь у вас есть полнофункциональные Ruby и RubyGems!


**3) TinyTDS:** перейдите в папку C:\\DevKit и запустите в терминале следующую команду. На компьютере установится TinyTDS.

	gem inst tiny_tds --pre

### База данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Шаг 1. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 2. Подключение

Функция [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) используется для подключения к базе данных SQL.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Шаг 3. Выполнение запроса

Скопируйте следующий код и вставьте его в пустой файл. Назовите его test.rb. Затем выполните его, введя следующую команду в командной строке:

	ruby test.rb

В примере кода функция [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) используется для извлечения результирующего набора из запроса к базе данных SQL. Эта функция принимает запрос и возвращает результирующий набор. По результирующему набору выполняется итерация с помощью [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## Шаг 4. Вставка строки

В приведенном примере показано, как выполнять инструкцию [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), передавать параметры в режиме защиты от внедрения кода SQL (https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) и извлекать автоматически созданные значения [первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx).

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

<!---HONumber=AcomDC_1210_2015-->