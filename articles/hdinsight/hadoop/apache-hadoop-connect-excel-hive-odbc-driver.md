---
title: Подключение Excel к Hadoop с помощью драйвера Hive ODBC в Azure HDInsight | Документация Майкрософт
description: Узнайте, как установить и использовать драйвер Microsoft Hive ODBC для Excel, чтобы запрашивать данные в кластерах HDInsight из Microsoft Excel.
keywords: hadoop excel, hive excel, hive odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 26234ca17d833fef01ad5a6465824c99d84cc556
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200509"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Подключение Excel к Hadoop в Azure HDInsight с помощью драйвера Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Решение Майкрософт для работы с данными большого размера интегрирует компоненты бизнес-аналитики Майкрософт с кластерами Apache Hadoop, которые были развернуты с помощью Azure HDInsight. Примером такой интеграции является возможность подключения Excel к хранилищу данных Hive на кластере Hadoop в HDInsight с помощью драйвера Microsoft Hive ODBC.

Можно также подключить данные, связанные с кластером HDInsight, и другими источниками данных, включая другие кластеры Hadoop (не HDInsight) из Excel с использованием надстройки Microsoft Power Query для Excel. Сведения об установке и использовании Power Query см. в статье [Подключение Excel к Hadoop с помощью Power Query][hdinsight-power-query].



**Предварительные требования**:

Перед началом работы с этой статьей необходимо иметь следующее:

* **Кластер HDInsight**. Дополнительные сведения о создании кластера см. в статье [Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Рабочая станция** с Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

## <a name="install-microsoft-hive-odbc-driver"></a>Установка драйвера Microsoft Hive ODBC
Скачайте и установите драйвер Microsoft Hive ODBC из [Центра загрузок][hive-odbc-driver-download].

Этот драйвер можно установить на 32-разрядной или 64-разрядной версии Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 и Windows Server 2012. Драйвер позволяет подключиться к Azure HDInsight. Необходимо установить версию, которая соответствует версии приложения, на котором будет использоваться драйвер ODBC. В рамках этого руководства используется драйвер из Office Excel.

## <a name="create-hive-odbc-data-source"></a>Создание источника данных Hive ODBC
Ниже показано, как создать источник данных Hive ODBC.

1. В Windows 8 или Windows 10 нажмите клавишу Windows, чтобы открыть начальный экран, затем введите **источники данных**.
2. Нажмите кнопку **Настройка источников данных ODBC (32-разр.)** или **Настройка источников данных ODBC (64-разр.)** в зависимости от версии Office. Если вы используете Windows 7, выберите **Источники данных ODBC (32-разр.)** или **Источники данных ODBC (64-разр.)** в разделе **Администрирование**. Вы увидите диалоговое окно **Администратор источников данных ODBC**.
   
    ![Администратор источников данных ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Настройка DSN с помощью администратора источников данных ODBC")

3. Из DNS пользователя щелкните **Добавить**, чтобы открыть мастер **Создать новый источник данных**.
4. Выберите **Драйвер Microsoft Hive ODBC** и щелкните **Готово**. Вы увидите диалоговое окно **Microsoft Hive ODBC Driver DNS Setup** (Настройка DNS-драйвера Microsoft Hive ODBC).
5. Введите или выберите следующие значения:
   
   | Свойство | ОПИСАНИЕ |
   | --- | --- |
   |  Имя источника данных |Присвойте имя источнику данных |
   |  Узел |Введите &lt;имя_кластера_HDInsight>.azurehdinsight.net. Например, myHDICluster.azurehdinsight.net |
   |  Порт |Используйте <strong>443</strong>. (Этот порт был изменен с 563 на 443.) |
   |  База данных |Используйте <strong>значение по умолчанию</strong>. |
   |  Механизм |Выберите <strong>Служба Azure HDInsight</strong>. |
   |  Имя пользователя |Введите имя пользователя HTTP кластера HDInsight. Имя пользователя по умолчанию — <strong>admin</strong>. |
   |  Пароль |Введите пароль пользователя кластера HDInsight. |
   
    </table>
   
    Существует несколько важных параметров, о которых следует помнить при выборе пункта **Расширенные параметры**.
   
   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   |  Использовать исходный запрос |При выборе этого параметра драйвер ODBC НЕ пытается преобразовать TSQL в HiveQL. Следует использовать только при 100% уверенности в отправке действительных инструкций HiveQL. При подключении к серверу SQL Server или базе данных Azure SQL необходимо снять этот флажок. |
   |  Строки, загружаемые для каждого блока |При получении большого объема записей включение этого параметра может обеспечить оптимальную производительность. |
   |  Длина столбца строки по умолчанию, длина столбца двоичного кода, масштаб столбца десятичных значений |Длина и точность типа данных может повлиять на способ выведения данных. Это приведет к возврату недопустимой информации из-за потери точности и (или) усечения. |

    ![Дополнительные параметры](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Дополнительные параметры конфигурации DSN")

1. Нажмите **Тест** для проверки источника данных. При правильной настройке источника данных здесь отображается *ТЕСТЫ УСПЕШНО ЗАВЕРШЕНЫ!*
2. Нажмите **OK** , чтобы закрыть диалоговое окно Тест. Новый источник данных не должен быть указан в **администраторе источника данных ODBC**.
3. Нажмите **OK** , чтобы завершить работу мастера.

## <a name="import-data-into-excel-from-hdinsight"></a>Импорт данных в Excel из службы HDInsight
Ниже описан способ импорта данных из таблицы Hive в рабочую книгу Excel с помощью источника данных ODBC, созданного в предыдущем разделе.

1. Откройте новую или существующую рабочую книгу в Excel.
2. На вкладке **Данные** щелкните **Получить данные**, **Из других источников**, а затем выберите **Из ODBC**, чтобы запустить **мастер подключения к данным**.
   
    ![Открытие мастера подключения к данным](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Открытие мастера подключения к данным")
4. Выберите имя источника данных, который вы создали в предыдущем разделе, и нажмите кнопку **ОК**.
5. Введите имя пользователя Hadoop (имя по умолчанию — admin) и пароль, затем нажмите кнопку **Подключить**.
6. В навигаторе разверните узлы **HIVE**, **по умолчанию**, выберите **hivesampletable**, а затем нажмите кнопку **Загрузить**. Пройдет несколько секунд, прежде чем данные будут импортированы в Excel.

    ![Навигатор ODBC Hive в HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Открытие мастера подключения данных")


## <a name="next-steps"></a>Дополнительная информация
В рамках этой статьи вы узнали, как получить данные из службы HDInsight в Excel с помощью драйвера Microsoft Hive ODBC. Аналогичным образом можно получать данные из службы HDInsight в базу данных SQL. Можно также передавать данные в службу HDInsight. Дополнительные сведения см. на следующих ресурсах:

* [Визуализация данных Hive с помощью Microsoft Power BI в Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./../hdinsight-connect-hive-zeppelin.md).
* [Подключение Excel к Hadoop с помощью Power Query](apache-hadoop-connect-excel-power-query.md).
* [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


