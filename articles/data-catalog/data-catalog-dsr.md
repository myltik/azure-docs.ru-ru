<properties
   pageTitle="Источники данных, поддерживаемые каталогом данных Azure | Microsoft Azure"
   description="Спецификация источников данных, поддерживаемых на данный момент."
   services="data-catalog"
   documentationCenter=""
   authors="trhabe"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="trhabe"/>

# Источники данных, поддерживаемые каталогом данных Azure

Пользователи каталога данных Azure могут публиковать метаданные с помощью общедоступного API, средства быстрой регистрации или вручную, указав данные непосредственно на веб-портале каталога данных. В приведенной ниже таблице указаны все источники, поддерживаемые каталогом на сегодняшний день, а также соответствующие каждому из них возможности публикации. Здесь же перечислены внешние средства работы с данными, которые каждый источник может запускать из портала. Во второй таблице, приведенной в этой статье, содержатся более технические характеристики свойств подключения каждого источника данных.


## Список поддерживаемых источников данных

<table>

    <tr>
       <td><b>Объект источника данных</b></td>
       <td><b>API</b></td>
       <td><b>Ручной ввод</b></td>
       <td><b>Средство регистрации</b></td>
       <td><b>Средства Open-In</b></td>
       <td><b>Примечания</b></td>
    </tr>

    <tr>
      <td>Каталог хранилища озера данных Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл хранилища озера данных Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Большой двоичный объект хранилища Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Каталог хранилища Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица хранилища Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Каталог HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>таблица базы данных Oracle;</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>представление базы данных Oracle;</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Другие (универсальный актив)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица хранилища данных SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление хранилища данных SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Размерность SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ключевые показатели эффективности SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Измерение SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>отчет для служб SQL Server Reporting Services.</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>"Обзор"</font></td>
      <td><font size=2>Только серверы в основном режиме. Режим SharePoint не поддерживается.</font></td>
    </tr>

    <tr>
      <td>таблица SQL Server;</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>представление SQL Server;</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление SAP Hana</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>Только представления расчетов и аналитики. Представления атрибутов не поддерживаются.</font></td>
    </tr>

    <tr>
      <td>Таблица Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл файловой системы</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Каталог FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Отчет HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Конечная точка HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Набор сущностей OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Функция OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление SAP Hana</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Объект SalesForce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Список SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Если вам требуется поддержка дополнительных источников, отправьте запрос функции с помощью [форума по каталогу данных Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br> <br>
## Спецификация ссылки на источник данных
> [AZURE.NOTE] Столбец "Структура DSL" в приведенной ниже таблице содержит только свойства подключения для контейнера свойств address, используемые каталогом данных Azure (т. е. контейнер свойств address может содержать другие свойства подключения источника данных, которые хранятся в каталоге данных Azure, но не используются).
<table>
    <tr>
       <td><b>Тип источника</b></td>
       <td><b>Тип ресурса</b></td>
       <td><b>Типа объектов</b></td>
       <td><b>Структура DSL<b></td>
    </tr>
    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Контейнер</td>
      <td>Озеро данных</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>аутентификация: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Таблица</td>
      <td>Каталог, файл</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>аутентификация: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Контейнер</td>
      <td>Контейнер</td>
      <td>
        <font size=2> протокол: azure-blobs
            <br>проверка подлинности: {azure-access-key}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; домен
            <br>&#160;&#160;&#160;&#160;&#160; учетная запись
            <br>&#160;&#160;&#160;&#160;&#160; контейнер </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Таблица</td>
      <td>Большой двоичный объект, каталог</td>
      <td>
        <font size=2> протокол: azure-blobs
            <br>проверка подлинности: {azure-access-key}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; домен
            <br>&#160;&#160;&#160;&#160;&#160; учетная запись
            <br>&#160;&#160;&#160;&#160;&#160; контейнер
            <br>&#160;&#160;&#160;&#160;&#160; имя </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Контейнер</td>
      <td>Контейнер</td>
      <td>
        <font size=2> протокол: azure-tables
            <br>проверка подлинности: {azure-access-key}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; домен
            <br>&#160;&#160;&#160;&#160;&#160; учетная запись </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>
        <font size=2> протокол: azure-tables
            <br>проверка подлинности: {azure-access-key}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; домен
            <br>&#160;&#160;&#160;&#160;&#160; учетная запись
            <br>&#160;&#160;&#160;&#160;&#160; имя </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Контейнер</td>
      <td>Виртуальный кластер</td>
      <td>
        <font size=2> протокол: cosmos
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Таблица</td>
      <td>Поток, набор потоков, представление</td>
      <td>
        <font size=2> протокол: cosmos
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Отчет</td>
      <td>Отчет, панель мониторинга</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: db2
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: db2
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; schema </font>
      </td>
    </tr>
    <tr>
      <td>Файловая система</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>
        <font size=2> протокол: файл
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; путь </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Таблица</td>
      <td>Каталог, файл</td>
      <td>
        <font size=2> протокол: ftp
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Распределенная файловая система Hadoop</td>
      <td>Контейнер</td>
      <td>HDInsight</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>аутентификация: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Распределенная файловая система Hadoop</td>
      <td>Таблица</td>
      <td>Каталог, файл</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>аутентификация: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: hive
            <br>проверка подлинности: {hdinsight, basic, username, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: hive
            <br>проверка подлинности: {hdinsight, basic, username, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Отчет</td>
      <td>Отчет, панель мониторинга</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Таблица</td>
      <td>Конечная точка, файл</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: mysql
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: mysql
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Контейнер</td>
      <td>Контейнер сущностей</td>
      <td>
        <font size=2> протокол: odata
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Таблица</td>
      <td>Набор сущностей, функция</td>
      <td>
        <font size=2> протокол: odata
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; ресурс </font>
      </td>
    </tr>
    <tr>
      <td>База данных Oracle</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: oracle
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>База данных Oracle</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: oracle
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: postgresql
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: postgresql
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Отчет</td>
      <td>Отчет, панель мониторинга</td>
      <td>
        <font size=2> протокол: http
            <br>аутентификация: {none, basic, windows, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Таблица</td>
      <td>Гибридные данные</td>
      <td>
        Протокол <font size=2>: Power Query
            <br>Аутентификация: {oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Таблица</td>
      <td>Объект</td>
      <td>
        <font size=2> протокол: salesforce-com
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Контейнер</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: sap-hana-sql
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; сервер </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>
        <font size=2> протокол: sap-hana-sql
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Таблица</td>
      <td>список</td>
      <td>
        <font size=2> протокол: sharepoint-list
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>Команда</td>
      <td>Хранимая процедура</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>TableValuedFunction</td>
      <td>Функция с табличным значением</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
          <br>проверка подлинности: {protocol, windows}
          <br>адрес:
          <br>&#160;&#160;&#160;&#160;&#160; server
          <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Команда</td>
      <td>Хранимая процедура</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Функция с табличным значением</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Ключевой показатель эффективности</td>
      <td>Ключевой показатель эффективности</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Таблица</td>
      <td>Измерение</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Ключевой показатель эффективности</td>
      <td>Ключевой показатель эффективности</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>аутентификация: {windows, basic, anonymous, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>Службы отчетов SQL Server</td>
      <td>Контейнер</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: reporting-services
            <br>проверка подлинности: {windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Службы отчетов SQL Server</td>
      <td>Отчет</td>
      <td>Отчет</td>
      <td>
        <font size=2> протокол: reporting-services
            <br>проверка подлинности: {windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: teradata
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: teradata
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>
        <font size="2"> протокол: mssql-mds
          <br>проверка подлинности: {windows}
          <br>адрес:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; модель
          <br>&#160;&#160;&#160;&#160;&#160; версия </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Таблица</td>
      <td>Сущность</td>
      <td>
        <font size="2"> протокол: mssql-mds
          <br>проверка подлинности: {windows}
          <br>адрес:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; модель
          <br>&#160;&#160;&#160;&#160;&#160; версия
          <br>&#160;&#160;&#160;&#160;&#160; сущность </font>
      </td>
    </tr>
    <tr>
      <td>Другой (ни один из указанных выше)</td>
      <td>*</td>
      <td>*</td>
      <td>
        <font size=2> протокол: generic-asset
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>
</table>

<!---HONumber=AcomDC_0921_2016-->