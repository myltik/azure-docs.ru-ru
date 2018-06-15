---
title: Подключение к базе данных Azure для PostgreSQL с помощью Python
description: В этом кратком руководстве представлен пример кода Python, который можно использовать для подключения к базе данных Azure для PostgreSQL и запроса данных из нее.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 926b35f49169c5a87e82996ba714aad40ba5244c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687325"
---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>База данных Azure для PostgreSQL: подключение и запрос данных с помощью Python
Это краткое руководство демонстрирует, как использовать [Python](https://python.org) для подключения к базе данных Azure для PostgreSQL. Здесь также используются инструкции по использованию инструкций SQL для вставки, обновления и удаления данных в базе данных с платформ Windows, Mac OS и Ubuntu Linux. В этой статье предполагается, что у вас уже есть опыт разработки на Python и вы только начали работу с базой данных Azure для PostgreSQL.

## <a name="prerequisites"></a>предварительным требованиям
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Создание базы данных с помощью портала](quickstart-create-server-database-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](quickstart-create-server-database-azure-cli.md)

Кроме того, вам понадобится следующее:
- установить [Python](https://www.python.org/downloads/);
- Установить пакет [pip](https://pip.pypa.io/en/stable/installing/) (он уже установлен, если вы используете двоичные файлы Python 2 >=2.7.9 или Python 3 >= 3.4, скачанные с сайта [python.org](https://python.org)).

## <a name="install-the-python-connection-libraries-for-postgresql"></a>Установка библиотек подключений Python для PostgreSQL
Установите пакет [psycopg2](http://initd.org/psycopg/docs/install.html), позволяющий подключаться к базе данных и запрашивать данные из нее. Пакет psycopg2 [доступен на странице PyPI](https://pypi.python.org/pypi/psycopg2/) в виде пакетов [wheel](http://pythonwheels.com/) для наиболее распространенных платформ (Linux, OS X, Windows). Вы можете использовать команду установки pip, чтобы получить двоичную версию модуля, включая все зависимости.

1. На своем компьютере запустите интерфейс командной строки:
    - На платформе Linux запустите оболочку Bash.
    - На платформе macOS запустите терминал.
    - На платформе Windows запустите командную строку из меню "Пуск".
2. Убедитесь, что вы используете последнюю версию pip при выполнении определенной команды. Например:
    ```cmd
    pip install -U pip
    ```

3. Выполните следующую команду, чтобы установить пакет psycopg2:
    ```cmd
    pip install psycopg2
    ```

## <a name="get-connection-information"></a>Получение сведений о подключении
Получите сведения, необходимые для подключения к базе данных Azure.для PostgreSQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск по имени созданного сервера (например, **mydemoserver**).
3. Щелкните имя сервера.
4. Запишите **имя сервера** и **имя для входа администратора сервера** с панели сервера **Обзор**. Если вы забыли свой пароль, можно также сбросить пароль с помощью этой панели.
 ![Имя сервера службы "База данных Azure для PostgreSQL"](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-python-code"></a>Как выполнять код Python
В этой статье содержится всего четыре образца кода, каждый из которых выполняет определенную функцию. В приведенной ниже инструкции объясняется, как создать текстовый файл, вставить блок кода и затем сохранить файл, чтобы запустить его позже. Не забудьте создать четыре отдельных файла, по одному для каждого блока кода.

- С помощью предпочитаемого текстового редактора создайте файл.
- Скопируйте и вставьте пример кода, показанный ниже, в текстовый файл. Замените значения параметров **Host**, **dbname**, **User** и **Password** значениями, указанными при создании сервера и базы данных.
- Сохраните PY-файл (например, postgres.py) в папку проекта. При сохранении файла в Windows убедитесь, что выбрали кодировку UTF-8. 
- Запустите командную строку, терминал или оболочку Bash и затем перейдите в каталог вашей папки проекта, например `cd postgres`.
-  Чтобы запустить код, введите команду Python, за которой следует имя файла, например `Python postgres.py`

> [!NOTE]
> Начиная с Python версии 3, при выполнении приведенных ниже фрагментов кода может появиться ошибка `SyntaxError: Missing parentheses in call to 'print'`. В этом случае замените каждый вызов команды `print "string"` вызовом функции в скобках, например `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Подключение, создание таблицы и вставка данных
Используйте указанный ниже код для подключения и загрузки данных с помощью функции [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) с инструкцией SQL **INSERT**. Функция [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) используется для выполнения SQL-запроса к базе данных PostgreSQL. Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

После успешного выполнения кода выводится следующий результат:

![Вывод командной строки](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Считывание данных
Используйте указанный ниже код для чтения данных, вставленных с помощью функции [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute), используя инструкцию SQL **SELECT**. Эта функция принимает запрос и возвращает результирующий набор, по которому может быть выполнена итерация с помощью метода [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Обновление данных
Используйте указанный ниже код, чтобы обновить строку inventory, вставленную ранее с помощью функции [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute), используя инструкцию SQL **UPDATE**. Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Удаление данных
Используйте указанный ниже код, чтобы удалить элемент inventory, вставленный ранее с помощью функции [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute), используя инструкцию SQL **DELETE**. Замените значения параметров host, dbname, user и password значениями, указанными при создании сервера и базы данных.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)
