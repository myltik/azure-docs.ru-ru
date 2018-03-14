---
title: "Подключение к Azure Databricks из Excel, Python или R | Документация Майкрософт"
description: "Сведения об использовании драйвера Simba для подключения Azure Databricks к Excel, Python или R."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 9daa7d30036d0a0f98d079e03a69c29d11e49664
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Подключение к Azure Databricks из Excel, Python или R

В этой статье представлены сведения об использовании драйвера ODBC Databricks для подключения Azure Databricks к Microsoft Excel, средам разработки для языков R и Python. После установки подключения вы можете получить доступ к данным в Azure Databricks из клиентов Excel, Python или R, которые также можно использовать для дальнейшего анализа данных. 

## <a name="prerequisites"></a>предварительным требованиям

* Необходима рабочая область Azure Databricks, кластер Spark и образцы данных, связанные с кластером. Если у вас еще нет этих компонентов, выполните инструкции, приведенные в кратком руководстве [Запуск задания Spark в Azure Databricks с помощью портала Azure](quickstart-create-databricks-workspace-portal.md).

* Скачайте драйвер ODBC Databricks со [страницы загрузки](https://databricks.com/spark/odbc-driver-download). Установите 32-разрядную или 64-разрядную версию в зависимости от приложения, из которого вы хотите подключиться к Azure Databricks. Например, для подключения из Excel установите 32-разрядную версию драйвера. Чтобы подключиться из R и Python, установите 64-разрядную версию драйвера.

* Настройте личный маркер доступа в Databricks. Инструкции см. в разделе об [управлении маркерами](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Настройка имени DSN

Имя источника данных (DSN) содержит сведения о конкретном источнике данных. Оно необходимо драйверу ODBC для подключения к источнику данных. В этом разделе вы настраиваете имя DSN, которое можно использовать с драйвером ODBC Databricks для подключения к Azure Databricks из таких клиентов, как Microsoft Excel, Python и R.

1. В рабочей области Azure Databricks перейдите к кластеру Databricks.

    ![Открытие кластера Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Открытие кластера Databricks")

2. На вкладке **конфигурации** щелкните вкладку **JDBC/ODBC** и скопируйте значения параметров **Server Hostname** (Имя узла сервера) и **HTTP Path** (Путь HTTP). Эти значения необходимы для выполнения шагов в данной статье.

    ![Получение конфигурации Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Получение конфигурации Databricks")

3. На компьютере запустите приложение **Источники данных ODBC** (32-разрядную или 64-разрядную версию). Для подключения из Excel используйте 32-разрядную версию. Чтобы подключиться из R и Python, используйте 64-разрядную версию.

    ![Запуск ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Запуск приложения ODBC")

4. На вкладке **DSN пользователя** щелкните **Добавить**. В диалоговом окне **Создание нового источника данных** выберите **Simba Spark ODBC Driver** (Драйвер ODBC для Simba Spark), а затем нажмите кнопку **Готово**.

    ![Запуск ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Запуск приложения ODBC")

5. В диалоговом окне **Simba Spark ODBC Driver** (Драйвер ODBC для Simba Spark) введите следующие значения.

    ![Настройка DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Настройка DSN")

    В следующей таблице содержатся сведения о значениях, которые необходимо указать в диалоговом окне.
    
    |Поле  | Значение  |
    |---------|---------|
    |**Имя источника данных**     | Укажите имя источника данных.        |
    |**Узлы**     | Укажите скопированное из рабочей области Databricks значение *имени узла сервера*.        |
    |**Порт**     | Введите *443*.        |
    |**Проверка подлинности** > **Механизм**     | Выберите *Имя пользователя и пароль*.        |
    |**Имя пользователя**     | Введите *маркер*.        |
    |**Пароль**     | Введите скопированное из рабочей области Databricks значение маркера. |
    
    Выполните следующие дополнительные действия в диалоговом окне настройки имени DSN.
    
    * Щелкните **HTTP Options** (Параметры HTTP). В открывшемся диалоговом окне вставьте значение *пути HTTP*, скопированное из рабочей области Databricks. Последовательно выберите **ОК**.
    * Щелкните **SSL Options** (Параметры SSL). В открывшемся диалоговом окне установите флажок **Включить SSL**. Последовательно выберите **ОК**.
    * Нажмите кнопку **Проверка**, чтобы проверить подключение к Azure Databricks. Нажмите кнопку **ОК**, чтобы сохранить конфигурацию.
    * В диалоговом окне **Администратор источников данных ODBC** нажмите кнопку **ОК**.

Теперь имя DSN настроено. В следующих разделах вы используете его для подключения к Azure Databricks из Excel, Python или R.

## <a name="connect-from-microsoft-excel"></a>Подключение из Microsoft Excel

В этом разделе вы извлечете данные из Azure Databricks в Microsoft Excel с помощью созданного ранее имени DSN. Перед началом работы убедитесь, что на вашем компьютере установлена программа Microsoft Excel. Вы можете использовать пробную версию Excel. Для этого перейдите по [этой ссылке](https://products.office.com/excel).

1. Откройте пустую книгу в Microsoft Excel. На ленте **Данные** щелкните **Get Data** (Получить данные). Щелкните **From Other Sources** (Из других источников), а затем — **From ODBC** (Из ODBC).

    ![Запуск ODBC из Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Запуск ODBC из Excel")

2. В диалоговом окне **From ODBC** (Из ODBC) выберите созданное ранее имя DSN и нажмите кнопку **ОК**.

    ![Выбор имени DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Выбор имени DSN")

3. Если будет предложено ввести учетные данные, для имени пользователя введите **token**. В поле пароля укажите значение маркера, полученное из рабочей области Databricks.

    ![Предоставление учетных данных для Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Выбор имени DSN")

4. В окне навигатора выберите таблицу в Databricks, которую необходимо загрузить в Excel, и нажмите кнопку **Загрузить**. 

    ![Загрузка данных в Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Загрузка данных в Excel")

После загрузки данных в книгу Excel с ними можно выполнять аналитические операции.

## <a name="connect-from-r"></a>Подключение из R

В этом разделе вы используете интегрированную среду разработки для языка R, чтобы обратиться к данным, доступным в Azure Databricks. Перед началом работы на вашем компьютере должны быть установлены следующие компоненты.

* Интегрированная среда разработки для языка R. В этой статье используется приложение RStudio для рабочего стола. Его можно установить со страницы [загрузки RStudio](https://www.rstudio.com/products/rstudio/download/).
* При использовании RStudio для рабочего стола в качестве интегрированной среды разработки установите Microsoft R Client, перейдя по адресу [http://aka.ms/rclient/](http://aka.ms/rclient/). 

Откройте RStudio и выполните описанные ниже действия.

- Укажите ссылку на пакет `RODBC`. Это даст возможность подключиться к Azure Databricks с помощью созданного ранее имени DSN.
- Установите подключение, используя имя DSN.
- Выполните SQL-запрос к данным в Azure Databricks. В следующем фрагменте кода *radio_sample_data* — это таблица, уже существующая в Azure Databricks.
- Выполните операции с запросом, чтобы проверить выходные данные. 

Следующий фрагмент кода выполняет эти задачи:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Подключение из Python

В этом разделе вы используете интегрированную среду разработки для Python (например, IDLE) для обращения к данным, доступным в Azure Databricks. Перед началом работы выполните следующее:

* Установите Python, перейдя по [этой ссылке](https://www.python.org/downloads/). Этот комплект также включает установку IDLE.

* С помощью командной строки на компьютере установите пакет `pyodbc`. Выполните следующую команду:

      pip install pyodbc

Откройте IDLE и выполните следующие действия.

- Импортируйте пакет `pyodbc`. Это даст возможность подключиться к Azure Databricks с помощью созданного ранее имени DSN.
- Установите подключение, используя созданное ранее имя DSN.
-  Выполните SQL-запрос с помощью созданного подключения. В следующем фрагменте кода *radio_sample_data* — это таблица, уже существующая в Azure Databricks.
- Выполните операции с запросом, чтобы проверить выходные данные.

Следующий фрагмент кода выполняет эти задачи:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об источниках, из которых можно выполнить импорт данных Azure Databricks см. [здесь](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


