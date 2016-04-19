<properties
   pageTitle="Источники данных, поддерживаемые каталогом данных Azure | Microsoft Azure"
   description="В этой статье перечислены все типы источников данных и ресурсов-контейнеров данных, которые можно регистрировать в каталоге данных Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="maroche"/>

# Источники данных, поддерживаемые каталогом данных Azure

Пользователи **каталога данных Azure** могут публиковать метаданные с помощью общедоступного API, инструмента регистрации источников данных ClickOnce или вручную, указав данные непосредственно на веб-портале каталога данных.

В приведенной ниже таблице указаны все источники, поддерживаемые каталогом данных на сегодняшний день, а также соответствующие каждому из них возможности публикации. Здесь же перечислены внешние инструменты данных, которые каждый источник может запускать с портала каталога данных с помощью функции "Открыть".

Еще ниже приведена вторая таблица, в которой содержатся технические характеристики свойств каждого подключения к источнику данных и спецификации ссылок на источники данных (DSR), используемых для каждого поддерживаемого ресурса-контейнера данных при применении API каталога данных.


## Список поддерживаемых источников данных и ресурсов-контейнеров

<table>

    <tr>
       <td><b>Ресурс-контейнер данных</b></td>
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
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление хранилища данных SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
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
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>таблица SQL Server;</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>представление SQL Server;</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
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
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление DB2</td>
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


<br> <br>

## Спецификация ссылки на источник данных

<table>
    <tr>
       <td><b>Тип источника</b></td>
       <td><b>Корневой тип</b></td>
       <td><b>Тип объекта</b></td>
       <td><b>Входит в тип объекта</b></td>
       <td><b>Структура DSR<b></td>
    </tr>


    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Таблица</td>
      <td>Каталог</td>
      <td>Озеро данных</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>проверка подлинности: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>Озеро данных</td>
      <td>
        <font size=2> протокол: webhdfs}
            <br>проверка подлинности: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>



    <tr>
      <td>Службы отчетов SQL Server</td>
      <td>Контейнер</td>
      <td>сервер;</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: reporting-services
            <br>проверка подлинности: {windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; версия {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище Azure</td>
      <td>Таблица</td>
      <td>BLOB-объект</td>
      <td>Контейнер</td>
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
      <td>Таблица</td>
      <td>Каталог</td>
      <td>Контейнер</td>
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
      <td>Таблица</td>
      <td>Таблица</td>
      <td>Хранилище Azure</td>
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
      <td>Службы аналитики SQL Server</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>проверка подлинности: {windows, basic, anonymous}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>
            <br>*basic/anonymous; доступ только по HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: db2
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: db2
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: db2
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>Файловая система</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: файл
            <br>Проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; путь </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Таблица</td>
      <td>Каталог</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: ftp
            <br>Проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: ftp
            <br>Проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>проверка подлинности: {windows, basic, anonymous}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>
            <br>*basic/anonymous; доступ только по HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>Распределенная файловая система Hadoop</td>
      <td>Таблица</td>
      <td>Каталог</td>
      <td>HDInsight</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>проверка подлинности: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Распределенная файловая система Hadoop</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>HDInsight</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>проверка подлинности: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Контейнер</td>
      <td>Озеро данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>проверка подлинности: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: hive
            <br>проверка подлинности: {hdinsight, basic, username}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: hive
            <br>проверка подлинности: {hdinsight, basic, username}
            <br>адрес: server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище Azure</td>
      <td>Контейнер</td>
      <td>Хранилище Azure</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: azure-tables
            <br>проверка подлинности: {azure-access-key}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; домен
            <br>&#160;&#160;&#160;&#160;&#160; учетная запись </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: http
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Отчет</td>
      <td>Отчет</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Таблица</td>
      <td>Конечная точка</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище Azure</td>
      <td>Контейнер</td>
      <td>Контейнер</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: azure-blobs
            <br>проверка подлинности: {Azure-Access-Key}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; домен
            <br>&#160;&#160;&#160;&#160;&#160; учетная запись
            <br>&#160;&#160;&#160;&#160;&#160; контейнер </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
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
      <td>MySQL</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
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
      <td>Распределенная файловая система Hadoop</td>
      <td>Контейнер</td>
      <td>HDInsight</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: webhdfs
            <br>проверка подлинности: {basic, oauth}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Контейнер</td>
      <td>Контейнер сущностей</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: odata
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: hiveserver2
            <br>проверка подлинности: {hdinsight, basic, username, none}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; порт
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Таблица</td>
      <td>Набор сущностей</td>
      <td>Контейнер сущностей</td>
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
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
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
      <td>База данных Oracle</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
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
      <td>OData</td>
      <td>Таблица</td>
      <td>Функция</td>
      <td>Контейнер сущностей</td>
      <td>
        <font size=2> протокол: odata
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; ресурс </font>
      </td>
    </tr>

    <tr>
      <td>Другие</td>
      <td>Таблица</td>
      <td>Другие</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: generic-asset
            <br>проверка подлинности: {none, basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: postgresql
            <br>Проверка подлинности: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: postgresql
            <br>Проверка подлинности: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: postgresql
            <br>Проверка подлинности: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: mysql
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>сервер;</td>
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
      <td>Salesforce</td>
      <td>Таблица</td>
      <td>Объект</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: salesforce-com
            <br>проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>

    <tr>
      <td>SharePoint</td>
      <td>Таблица</td>
      <td>список</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: sharepoint-list
            <br>Проверка подлинности: {basic, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; URL </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище данных SQL</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище данных SQL</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Таблица</td>
      <td>Функция с табличным значением</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>

    <tr>
      <td>База данных Oracle</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: oracle
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>

    <tr>
      <td>Службы аналитики SQL Server</td>
      <td>Ключевой показатель эффективности</td>
      <td>Ключевой показатель эффективности</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>проверка подлинности: {windows, basic, anonymous}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Kpi}
            <br>
            <br>*basic/anonymous; доступ только по HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>Службы аналитики SQL Server</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>проверка подлинности: {windows, basic, anonymous}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>

    <tr>
      <td>Службы аналитики SQL Server</td>
      <td>Таблица</td>
      <td>Измерение</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>проверка подлинности: {windows, basic, anonymous}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>

    <tr>
      <td>SAP Hana</td>
      <td>Контейнер</td>
      <td>сервер;</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: sap-hana-sql
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; сервер </font>
      </td>
    </tr>

    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services
            <br>проверка подлинности: {windows, basic, anonymous}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; модель
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table}
            <br>
            <br>*basic/anonymous; доступ только по HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>Хранилище данных SQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
      <td>
        <font size=2> протокол: tds
            <br>проверка подлинности: {sql, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; база данных </font>
      </td>
    </tr>

    <tr>
      <td>Службы отчетов SQL Server</td>
      <td>Отчет</td>
      <td>Отчет</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: reporting-services
            <br>проверка подлинности: {windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; версия {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>Недоступно</td>
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
      <td>Таблица</td>
      <td>База данных</td>
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
      <td>Teradata</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: teradata
            <br>проверка подлинности: {protocol, windows}
            <br>адрес:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; объект </font>
      </td>
    </tr>



</table>

<!---HONumber=AcomDC_0406_2016-->