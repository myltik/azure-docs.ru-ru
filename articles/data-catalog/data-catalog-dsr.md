---
title: "Источники данных, поддерживаемые в каталоге данных Azure | Документация Майкрософт"
description: "В этой статье перечислены спецификации источников данных, поддерживаемых в настоящее время."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: 6589edd535b513f8d1eb47e69f4fbcdd96a2f10d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Источники данных, поддерживаемые в каталоге данных Azure

Вы можете публиковать метаданные с помощью общедоступного API или средства быстрой регистрации, или вручную, указав данные непосредственно на веб-портале каталога данных Azure. В следующей таблице перечислены все источники данных, поддерживаемые каталогом в настоящее время, и возможности публикации для каждого из них. Здесь же перечислены внешние средства работы с данными, которые каждый источник данных может запускать из портала. Во второй таблице содержатся технические характеристики свойств подключения каждого источника данных.


## <a name="list-of-supported-data-sources"></a>Список поддерживаемых источников данных

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
      <td>Каталог Azure Data Lake Store</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Файл Azure Data Lake Store</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Хранилище больших двоичных объектов Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Каталог службы хранилища Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица служба хранилища Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td>
        <font size="2">
      </td>
      <td>
        <font size="2">
      </td>
    </tr>
    <tr>
      <td>Каталог HDFS</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Файл HDFS</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица Hive</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление Hive</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица MySQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление MySQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица базы данных Oracle</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление базы данных Oracle</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Другие (универсальный ресурс)</td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица хранилища данных SQL Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop, SQL Server Data Tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление хранилища данных SQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop, SQL Server Data Tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Размерность SQL Server Analysis Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Ключевые показатели эффективности SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Измерение SQL Server Analysis Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица SQL Server Analysis Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Отчет для служб SQL Server Reporting Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>"Обзор"</td>
      <td>Только серверы в основном режиме. Режим SharePoint не поддерживается.</td>
    </tr>
    <tr>
      <td>Таблица SQL Server</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop, SQL Server Data Tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление SQL Server</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel, Power BI Desktop, SQL Server Data Tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица Teradata</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление Teradata</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Представление SAP Hana</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td>Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица DB2</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Представление DB2</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Файл файловой системы</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Каталог FTP</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Файл FTP</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Отчет HTTP</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Конечная точка HTTP</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Файл HTTP</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Набор сущностей OData</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Функция OData</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица PostgreSQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Представление PostgreSQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Представление SAP Hana</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td> Объект SalesForce</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Список SharePoint </td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Коллекция Azure Cosmos DB</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Универсальная таблица ODBC</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Универсальное представление ODBC</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица Cassandra</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td></td>
      <td>Публикация в качестве универсального ресурса ODBC</td>
    </tr>
    <tr>
      <td>Представление Cassandra</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td></td>
      <td>Публикация в качестве универсального ресурса ODBC</td>
    </tr>
    <tr>
      <td>Таблица Sybase</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Представление Sybase</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Таблица MongoDB</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td></td>
      <td>Публикация в качестве универсального ресурса ODBC</td>
    </tr>
    <tr>
      <td>Представление MongoDB</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td></td>
      <td>Публикация в качестве универсального ресурса ODBC</td>
    </tr>
</table>

Если вам нужна поддержка определенного источника данных, предложите свой вариант (или проголосуйте за него, если он уже указан) на [форумах обратной связи каталога данных Azure](https://feedback.azure.com/forums/906052-data-catalog).


## <a name="data-source-reference-specification"></a>Спецификация ссылки на источник данных
> [!NOTE]
> Столбец **Структура DSL** в приведенной ниже таблице содержит только свойства подключения для контейнера свойств address, используемые каталогом данных Azure. Т. е. контейнер свойств address может содержать другие свойства подключения источника данных, которые хранятся в каталоге данных Azure, но не используются.

<table>
    <tr>
       <td><b>Тип источника</b></td>
       <td><b>Тип ресурса</b></td>
       <td><b>Типы объектов</b></td>
       <td><b>Структура DSL<b></td>
    </tr>
    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Контейнер</td>
      <td>Озеро данных</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>
        
            Protocol: azure-blobs
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Blob, directory</td>
      <td>
        
            Protocol: azure-blobs
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>
        
            Protocol: azure-tables
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        
            Protocol: azure-tables
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name
        
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Container</td>
      <td>Virtual cluster</td>
      <td>
        
            Protocol: cosmos
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Table</td>
      <td>Stream, stream set, view</td>
      <td>
        
            Protocol: cosmos
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: db2
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: db2
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
        
      </td>
    </tr>
    <tr>
      <td>File system</td>
      <td>Table</td>
      <td>File</td>
      <td>
        
            Protocol: file
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
        
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: ftp
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Container</td>
      <td>Cluster</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: hive
            <br>Authentication: {HDInsight, basic, username, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>connectionProperties:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2}
        
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: hive
            <br>Authentication: {HDInsight, basic, username, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>connectionProperties:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2}
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Table</td>
      <td>Endpoint, file</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: mysql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: mysql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Container</td>
      <td>Entity container</td>
      <td>
        
            Protocol: odata
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Table</td>
      <td>Entity set, function</td>
      <td>
        
            Protocol: odata
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource
        
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: oracle
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: oracle
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: postgresql
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: postgresql
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>Power BI Desktop</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Power BI Desktop</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Data mashup</td>
      <td>
        
            Protocol: power-query
            <br>Authentication: {oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Table</td>
      <td>Object</td>
      <td>
        
            Protocol: salesforce-com
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName
        
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Container</td>
      <td>Server</td>
      <td>
        
            Protocol: sap-hana-sql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
        
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Table</td>
      <td>View</td>
      <td>
        
            Protocol: sap-hana-sql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Table</td>
      <td>List</td>
      <td>
        
            Protocol: sharepoint-list
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Command</td>
      <td>Stored procedure</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Table-valued function</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: tds
          <br>Authentication: {protocol, windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Command</td>
      <td>Stored procedure</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Table-valued function</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Table</td>
      <td>Dimension</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Container</td>
      <td>Server</td>
      <td>
        
            Protocol: reporting-services
            <br>Authentication: {windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Report</td>
      <td>Report</td>
      <td>
        
            Protocol: reporting-services
            <br>Authentication: {windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010}
        
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: teradata
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: teradata
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        <font size="2">
          Protocol: mssql-mds
          <br>Authentication: {windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Table</td>
      <td>Entity</td>
      <td>
        <font size="2">
          Protocol: mssql-mds
          <br>Authentication: {windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity
        
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: document-db
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Collection</td>
      <td>Collection</td>
      <td>
        
            Protocol: document-db
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; collection
        
      </td>
    </tr>
    <tr>
      <td>Generic ODBC</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: odbc
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Generic ODBC</td>
      <td>Table</td>
      <td>Table, View</td>
      <td>
        
            Protocol: odbc
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
        
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            protocol: sybase
            <br>authentication: {basic, windows}
            <br>address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Table</td>
      <td>Table, View</td>
      <td>
        
            protocol: sybase
            <br>authentication: {basic, windows}
            <br>address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>Other (none of the above)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        
            Protocol: generic-asset
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId
        
      </td>
    </tr>
</table>
