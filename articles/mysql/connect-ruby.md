---
title: Подключение к Базе данных Azure для MySQL с помощью Ruby
description: В этом кратком руководстве представлены примеры кода Ruby, которые можно использовать для подключения к базе данных Azure для MySQL и запроса данных из нее.
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: cbd60be37dc7021ecbb961027dca40d048ed84cb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265084"
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>База данных Azure для MySQL: подключение и запрос данных с помощью Ruby
В этом кратком руководстве объясняется, как подключиться к базе данных Azure для MySQL с помощью приложения [Ruby](https://www.ruby-lang.org) и пакета [Mysql2](https://rubygems.org/gems/mysql2) на платформе Windows, Ubuntu Linux и Mac. Здесь также показано, как использовать инструкции SQL для запроса, вставки, обновления и удаления данных в базе данных. В этой статье предполагается, что у вас уже есть опыт разработки на Ruby и вы только начали работу с базой данных Azure для MySQL.

## <a name="prerequisites"></a>предварительным требованиям
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Создание базы данных Azure для сервера MySQL с помощью портала Azure)
- [Create an Azure Database for MySQL server using Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) (Создание базы данных Azure для сервера MySQL с помощью Azure CLI)

## <a name="install-ruby"></a>Установка Ruby
Установите Ruby, Gem и библиотеку MySQL2 на своем компьютере. 

### <a name="windows"></a>Windows
1. Скачайте и установите [Ruby](http://rubyinstaller.org/downloads/) версии 2.3.
2. Запустите новую командную строку (cmd) из меню "Пуск".
3. Перейдите в каталог Ruby версии 2.3. `cd c:\Ruby23-x64\bin`
4. Выполните команду `ruby -v`, чтобы узнать установленную версию Ruby.
5. Выполните команду `gem -v`, чтобы узнать установленную версию Gem.
6. Создайте модуль Mysql2 для Ruby с помощью Gem, выполнив команду `gem install mysql2`.

### <a name="macos"></a>MacOS
1. Установите Ruby с помощью Homebrew, выполнив команду `brew install ruby`. Дополнительные параметры установки см. в [документации по установке](https://www.ruby-lang.org/en/documentation/installation/#homebrew) Ruby.
2. Выполните команду `ruby -v`, чтобы узнать установленную версию Ruby.
3. Выполните команду `gem -v`, чтобы узнать установленную версию Gem.
4. Создайте модуль Mysql2 для Ruby с помощью Gem, выполнив команду `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Установите Ruby, выполнив команду `sudo apt-get install ruby-full`. Дополнительные параметры установки см. в [документации по установке](https://www.ruby-lang.org/en/documentation/installation/) Ruby.
2. Выполните команду `ruby -v`, чтобы узнать установленную версию Ruby.
3. Установите последние обновления для Gem, выполнив команду `sudo gem update --system`.
4. Выполните команду `gem -v`, чтобы узнать установленную версию Gem.
5. Установите gcc, make и другие инструменты сборки, выполнив команду `sudo apt-get install build-essential`.
6. Установите библиотеки разработчика клиента MySQL, выполнив команду `sudo apt-get install libmysqlclient-dev`.
7. Создайте модуль Mysql2 для Ruby с помощью Gem, выполнив команду `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Получение сведений о подключении
Получите сведения о подключении, необходимые для подключения к базе данных Azure.для MySQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск по имени созданного сервера (например, **mydemoserver**).
3. Щелкните имя сервера.
4. Запишите **имя сервера** и **имя для входа администратора сервера** с панели сервера **Обзор**. Если вы забыли свой пароль, можно также сбросить пароль с помощью этой панели.
 ![Имя сервера базы данных Azure для MySQL](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>Выполнение кода Ruby 
1. Вставьте код Ruby из раздела ниже в текстовые файлы и сохраните их в папке проекта с расширением файла .rb, например `C:\rubymysql\createtable.rb` или `/home/username/rubymysql/createtable.rb`.
2. Чтобы выполнить код, запустите командную строку или оболочку Bash. Перейдите в папку проекта `cd rubymysql`.
3. Введите команду Ruby, за которой следует имя файла, например `ruby createtable.rb`, чтобы запустить приложение.
4. Если в операционной системе Windows приложение Ruby не указано в переменной среды PATH, может потребоваться указать полный путь, чтобы запустить приложение Node, например `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Подключение и создание таблицы
Используйте приведенный ниже код для подключения и создайте таблицу с помощью инструкции SQL **CREATE TABLE**. Добавьте строки в таблицу, применив инструкцию SQL **INSERT INTO**.

Код использует класс [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) и метод .new(), чтобы подключиться к базе данных Azure для MySQL. Затем он вызывает метод [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) несколько раз для выполнения команд DROP, CREATE TABLE и INSERT INTO. После этого вызывается метод [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `username` и `password` собственными значениями. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Считывание данных
Используйте указанный ниже код с инструкцией SQL **SELECT** для подключения и чтения данных. 

Код использует класс [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) и метод .new(), чтобы подключиться к базе данных Azure для MySQL. Затем он вызывает метод [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) для выполнения команды SELECT. После этого вызывается метод [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `username` и `password` собственными значениями. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Обновление данных
Используйте указанный ниже код с инструкцией SQL **UPDATE** для подключения и обновления данных.

Код использует класс [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) и метод .new(), чтобы подключиться к базе данных Azure для MySQL. Затем он вызывает метод [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) для выполнения команды UPDATE. После этого вызывается метод [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `username` и `password` собственными значениями. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Удаление данных
Используйте следующий код с инструкцией SQL **DELETE** для подключения и чтения данных. 

Код использует класс [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) и метод .new(), чтобы подключиться к базе данных Azure для MySQL. Затем он вызывает метод [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) для выполнения команды DELETE. После этого вызывается метод [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `username` и `password` собственными значениями. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./concepts-migrate-import-export.md)
