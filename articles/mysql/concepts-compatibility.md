---
title: "Совместимость драйверов и инструментов управления MySQL | Документация Майкрософт"
description: "Совместимость драйверов и инструментов управления MySQL с базой данных Azure для MySQL."
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 1042f7919b8761bdbc23ae19871703c53dff28f0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2017
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Совместимость драйверов и инструментов управления MySQL с базой данных Azure для MySQL.
В этой статье описываются драйверы и инструменты управления, совместимые с Базой данных Azure для MySQL.

## <a name="mysql-drivers"></a>Драйверы MySQL
База данных Azure для MySQL использует самый популярный выпуск Community Edition базы данных MySQL. Таким образом, она совместима с самыми разнообразными языками программирования и драйверами. Цель этого руководства — обеспечить поддержку трех последних версий драйверов MySQL и взаимодействие с сообществом разработчиков открытого кода, чтобы постоянно улучшать функциональность и удобство использования драйверов MySQL. Следующая таблица содержит список драйверов, которые были протестированы и которые совместимы с базой данных Azure для MySQL 5.6 и 5.7.

| **Драйвер** | **Ссылки** | **Совместимые версии** | **Несовместимые версии** | **Примечания** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5,3 | Для подключения PHP 7.0 с SSL MySQLi добавьте MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT в строке подключения. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Набор PDO: параметр ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` имеет значение False.|
| .Net | [MySqlConnector на Github]: https://github.com/mysql-net/MySqlConnector/releases <br> [Пакет установки из Nuget]:<br> https://www.nuget.org/packages/MySqlConnector/ | Версия 0.27 и более поздние версии | Версия 0.26.5 и предыдущие версии | |
| NodeJS |  [MySQLjs на GitHub]:<br> https://github.com/mysqljs/mysql/releases <br> [Пакет установки из NPM]:<br> Выполните команду npm install mysql в NPM | 2.15 | Версия 2.14.1 и предыдущие версии | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1,3 | Версия 1.2 и предыдущие версии | Введите allowNativePasswords=true в строке подключения |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | Версия 1.2.2 и предыдущие версии | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | Версия 1.5.5 и предыдущие версии | |

## <a name="management-tools"></a>Инструменты управления
Преимущества совместимости также распространяются на инструменты управления базой данных. Имеющиеся инструменты должны продолжать работать с базой данных Azure для MySQL, пока обработка базы данных выполняется в пределах разрешений пользователя. Следующая таблица содержит три распространенных инструмента управления базой данных, которые были протестированы и которые совместимы с базой данных Azure для MySQL 5.6 и 5.7.

|                                     | **MySQL Workbench 6.x и более поздней версии** | **Navicat 12** | **PHPMyAdmin 4.x и более поздней версии** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Создание, обновление, чтение, запись и удаление | X | X | X |
| SSL-соединение | X | X | X |
| Автозавершение запроса SQL | X | X |  |
| Импорт и экспорт данных | X | X | X |
| Экспорт в несколько форматов | X | X | X |
| Архивация и восстановление |  | X |  |
| Отображение параметров сервера | X | X | X |
| Отображение клиентских подключений | X | X | X |
