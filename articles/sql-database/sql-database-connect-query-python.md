---
title: "Использование Python для создания запросов к базе данных SQL Azure | Документация Майкрософт"
description: "В этой статье показано, как использовать Python для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 08/08/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: afffcb9a4938bf97626f182bb4f4d099d807d411
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---
# <a name="use-python-to-query-an-azure-sql-database"></a>Использование Python для создания запросов к базе данных SQL Azure

 В этом кратком руководстве показано, как использовать [Python](https://python.org) для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос к данным.

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

- База данных SQL Azure. В этом кратком руководстве используются ресурсы, созданные в одном из этих кратких руководств: 

   - [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
   - [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
   - [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

- [Правило брандмауэра на уровне сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.

- Убедитесь, что установлен Python и связанное программное обеспечение для вашей операционной системы.

    - **Mac OS.** Установите Homebrew, Python, драйвер ODBC и SQLCMD, а затем драйвер Python для SQL Server. См. [шаги 1.2, 1.3 и 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu.** Установите Python и другие необходимые пакеты, а затем установите драйвер Python для SQL Server. См. [шаги 1.2, 1.3 и 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows.** Установите последнюю версию Python (переменная среды теперь настраивается автоматически), драйвер ODBC и SQLCMD, а затем установите драйвер Python для SQL Server. Ознакомьтесь с шагами 1.2, 1.3 и 2.1 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на следующем рисунке. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер, перейдите на соответствующую страницу, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.     
    
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
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
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

## <a name="next-steps"></a>Дальнейшие действия

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft Python Drivers for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) (Драйверы Microsoft Python для SQL Server)
- [Центр по разработке для Python](https://azure.microsoft.com/develop/python/?v=17.23h)


