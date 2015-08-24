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
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Подключение к Базе данных SQL с помощью Ruby в операционной системе Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Этот раздел содержит пример кода Ruby, выполняемого на клиентском компьютере под управлением Windows 8.1, для подключения к Базе данных SQL Azure.

## Установка необходимых модулей

Откройте терминал и установите следующее:

**1) Ruby:** если на вашем компьютере не установлен язык Ruby, установите его. Новым пользователям Ruby рекомендуем использовать установщики Ruby 2.1.X. Они предоставляют стабильный язык, а также обширный список совместимых и обновленных пакетов. [Перейдите на страницу загрузки Ruby]() и скачайте соответствующий установщик 2.1.x. Например, если вы пользуетесь 64-разрядным компьютером, скачайте установщик **Ruby 2.1.6 (x64)**. <br/><br/>Скачав установщик, сделайте следующее:


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

## Создание базы данных и получение строки подключения

Пример Ruby основывается на примере базы данных `AdventureWorks`. Если у вас еще нет базы данных `AdventureWorks`, инструкции по ее созданию вы можете найти в статье [Создание первой Базы данных SQL Azure](sql-database-get-started.md)

В этом разделе также объясняется, как получить строку подключения к базе данных.

## Подключение к базе данных SQL

Функция [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) используется для подключения к Базе данных SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Выполнение инструкции SELECT и получение результирующего набора

Скопируйте следующий код и вставьте его в пустой файл. Назовите его test.rb. Затем выполните его, введя следующую команду в командной строке:

	ruby test.rb

В примере кода функция [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) используется для извлечения результирующего набора из запроса к Базе данных SQL. Эта функция принимает запрос и возвращает результирующий набор. По результирующему набору выполняется итерация с помощью [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

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

Для получения автоматически созданных [значений первичного ключа](http://msdn.microsoft.com/library/ms179610.aspx) в Базе данных SQL можно использовать свойство [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) и объект [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx).

Чтобы использовать TinyTDS с Azure, рекомендуем выполнить несколько инструкций `SET`, чтобы изменить способ обработки определенной информации в текущем сеансе. Рекомендуемые инструкции `SET` представлены в примере кода. Например, инструкция `SET ANSI_NULL_DFLT_ON` позволяет использовать нулевые значения в новых столбцах, даже если допустимость нулевых значений в столбце не указана явным образом.

Для согласования с форматом [datetime](http://msdn.microsoft.com/library/ms187819.aspx) Microsoft SQL Server, используйте функцию [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime), чтобы преобразовать дату и время в соответствующий формат.

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

<!---HONumber=August15_HO7-->