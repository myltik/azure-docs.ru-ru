---
title: "Чтение с помощью Python из базы данных Azure для PostgreSQL | Документация Майкрософт"
description: "Если у вас отсутствуют предварительные знания, то эта статья объяснит вам, как с помощью примера кода Python записать и прочитать данных из таблицы в базе данных Azure для PostgreSQL."
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Чтение с помощью Python из базы данных Azure для PostgreSQL


В этой статье представлена короткая программа на языке Python, которая использует инструкцию SQL SELECT для чтения строк из таблицы. В ней также объясняется, как найти и установить все компоненты, необходимые для запуска программы Python.

Этот пример программы Python, а также описанные здесь инструменты Python одинаково хорошо применимы к различным платформам, включая Linux, Mac и Windows. 


## <a name="install-the-python-interpreter"></a>Установка интерпретатора Python


Пример кода Python в этой статье написан для интерпретатора Python версии 2.7, поэтому его не удастся выполнить на версии 3.x.

Скачайте и установите интерпретатор Python версии 2.7, используя эту ссылку:

- [скачать интерпретатор Python с сайта python.org](https://www.python.org/downloads/)

После установки убедитесь, что интерпретатор находится и запускается из командной строки. Используйте следующую команду:

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>Установка pip.exe, установщика модулей Python


При установке интерпретатора Python также мог установиться файл pip.exe. Возможное его расположение — подкаталог *Scripts/*. Pip.exe устанавливает специализированные модули Python. Убедитесь, что pip.exe находится и запускается:

`pip.exe`

Если не удается запустить pip.exe, то проверьте наличие файла служебной программы Python с именем **get-pip.py**. Если файл get-pip.py найден, то запустите его, чтобы получить pip.exe:

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>Установка psycopg, модуля подключений


Программе Python требуется модуль, который знает, как подключить эту программу к серверу базы данных Azure для PostgreSQL. Этот модуль подключений называется **psycopg2**. Подробнее см. здесь:

- [веб-сайт psycopg2](http://initd.org/psycopg/)

Установите psycopg2 с помощью следующей команды установки pip.exe:

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера базы данных Azure для PostgreSQL


Если у вас еще нет доступа к серверу базы данных Azure для PostgreSQL, то воспользуйтесь следующими ссылками на статьи, где описывается создание сервера:

- [Create an Azure Database for PostgreSQL by using the Azure portal](quickstart-create-server-database-portal.md) (Создание базы данных Azure для PostgreSQL с помощью портала Azure)
- [Create an Azure Database for PostgreSQL by using the Azure CLI](quickstart-create-server-database-azure-cli.md) (Создание базы данных Azure для PostgreSQL с помощью Azure CLI)


## <a name="obtain-the-connection-string-values"></a>Получение значений строк подключения


На портале Azure можно получить значения строк подключения к серверу базы данных Azure для PostgreSQL. Потребуются значения параметров, описанные в следующей таблице.

- В столбце *Имя* указаны идентификаторы параметров, которые необходимы для psycopg2.
- В столбце *-Символ* указаны идентификаторы параметров, которые необходимы для примера программы Python, *PythonDriver.py*.


| Имя | -Символ | Пример значения |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| user | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />*(Все серверы PostgreSQL имеют базу данных с именем **postgres**)* |
| порт | -p | 5432 *(возможно, конкретное значение — 5432)* |
| пароль | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Пример программы Python


В этом разделе приводится исходный код для нашего примера программы Python. Программа будет запущена далее в этой статье.

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>Командные строки для запуска примера программы


В этом разделе приводятся командные строки, которые используются для тестирования примера программы Python, а также фактические выходные данные подтверждения.

В параметрах, описанных выше, необходимо заменить отображаемые здесь примеры значений фактическими значениями строк подключения.

Точный синтаксис для выполнения предыдущего примера программы Python может немного отличаться. Точный синтаксис зависит от вашей операционной системы и от используемого типа консоли.


#### <a name="windows-cmdexe-console"></a>Консоль cmd.exe Windows


В следующем блоке кода представлен фактический тестовый запуск примера программы Python. Использовалась простая командная строка cmd.exe. После каждого введения знака продолжения строки "^" выполнялись такие действия:

1. Нажималась клавиша ВВОД.
2. Отображалась фраза **More?** (Продолжить?).
3. Вводилась следующая часть полной строки, и т. д.

Этот способ продолжения строки использовался, чтобы пример не становился слишком длинным и его было удобнее просмотреть или распечатать.

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

Строка **SUCCESS** (Успешно) является подтверждением того, что программа была выполнена.

В качестве альтернативного способа можно поместить полную командную строку в BAT-файл. Затем запустить BAT-файл из командной строки cmd.exe.


#### <a name="powershell-file"></a>Файл PowerShell


В консоли PowerShell командная строка не поддерживает символ продолжения строки. Поэтому при использовании PowerShell мы поместим соответствующие команды в файл PowerShell. Затем мы запустим этот файл из командной строки PowerShell.

Скопируйте следующий код в файл с именем *PythonDriverRun.ps1*.

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

Запустите файл PythonDriverRun.ps1, как показано ниже. Отображаемая строка **SUCCESS** (Успешно) является подтверждением выполнения программы.

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

Если вы предпочитаете запустить файл PythonDriverRun.ps1 непосредственно из командной строки PowerShell, то необходимо добавить в начало знак "&" и пробел. Без начального знака "&" сообщение подтверждения исчезнет слишком быстро после появления.

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>Установка pgAdmin для проверки сервера


По завершении программы PythonDriverRun.ps1 она выполняет необходимую очистку, удаляя таблицу testpy1, созданную программой. С помощью знака "#" можно закомментировать строку исходного кода, которая выдает инструкцию **DROP TABLE**. Это действие позволит сохранить таблицу, чтобы была возможность просмотреть ее позже.

С помощью инструмента pgAdmin можно проверить любой сервер PostgreSQL и объекты на этом сервере. Пользователи Microsoft SQL Server или Базы данных SQL Azure могут заметить сходство между SQL Server Management Studio (SSMS) и pgAdmin.

При желании вы можете установить **pgAdmin**, чтобы проверить сервер и таблицу **testpy1**.


#### <a name="1-install-pgadmin"></a>1. Установка pgAdmin


Инструкции по установке pgAdmin доступны по этому адресу:

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

Исполняемый файл может иметь имя **pgAdmin4.exe**, а не просто pgAdmin.exe.

Для запуска pgAdmin4.exe на компьютере под управлением Windows введите в командной строке команду, аналогичную следующей:

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2) Подключение pgAdmin к серверу

 
Когда отобразится пользовательский интерфейс pgAdmin, найдите область **Browser** (Браузер). Затем щелкните правой кнопкой мыши **Servers** (Серверы)  > **Create** (Создать)  > **Servers** (Серверы). В данном случае термин *Создать* означает создание *подключения* к любому существующему серверу PostgreSQL, включая серверы базы данных Azure для PostgreSQL.

После создания подключения в области **Browser** (Браузер) отобразится дерево объектов.


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3. Переход к таблице в дереве pgAdmin


Чтобы увидеть таблицу, разверните элементы дерева, как показано на следующем изображении:

- **Servers** (Серверы) &gt; *[Имя_вашего_сервера]* &gt; **Databases** (Базы данных) &gt; postgres &gt; **Schemas** (Схемы) &gt; public (общедоступные) &gt; **tables** (таблицы) &gt; testpy1

![Интерфейс pgAdmin, отображающий таблицу testpy1 в дереве](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4. Удаление таблицы testpy1


Чтобы выполнить окончательную очистку, щелкните узел testpy1 правой кнопкой мыши и выберите **Delete/Drop** (Удалить).


## <a name="next-steps"></a>Дальнейшие действия

- [База данных SQL Azure: подключение и запрос данных с помощью Python](../sql-database/sql-database-connect-query-python.md)
- [Библиотеки подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md)

