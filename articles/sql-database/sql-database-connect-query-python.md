---
title: Использование Python для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Python для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: python
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: carlrab
ms.openlocfilehash: e8928a69436cfa0fc601c4a0c544433f3b87a90f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790820"
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Использование Python для создания запросов к базе данных SQL Azure

 В этом кратком руководстве показано, как использовать [Python](https://python.org) для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных. Дополнительные сведения о пакете SDK см. в [справочной документации](https://docs.microsoft.com/python/api/overview/azure/sql). Также см. [пример ](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) pyodbc и репозиторий [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) на GitHub.

## <a name="prerequisites"></a>предварительным требованиям

Ниже указаны требования для работы с этим кратким руководством.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра уровня сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.

- Убедитесь, что установлен компонент Python и связанное программное обеспечение для вашей операционной системы.

    - **Mac OS.** Установите Homebrew, Python, драйвер ODBC и SQLCMD, а затем драйвер Python для SQL Server. См. [шаги 1.2, 1.3 и 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu.** Установите Python и другие необходимые пакеты, а затем установите драйвер Python для SQL Server. См. [шаги 1.2, 1.3 и 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows.** Установите последнюю версию Python (переменная среды теперь настраивается автоматически), драйвер ODBC и SQLCMD, а затем установите драйвер Python для SQL Server. Ознакомьтесь с шагами 1.2, 1.3 и 2.1 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Вставка кода для отправки запроса к базе данных SQL 

1. Создайте файл **sqltest.py** в предпочитаемом текстовом редакторе.  

2. Замените содержимое следующим кодом и добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующие команды:

   ```Python
   python sqltest.py
   ```

2. Убедитесь, что возвращены первые 20 строк, а затем закройте окно приложения.

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft Python Drivers for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) (Драйверы Microsoft Python для SQL Server)
- [Центр по разработке для Python](https://azure.microsoft.com/develop/python/?v=17.23h)

