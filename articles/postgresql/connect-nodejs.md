---
title: "Подключение к базе данных Azure для PostgreSQL с помощью Node.js | Документация Майкрософт"
description: "В этом кратком руководстве представлен пример кода Node.js, который можно использовать для подключения к базе данных Azure для PostgreSQL и запроса данных из нее."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: ru-ru
ms.lasthandoff: 06/26/2017

---

<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# База данных Azure для PostgreSQL: подключение и запрос данных с помощью Node.js
В этом кратком руководстве описывается, как подключиться к базе данных Azure для PostgreSQL при помощи [Node.js](https://nodejs.org/) на платформе Windows, Ubuntu Linux или Mac. Здесь также показано, как использовать инструкции SQL для запроса, вставки, обновления и удаления данных в базе данных. В этой статье предполагается, что у вас уже есть опыт разработки на Node.js и вы только начали работу с базой данных Azure для PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Предварительные требования
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Создание базы данных с помощью портала](quickstart-create-server-database-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](quickstart-create-server-database-azure-cli.md)

Также вам потребуется:
- Установите [Node.js](https://nodejs.org)
- Установить пакет [pg](https://www.npmjs.com/package/pg). 

<a id="install-nodejs" class="xliff"></a>

## Установка Node.js 
В зависимости от используемой платформы выполните приведенные ниже инструкции, чтобы установить Node.js.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Чтобы установить **brew** (простой в использовании диспетчер пакетов для Mac OS X) и **Node.js**, выполните следующую команду:

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Чтобы установить **Node.js** и **npm** (диспетчер пакетов для Node.js), выполните следующую команду:

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
Войдите на [страницу скачиваемых файлов Node.js](https://nodejs.org/en/download/) и выберите нужный установщик Windows.

<a id="install-pg-client" class="xliff"></a>

## Установка клиента pg
Установка [pg](https://www.npmjs.com/package/pg) — клиента на основе чистого JavaScript без блокировки для Node.js — поможет при подключении и выполнении запросов к PostgreSQL.

Для этого запустите диспетчер пакетов узла (npm) для JavaScript из командной строки.
```bash
npm install pg
```

Проверьте установку, получив список установленных пакетов.
```bash
npm list
```
В полученном списке указывается версия каждого компонента. 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

<a id="get-connection-information" class="xliff"></a>

## Получение сведений о подключении
Получите сведения, необходимые для подключения к базе данных Azure.для PostgreSQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск созданного сервера **mypgserver-20170401**.
3. Щелкните имя сервера **mypgserver-20170401**.
4. Выберите страницу **обзора** сервера. Запишите значения **имени сервера** и **имени для входа администратора сервера**.
 ![База данных Azure для PostgreSQL. Учетные данные администратора сервера для входа](./media/connect-nodejs/1-connection-string.png)
5. Если вы забыли данные для входа на сервер, перейдите на страницу **Обзор**, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.

<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>

## Выполнение кода JavaScript в Node.js
Node.js можно запустить из оболочки Bash или командной строки Windows при помощи команды `node`. Затем интерактивно запустите пример кода JavaScript, скопировав и вставив его в командную строку. Либо же можно сохранить код JavaScript в текстовый файл и запустить `node filename.js` с именем файла в качестве параметра для его выполнения.

<a id="connect-create-table-and-insert-data" class="xliff"></a>

## Подключение, создание таблицы и вставка данных
Используйте приведенный ниже код для подключения и загрузки данных с помощью инструкций SQL **CREATE TABLE** и **INSERT INTO**.
Объект [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) используется для обмена данными с сервером PostgreSQL. Функция [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) используется для подключения к серверу. Функция [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) используется для выполнения SQL-запроса к базе данных PostgreSQL. 

Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

<a id="read-data" class="xliff"></a>

## Считывание данных
Используйте указанный ниже код с инструкцией SQL **SELECT** для подключения и чтения данных. Объект [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) используется для обмена данными с сервером PostgreSQL. Функция [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) используется для подключения к серверу. Функция [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) используется для выполнения SQL-запроса к базе данных PostgreSQL. 

Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="update-data" class="xliff"></a>

## Обновление данных
Используйте указанный ниже код с инструкцией SQL **UPDATE** для подключения и чтения данных. Объект [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) используется для обмена данными с сервером PostgreSQL. Функция [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) используется для подключения к серверу. Функция [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) используется для выполнения SQL-запроса к базе данных PostgreSQL. 

Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="delete-data" class="xliff"></a>

## Удаление данных
Используйте указанный ниже код с инструкцией SQL **DELETE** для подключения и чтения данных. Объект [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) используется для обмена данными с сервером PostgreSQL. Функция [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) используется для подключения к серверу. Функция [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) используется для выполнения SQL-запроса к базе данных PostgreSQL. 

Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)

