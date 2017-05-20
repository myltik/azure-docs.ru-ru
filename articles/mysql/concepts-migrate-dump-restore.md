---
title: "Перенос базы данных MySQL с помощью дампа и восстановления в базе данных Azure для MySQL | Документация Майкрософт"
description: "Представляет перенос базы данных Azure для MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c09a6fa947d235189ab0137b074b6d7d9c925827
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Перенос базы данных MySQL в базу данных Azure для MySQL с помощью дампа и восстановления
В этой статье описываются два распространенных способа архивации и восстановления баз данных в базе данных Azure для MySQL:
- создание резервной копии и восстановление из командной строки (с помощью mysqldump); 
- создание резервной копии и восстановление с помощью PHPMyAdmin. 

## <a name="before-you-begin"></a>Перед началом работы
Прежде чем приступить к выполнению этого руководства, необходимо выполнить следующее:
- [создать сервер базы данных Azure для MySQL с помощью портала Azure](quickstart-create-mysql-server-database-using-azure-portal.md);
- установить на компьютере служебную программу командной строки [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html);
- установить MySQL Workbench ([ссылка на скачивание MySQL Workbench](https://dev.mysql.com/downloads/workbench/)), Toad, Navicat или любой сторонний инструмент MySQL.

## <a name="use-common-tools"></a>Использование распространенных инструментов
Используйте распространенные инструменты, такие как MySQL Workbench, mysqldump, Toad или Navicat, для удаленного подключения и восстановления данных в базу данных Azure для MySQL. Используйте эти инструменты на своем клиентском компьютере, подключенном к Интернету, чтобы подключиться к базе данных Azure для MySQL. Для обеспечения безопасности используйте подключение с SSL-шифрованием, а также см. статью [SSL-соединения в базе данных Azure для MySQL](concepts-ssl-connection-security.md). При переносе в базу данных Azure для MySQL не нужно перемещать файлы дампа в особое облачное расположение. 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Создание файла резервной копии из командной строки с помощью mysqldump
Чтобы создать резервную копию существующей базы данных MySQL локально или на виртуальной машине, выполните следующую команду: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Необходимо указать следующие параметры:
- [uname] — имя пользователя базы данных; 
- [pass] — пароль для базы данных (обратите внимание, что между "-p" и паролем нет пробела); 
- [dbname] — имя базы данных; 
- [backupfile.sql] — имя файла резервной копии базы данных; 
- [--opt] — параметр mysqldump. 

Например, чтобы создать резервную копию базы данных с именем testdb, с именем пользователя testuser и без пароля и сохранить ее в файл testdb_backup.sql, используйте приведенную ниже команду. Эта команда создаст резервную копию базы данных testdb, сохранив ее в файл testdb_backup.sql, который будет содержать все инструкции SQL, необходимые для повторного создания базы данных. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Чтобы выбрать для создания резервной копии конкретные таблицы в базе данных, укажите имена этих таблиц в виде списка, разделенного пробелами. Например, чтобы создать резервную копию только для таблиц table1 и table2 из базы данных testdb, используйте этот пример: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Чтобы создать резервную копию сразу нескольких баз данных, используйте параметр --databases и укажите имена этих баз данных в виде списка, разделенного пробелами. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Чтобы одновременно создать резервную копию всех баз данных на сервере, используйте параметр --all-databases.
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="upload-files"></a>Передача файлов
С помощью клиента WinSCP можно легко передать файлы и управлять процессами импорта или дампа существующих файлов из среды MySQL (Azure или не Azure) в локальной среде, используя протокол SFTP или FTPS для экспорта.

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>Создание базы данных на целевом сервере Azure MySQL
Создайте пустую базу данных на целевом сервере базы данных Azure для MySQL, на который необходимо перенести данные. Используйте для этого MySQL Workbench, Toad, Navicat или любой сторонний инструмент MySQL. База данных может иметь то же имя, что и база данных, содержащая данные дампа. Вы также можете создать базу данных с другим именем.

![Строка подключения базы данных Azure для MySQL](./media/concepts-migrate-import-export/p5.png)

![Строка подключения MySQL Workbench](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Восстановление базы данных MySQL с помощью командной строки или MySQL Workbench
После создания целевой базы данных можно воспользоваться командой mysql или клиентом MySQL Workbench, чтобы восстановить данные в определенную вновь созданную базу данных из файла дампа.
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
В этом примере мы восстановит данные во вновь созданную базу данных testdb3 на целевом сервере.
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Экспорт с помощью PHPMyAdmin
Для экспорта можно использовать распространенный инструмент phpMyAdmin, который уже может быть установлен в вашей локальной среде. Чтобы экспортировать базу данных MySQL с помощью PHPMyAdmin, выполните следующие действия:
- Откройте phpMyAdmin.
- Выберите базу данных, щелкнув ее имя в списке в левой части экрана. 
- Щелкните ссылку "Export" (Экспорт). При этом откроется новый экран и будет предложено просмотреть дамп базы данных. 
- В области "Export" (Экспорт) щелкните ссылку "Select All" (Выбрать все), чтобы выбрать все таблицы в базе данных. 
- В области параметров SQL выберите необходимые параметры. 
- Щелкните параметр "Save as file" (Сохранить как файл), выберите соответствующий вариант сжатия, а затем нажмите кнопку "Go" (Перейти). Появится диалоговое окно, предлагающее сохранить файл локально.

## <a name="import-using-phpmyadmin"></a>Импорт с помощью PHPMyAdmin
Импорт базы данных выполняется подобно экспорту. Выполните следующие действия:
- Откройте phpMyAdmin. 
- Создайте базу данных, присвоив ей соответствующее имя. Затем выберите эту базу данных, щелкнув ее имя в списке в левой части экрана. Если вы хотите перезаписать импортируемые данные поверх существующей базы данных, то щелкните имя базы данных, установите флажки рядом со всеми именами таблиц, а затем выберите "Drop" (Удалить), чтобы удалить все существующие таблицы в базе данных. 
- Щелкните ссылку "SQL". Откроется новый экран, в котором можно ввести команды SQL или передать файл SQL. 
- Используйте кнопку "Browse" (Обзор) для поиска файла базы данных. 
- Нажмите кнопку "Go" (Перейти). В результате будет экспортирована резервная копия, будут выполнены команды SQL и повторно создана база данных.

## <a name="next-steps"></a>Дальнейшие действия

[Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Создание сервера базы данных Azure для MySQL с помощью портала Azure) 
[Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) (Создание сервера базы данных Azure для MySQL с помощью Azure CLI)

