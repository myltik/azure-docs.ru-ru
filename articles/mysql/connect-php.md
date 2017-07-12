---
title: "Подключение к базе данных Azure для MySQL с помощью PHP | Документация Майкрософт"
description: "В этом кратком руководстве представлены примеры кода PHP, которые можно использовать для подключения к базе данных Azure для MySQL и запроса данных из нее."
services: mysql
author: mswutao
ms.author: wuta
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 06/26/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: e3ac0e1813022d1b3544fc2c784719d6c98a0cf3
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017

---

<a id="azure-database-for-mysql-use-php-to-connect-and-query-data" class="xliff"></a>

# База данных Azure для MySQL: подключение и запрос данных с помощью PHP
В этом кратком руководстве объясняется, как подключиться к базе данных Azure для MySQL с помощью приложения [PHP](http://php.net/manual/intro-whatis.php). Здесь также показано, как использовать инструкции SQL для запроса, вставки, обновления и удаления данных в базе данных. В этой статье предполагается, что у вас уже есть опыт разработки на PHP, но вы только начали работу с базой данных Azure для MySQL.

<a id="prerequisites" class="xliff"></a>

## Предварительные требования
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Создание сервера базы данных Azure для MySQL с помощью портала Azure)
- [Create an Azure Database for MySQL server using Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) (Создание сервера базы данных Azure для MySQL с помощью Azure CLI)

<a id="install-php" class="xliff"></a>

## Установка PHP
Установите PHP на своем сервере или создайте [веб-приложение](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) Azure с PHP.

<a id="macos" class="xliff"></a>

### MacOS
- Скачайте [PHP версии 7.1.4](http://php.net/downloads.php).
- Установите PHP и выполните настройку согласно инструкциям в [руководстве по PHP](http://php.net/manual/install.macosx.php).

<a id="linux-ubuntu" class="xliff"></a>

### Linux (Ubuntu)
- Скачайте [PHP 7.1.4 (x64) непотокобезопасной версии](http://php.net/downloads.php).
- Установите PHP и выполните настройку согласно инструкциям в [руководстве по PHP](http://php.net/manual/install.unix.php).

<a id="windows" class="xliff"></a>

### Windows
- Скачайте [PHP 7.1.4 (x64) непотокобезопасной версии](http://windows.php.net/download#php-7.1).
- Установите PHP и выполните настройку согласно инструкциям в [руководстве по PHP](http://php.net/manual/install.windows.php).

<a id="get-connection-information" class="xliff"></a>

## Получение сведений о подключении
Получите сведения о подключении, необходимые для подключения к базе данных Azure.для MySQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск по имени созданного сервера, например **myserver4demo**.
3. Щелкните имя сервера.
4. Откройте страницу **свойств** сервера. Запишите значения **имени сервера** и **имени для входа администратора сервера**.
 ![Имя сервера базы данных Azure для MySQL](./media/connect-php/1_server-properties-name-login.png)
5. Если вы забыли данные для входа на сервер, перейдите на страницу **Обзор**, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.

<a id="connect-and-create-a-table" class="xliff"></a>

## Подключение и создание таблицы
Используйте указанный ниже код с инструкцией SQL **CREATE TABLE** для подключения и создания таблицы. 

В коде используется класс **улучшенного расширения MySQL** (mysqli), включенный в PHP. Код вызывает методы [mysqli_init](http://php.net/manual/mysqli.init.php) и [mysqli_real_connect](http://php.net/manual/mysqli.real-connect.php), чтобы подключиться к MySQL. Затем код вызывает метод [mysqli_query](http://php.net/manual/mysqli.query.php) для выполнения запроса и метод [mysqli_close](http://php.net/manual/mysqli.close.php), чтобы разорвать подключение.

Замените значения параметров host, username, password и db_name своими значениями. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

<a id="insert-data" class="xliff"></a>

## Добавление данных
Используйте указанный ниже код с инструкцией SQL **INSERT** для подключения и вставки данных.

В коде используется класс **улучшенного расширения MySQL** (mysqli), включенный в PHP. Метод [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) используется для создания подготовленной инструкции INSERT, а затем с помощью метода [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php) привязываются параметры для каждого вставленного значения столбца. Код выполняет инструкцию, используя метод [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php), и закрывает ее с помощью метода [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Замените значения параметров host, username, password и db_name своими значениями. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

<a id="read-data" class="xliff"></a>

## Считывание данных
Используйте указанный ниже код с инструкцией SQL **SELECT** для подключения и чтения данных.  В коде используется класс **улучшенного расширения MySQL** (mysqli), включенный в PHP. В коде используется метод [mysqli_query](http://php.net/manual/mysqli.query.php) для выполнения SQL-запроса и метод [mysqli_fetch_assoc](http://php.net/manual/mysqli-result.fetch-assoc.php) для получения результирующих строк.

Замените значения параметров host, username, password и db_name своими значениями. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

<a id="update-data" class="xliff"></a>

## Обновление данных
Используйте указанный ниже код с инструкцией SQL **UPDATE** для подключения и обновления данных.

В коде используется класс **улучшенного расширения MySQL** (mysqli), включенный в PHP. Метод [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) используется для создания подготовленной инструкции UPDATE, а затем с помощью метода [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php) привязываются параметры для каждого обновленного значения столбца. Код выполняет инструкцию, используя метод [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php), и закрывает ее с помощью метода [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Замените значения параметров host, username, password и db_name своими значениями. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


<a id="delete-data" class="xliff"></a>

## Удаление данных
Используйте указанный ниже код с инструкцией SQL **DELETE** для подключения и чтения данных. 

В коде используется класс **улучшенного расширения MySQL** (mysqli), включенный в PHP. Метод [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) используется для создания подготовленной инструкции DELETE, а затем с помощью метода [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php) привязываются параметры для предложения WHERE в инструкции. Код выполняет инструкцию, используя метод [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php), и закрывает ее с помощью метода [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Замените значения параметров host, username, password и db_name своими значениями. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
> [!div class="nextstepaction"]
> [Создание веб-приложения PHP в Azure с подключением к базе данных MySQL](../app-service-web/app-service-web-tutorial-php-mysql.md?toc=%2fazure%2fmysql%2ftoc.json)

