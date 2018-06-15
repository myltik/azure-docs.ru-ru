---
title: Подключение к Базе данных Azure для MySQL с помощью Python
description: В этом кратком руководстве представлены примеры кода Python, которые можно использовать для подключения к базе данных Azure для MySQL и запроса данных из нее.
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 7eef3d71a35b5016e48e519b95c2573fbe3390e7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265111"
---
# <a name="azure-database-for-mysql-use-python-to-connect-and-query-data"></a>База данных Azure для MySQL: подключение и запрос данных с помощью Python
Это краткое руководство демонстрирует, как использовать [Python](https://python.org) для подключения к базе данных Azure для MySQL. Здесь используются инструкции SQL для запроса, вставки, обновления и удаления данных в базе данных из платформ Windows, Mac OS и Ubuntu Linux. В этой статье предполагается, что у вас уже есть опыт разработки на языке Python и вы только начали работу с базой данных Azure для MySQL.

## <a name="prerequisites"></a>предварительным требованиям
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Создание базы данных Azure для сервера MySQL с помощью портала Azure)
- [Create an Azure Database for MySQL server using Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) (Создание сервера базы данных Azure для MySQL с помощью Azure CLI)

## <a name="install-python-and-the-mysql-connector"></a>Установка Python и соединителя MySQL
Установите [Python](https://www.python.org/downloads/) и [соединитель MySQL для Python](https://dev.mysql.com/downloads/connector/python/) на компьютере. В зависимости от используемой платформы выполните действия, приведенные в соответствующем разделе.

### <a name="windows"></a>Windows
1. Скачайте и установите Python 2.7 с веб-сайта [python.org](https://www.python.org/downloads/windows/). 
2. Проверьте установку Python, запустив командную строку. Выполните команду `C:\python27\python.exe -V`, используя параметр V в верхнем регистре, чтобы узнать номер версии.
3. Установите соединитель Python для MySQL с веб-сайта [mysql.com](https://dev.mysql.com/downloads/connector/python/) в соответствии с версией Python.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. В Linux (Ubuntu) Python обычно устанавливается как часть установки по умолчанию.
2. Проверьте установку Python, запустив оболочку bash. Выполните команду `python -V`, используя параметр V в верхнем регистре, чтобы узнать номер версии.
3. Проверьте установку PIP, выполнив команду `pip show pip -V`, чтобы получить номер версии. 
4. PIP может быть включен в некоторых версиях Python. Если PIP не установлен, вы можете установить пакет [PIP](https://pip.pypa.io/en/stable/installing/), выполнив команду `sudo apt-get install python-pip`.
5. Обновите PIP до последней версии, выполнив команду `pip install -U pip`.
6. Установите соединитель MySQL для Python и его зависимости с помощью команды PIP:

   ```bash
   sudo pip install mysql-connector-python-rf
   ```
 
### <a name="macos"></a>MacOS
1. В Mac OS Python обычно устанавливается как часть установки операционной системы по умолчанию.
2. Проверьте установку Python, запустив оболочку bash. Выполните команду `python -V`, используя параметр V в верхнем регистре, чтобы узнать номер версии.
3. Проверьте установку PIP, выполнив команду `pip show pip -V`, чтобы получить номер версии.
4. PIP может быть включен в некоторых версиях Python. Если PIP не установлен, вы можете установить пакет [PIP](https://pip.pypa.io/en/stable/installing/).
5. Обновите PIP до последней версии, выполнив команду `pip install -U pip`.
6. Установите соединитель MySQL для Python и его зависимости с помощью команды PIP:

   ```bash
   pip install mysql-connector-python-rf
   ```

## <a name="get-connection-information"></a>Получение сведений о подключении
Получите сведения о подключении, необходимые для подключения к базе данных Azure.для MySQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск по имени созданного сервера (например, **mydemoserver**).
3. Щелкните имя сервера.
4. Запишите **имя сервера** и **имя для входа администратора сервера** с панели сервера **Обзор**. Если вы забыли свой пароль, можно также сбросить пароль с помощью этой панели.
 ![Имя сервера базы данных Azure для MySQL](./media/connect-python/1_server-overview-name-login.png)
   

## <a name="run-python-code"></a>Выполнение кода Python
- Вставьте код в текстовый файл и сохраните файл в папку проекта с расширением файла PY, например C:\pythonmysql\createtable.py или /home/username/pythonmysql/createtable.py.
- Чтобы выполнить код, запустите командную строку или оболочку Bash. Перейдите в папку проекта `cd pythonmysql`. Затем введите команду python, за которой следует имя файла `python createtable.py`, чтобы запустить приложение. В Windows, если файл python.exe не найден, может потребоваться указать полный путь к исполняемому файлу или добавить путь Python в переменную среды path. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Подключение, создание таблицы и вставка данных
Используйте следующий код для подключения к серверу, создания таблицы и загрузки данных с помощью инструкции SQL **INSERT**. 

В коде импортируется библиотека mysql.connector. Функция [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) используется для подключения базы данных Azure для MySQL с помощью [аргументов подключения](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) из коллекции конфигураций. Код использует курсор при подключении, а метод [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) выполняет SQL-запрос к базе данных MySQL. 

Замените параметры `host`, `user`, `password` и `database` значениями, указанными при создании сервера и базы данных.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Считывание данных
Используйте указанный ниже код с инструкцией SQL **SELECT** для подключения и чтения данных. 

В коде импортируется библиотека mysql.connector. Функция [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) используется для подключения базы данных Azure для MySQL с помощью [аргументов подключения](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) из коллекции конфигураций. Код использует курсор при подключении, а метод [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) выполняет инструкцию SQL для базы данных MySQL. Строки данных считываются с помощью метода [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html). Результирующий набор хранится в строке коллекции. Для циклического прохода по строкам используется итератор.

Замените параметры `host`, `user`, `password` и `database` значениями, указанными при создании сервера и базы данных.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Обновление данных
Используйте указанный ниже код с инструкцией SQL **UPDATE** для подключения и обновления данных. 

В коде импортируется библиотека mysql.connector.  Функция [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) используется для подключения базы данных Azure для MySQL с помощью [аргументов подключения](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) из коллекции конфигураций. Код использует курсор при подключении, а метод [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) выполняет инструкцию SQL для базы данных MySQL. 

Замените параметры `host`, `user`, `password` и `database` значениями, указанными при создании сервера и базы данных.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Удаление данных
Используйте указанный ниже код с инструкцией SQL **DELETE** для подключения и удаления данных. 

В коде импортируется библиотека mysql.connector.  Функция [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) используется для подключения базы данных Azure для MySQL с помощью [аргументов подключения](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) из коллекции конфигураций. Код использует курсор при подключении, а метод [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) выполняет SQL-запрос к базе данных MySQL. 

Замените параметры `host`, `user`, `password` и `database` значениями, указанными при создании сервера и базы данных.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./concepts-migrate-import-export.md)
