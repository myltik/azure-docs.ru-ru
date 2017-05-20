---
title: "Как подключить приложения к базе данных Azure для MySQL | Документация Майкрософт"
description: "В этом документе перечислены все строки подключения, которые в настоящее время поддерживаются для подключения приложений к базе данных Azure для MySQL."
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Как подключить приложения к базе данных Azure для MySQL
В этом документе перечислены все типы строк подключения, поддерживаемые базой данных Azure для MySQL, а также шаблоны и примеры. Строка подключения может содержать различные параметры и настройки.

- Получение сертификата описано в документе [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL](./howto-configure-ssl.md).
- {ваш_узел} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

Например, предположим, что имя сервера — **wpdemo**, имя базы данных — **wpdb**, имя пользователя — **WPAdmin**, а пароль — **orcas!2**.

В этом случае строка подключения будет следующей.

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>Получение строки подключения на портале
На [портале Azure](https://portal.azure.com) перейдите к базе данных Azure для MySQL и щелкните **Строки подключения**, чтобы получить список строк для своего экземпляра: ![строки подключения на портале](./media/howto-connection-strings/connection-strings-on-portal.png).

Строка содержит такие сведения, как драйвер, сервер и другие параметры подключения к базе данных. Скопируйте необходимую строку подключения, а затем укажите в ней свои параметры, например имя базы данных, пароль и т. д. Затем эту строку можно будет использовать для подключения к серверу.

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о библиотеке подключений приведены в разделе [Библиотеки подключений для базы данных Azure для MySQL](./concepts-connection-libraries.md)

