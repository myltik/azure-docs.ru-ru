---
title: Подключение к базе данных SQL с помощью C и C++ | Документация Майкрософт
description: Используйте приведенный в этом кратком руководстве пример кода, чтобы с помощью базы данных SQL Azure разработать современное приложение на C++ на основе мощной облачной реляционной базы данных.
services: sql-database
author: edmacauley
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.devlang: cpp
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: edmacauley
ms.openlocfilehash: c37fdaa9f7aa2a0d243fe6cbc175060156967c61
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644704"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Подключение к базе данных SQL с помощью C и C++
Эта публикация предназначена для разработчиков C и C++, выполняющих подключение приложений к базе данных SQL Azure. Публикация содержит несколько разделов, что дает возможность переходить сразу к интересующей вас теме. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Предварительные требования для выполнения инструкций руководства по C/C++
Убедитесь, что у вас есть указанные ниже компоненты.

* Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Для разработки и запуска этого примера необходимо установить компоненты языка C++.
* [Инструменты разработки Visual Studio для Linux](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Если вы разрабатываете приложение на платформе Linux, необходимо также установить расширение Linux для Visual Studio. 

## <a id="AzureSQL"></a>База данных SQL Azure и SQL Server на виртуальных машинах
Компоненты Azure SQL основаны на Microsoft SQL Server и предназначены для обеспечения высокой доступности, производительности и масштабируемости служб. Использование SQL Azure с собственной локальной базой данных дает множество преимуществ. Благодаря SQL Azure вам не нужно устанавливать, настраивать, обслуживать базу данных или управлять ею. Вы работаете только с содержимым и структурой базы данных. В нее встроены такие возможности, как отказоустойчивость и избыточность, которые так важны при работе с базами данных. 

В данный момент Azure предлагает два варианта для обработки рабочих нагрузок сервера SQL: база данных SQL Azure (база данных как услуга) и сервер SQL на виртуальных машинах. Мы не будем подробно рассматривать различия между этими двумя вариантами, однако отметим, что база данных Azure SQL является лучшим решением для новых облачных приложений, так как она позволяет экономить средства и оптимизировать производительность облачных служб. Если вы рассматриваете возможность переноса или расширения своих локальных приложений в облако, сервер SQL на виртуальной машине Azure может быть хорошим выбором. Чтобы было проще следовать инструкциям в этой статье, создадим базу данных SQL Azure. 

## <a id="ODBC"></a>Технологии доступа к данным: ODBC и OLE DB
Подключение к базе данных SQL Azure ничем не отличается от обычной процедуры. В настоящее время существует два способа подключения к базам данных: ODBC и OLE DB. В последние годы корпорация Майкрософт поддерживает [ODBC для доступа к собственным реляционным данным](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). Технология ODBC относительно проста и работает гораздо быстрее, чем OLE DB. Единственное предостережение — ODBC использует старый API в стиле C. 

## <a id="Create"></a>Шаг 1. Создание базы данных SQL Azure
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started-portal.md) .  Или просмотрите [этот короткий 2 -минутный видеоролик](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/), чтобы создать базу данных SQL Azure с помощью портала Azure.

## <a id="ConnectionString"></a>Шаг 2. Получение строки подключения
После подготовки базы данных SQL Azure необходимо выполнить следующие действия, чтобы найти информацию о подключении и добавить IP-адрес клиента для доступа через брандмауэр. 

На [портале Azure](https://portal.azure.com/) перейдите к строке подключения ODBC базы данных SQL Azure с помощью команды **Показать строки подключения к базам данных** в обзоре базы данных: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Скопируйте содержимое строки **ODBC (включает Node.js) [проверка подлинности SQL]**. Оно будет использоваться позже для подключения из интерпретатора командной строки ODBC C++. Эта строка содержит такие сведения, как драйвер, сервер и другие параметры подключения к базе данных. 

## <a id="Firewall"></a>Шаг 3. Добавление IP-адреса в брандмауэр
Перейдите к разделу брандмауэра, где указан сервер базы данных, и добавьте IP-адрес клиента с помощью [этих действий](sql-database-configure-firewall-settings.md), чтобы установить подключение. 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

На этом этапе база данных SQL Azure настроена и готова к подключению из приложения C++. 

## <a id="Windows"></a>Шаг 4. Подключение из приложения Windows C/C++
К базе данных SQL Azure можно подключиться при помощи ODBC в Windows с использованием [этого примера](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), созданного с использованием Visual Studio. В этом примере используется интерпретатор командной строки ODBC, с помощью которого можно подключиться к базе данных SQL Azure. Данный пример принимает в качестве аргумента командной строки файл с именем базы данных-источника (DSN) или подробную строку подключения, скопированную на портале Azure ранее. Откройте страницу свойств для этого проекта и вставьте строку подключения в качестве аргумента команды, как показано ниже: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Убедитесь, что в строке подключения к базе данных указаны правильные сведения для проверки подлинности. 

Запустите приложение, чтобы создать его. Должно появиться следующее окно, подтверждающее успешность подключения. Чтобы проверить подключение базы данных, можно выполнить базовые команды SQL, например **создать таблицу**:

![Команды SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Кроме того, файл DSN можно создать при помощи мастера, который запускается, если не указаны аргументы командной строки. Рекомендуется попробовать и этот вариант. Этот файл DSN можно использовать для автоматизации и защиты параметров проверки подлинности: 

![Создание файла DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Поздравляем! Вы успешно установили подключение к базе данных SQL Azure при помощи C++ и ODBC в Windows. Чтобы сделать то же самое для платформы Linux, см. сведения дальше в этой статье. 

## <a id="Linux"></a>Шаг 5. Подключение из приложения Linux C/C++
Возможно, вы еще не знаете об этом, но теперь в Visual Studio можно разрабатывать приложения C++ для Linux. Об этой новой возможности можно прочитать в записи блога [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) (Разработка Visual C++ для Linux). Чтобы создавать приложения для Linux, необходим удаленный компьютер, на котором запущен дистрибутив Linux. Если у вас нет такого компьютера, его можно быстро настроить при помощи [виртуальных машин Linux Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Для выполнения инструкций этого руководства предположим, что у вас настроен дистрибутив Linux Ubuntu 16.04. Описанные здесь действия также должны работать для Ubuntu 15.10, Red Hat 6 и Red Hat 7. 

С помощью следующих действий устанавливаются библиотеки, необходимые вашему дистрибутиву для SQL и ODBC:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Запустите Visual Studio. Последовательно выберите "Средства" -> "Параметры" -> "Кроссплатформенный" -> "Диспетчер соединений" и добавьте подключение к компьютеру под управлением Linux: 

!["Средства" -> "Параметры"](./media/sql-database-develop-cplusplus-simple/tools.png)

После того как установлено подключение по протоколу SSH, создайте шаблон пустого проекта (Linux): 

![Шаблон нового проекта](./media/sql-database-develop-cplusplus-simple/template.png)

Затем можно добавить [новый исходный файл C и заменить его этим содержимым](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Используя API ODBC SQLAllocHandle, SQLSetConnectAttr и SQLDriverConnect, можно инициализировать и установить подключение к базе данных. Как и для образца Windows ODBC, необходимо заменить вызов SQLDriverConnect сведениями из параметров строки подключения к базе данных, ранее скопированными на портале Azure. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Последнее что необходимо выполнить перед компиляцией — добавить **odbc** в качестве зависимости библиотеки: 

![Добавление ODBC в качестве входной библиотеки](./media/sql-database-develop-cplusplus-simple/lib.png)

Чтобы запустить приложение, откройте консоль Linux из меню **Отладка**: 

![Консоль Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Если подключение успешно, вы увидите имя текущей базы данных в консоли Linux: 

![Выходные данные в окне консоли Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Поздравляем! Вы успешно выполнили инструкции руководства и теперь можете подключиться к базе данных SQL Azure из C++ на платформах Windows и Linux.

## <a id="GetSolution"></a>Получение полного решения C/C++ для этого руководства
Решение GetStarted, содержащее все примеры из этой статьи, можно найти в таких разделах GitHub:

* [образец ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) — загрузите образец ODBC C++ Windows для подключения к Azure SQL;
* [образец ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29) — загрузите образец ODBC C++ Linux для подключения к Azure SQL.

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* См. дополнительные сведения в [справочнике по API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)

