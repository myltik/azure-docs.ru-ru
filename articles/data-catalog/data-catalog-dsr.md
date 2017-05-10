---
title: "Источники данных, поддерживаемые каталогом данных Azure | Документация Майкрософт"
description: "Спецификация источников данных, поддерживаемых на данный момент."
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
ms.date: 03/14/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: d68ed62022834c9417d820a7a64e30b47f71a1bd
ms.contentlocale: ru-ru
ms.lasthandoff: 03/31/2017


---

# <a name="azure-data-catalog-supported-data-sources"></a>Источники данных, поддерживаемые каталогом данных Azure

Вы можете публиковать метаданные с помощью общедоступного API, средства быстрой регистрации или вручную, указав данные непосредственно на веб-портале каталога данных. В приведенной ниже таблице указаны все источники, поддерживаемые каталогом на сегодняшний день, а также соответствующие каждому из них возможности публикации.  Здесь же перечислены внешние средства работы с данными, которые каждый источник может запускать из портала. Во второй таблице содержатся более технические характеристики свойств подключения каждого источника данных.


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
      <td>Каталог хранилища озера данных Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл хранилища озера данных Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Большой двоичный объект хранилища Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Каталог хранилища Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица хранилища Azure</td>
      <td>✓</td>
      <td>✓ </td>
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
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>таблица базы данных Oracle;</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>представление базы данных Oracle;</td>
      <td>✓</td>
      <td>✓ </td>
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
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление хранилища данных SQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Размерность SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ключевые показатели эффективности SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Измерение SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>отчет для служб SQL Server Reporting Services.</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>"Обзор"</font></td>
      <td><font size=2>Только серверы в основном режиме. Режим SharePoint не поддерживается.</font></td>
    </tr>

    <tr>
      <td>таблица SQL Server;</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>представление SQL Server;</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление SAP Hana</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Таблица Db2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление Db2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
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
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Файл FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
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
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Представление PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
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
      <td>✓ </td>
      <td>✓</td>
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
  
    <tr>
      <td>Коллекция Azure DocumentDB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Универсальная таблица ODBC</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Универсальное представление ODBC</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Если вам требуется поддержка дополнительных источников, отправьте запрос функции с помощью [форума по каталогу данных Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Спецификация ссылки на источник данных
> [!NOTE]
> Столбец "Структура DSL" в приведенной ниже таблице содержит только свойства подключения для контейнера свойств address, используемые каталогом данных Azure. Т. е. контейнер свойств address может содержать другие свойства подключения источника данных, которые хранятся в каталоге данных Azure, но не используются.
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
        <font size=2> протокол: webhdfs <br>аутентификация: {basic, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище озера данных Azure</td>
      <td>Таблица</td>
      <td>Каталог, файл</td>
      <td>
        <font size=2> протокол: webhdfs <br>аутентификация: {basic, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Контейнер</td>
      <td>Контейнер</td>
      <td>
        <font size=2> протокол: azure-blobs <br>проверка подлинности: {azure-access-key} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Таблица</td>
      <td>Большой двоичный объект, каталог</td>
      <td>
        <font size=2> протокол: azure-blobs <br>проверка подлинности: {azure-access-key} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Контейнер</td>
      <td>Контейнер</td>
      <td>
        <font size=2> протокол: azure-tables <br>проверка подлинности: {azure-access-key} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище Azure</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>
        <font size=2> протокол: azure-tables <br>проверка подлинности: {azure-access-key} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Контейнер</td>
      <td>Виртуальный кластер</td>
      <td>
        <font size=2> протокол: cosmos <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Таблица</td>
      <td>Поток, набор потоков, представление</td>
      <td>
        <font size=2> протокол: cosmos <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Отчет</td>
      <td>Отчет, панель мониторинга</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: db2 <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: db2 <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>Файловая система</td>
      <td>Таблица</td>
      <td>Файл</td>
      <td>
        <font size=2> протокол: файл <br>проверка подлинности: {none, basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Таблица</td>
      <td>Каталог, файл</td>
      <td>
        <font size=2> протокол: ftp <br>проверка подлинности: {none, basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Распределенная файловая система Hadoop</td>
      <td>Контейнер</td>
      <td>HDInsight</td>
      <td>
        <font size=2> протокол: webhdfs <br>аутентификация: {basic, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Распределенная файловая система Hadoop</td>
      <td>Таблица</td>
      <td>Каталог, файл</td>
      <td>
        <font size=2> протокол: webhdfs <br>аутентификация: {basic, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: hive <br>проверка подлинности: {hdinsight, basic, username, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: hive <br>проверка подлинности: {hdinsight, basic, username, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Отчет</td>
      <td>Отчет, панель мониторинга</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Таблица</td>
      <td>Конечная точка, файл</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: mysql <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: mysql <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Контейнер</td>
      <td>Контейнер сущностей</td>
      <td>
        <font size=2> протокол: odata <br>проверка подлинности: {none, basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Таблица</td>
      <td>Набор сущностей, функция</td>
      <td>
        <font size=2> протокол: odata <br>проверка подлинности: {none, basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource </font>
      </td>
    </tr>
    <tr>
      <td>База данных Oracle</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: oracle <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>База данных Oracle</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: oracle <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: postgresql <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: postgresql <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Контейнер</td>
      <td>Сайт</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Отчет</td>
      <td>Отчет, панель мониторинга</td>
      <td>
        <font size=2> протокол: http <br>аутентификация: {none, basic, windows, oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Таблица</td>
      <td>Гибридные данные</td>
      <td>Протокол 
        <font size=2>: Power Query <br>Аутентификация: {oauth} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Таблица</td>
      <td>Объект</td>
      <td>
        <font size=2> протокол: salesforce-com <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Контейнер</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: sap-hana-sql <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Таблица</td>
      <td>Просмотр</td>
      <td>
        <font size=2> протокол: sap-hana-sql <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Таблица</td>
      <td>список</td>
      <td>
        <font size=2> протокол: sharepoint-list <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>Команда</td>
      <td>Хранимая процедура</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>TableValuedFunction</td>
      <td>Функция с табличным значением</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Хранилище данных SQL</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Команда</td>
      <td>Хранимая процедура</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Функция с табличным значением</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: tds <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Ключевой показатель эффективности</td>
      <td>Ключевой показатель эффективности</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Многомерная модель служб SQL Server Analysis Services</td>
      <td>Таблица</td>
      <td>Измерение</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Ключевой показатель эффективности</td>
      <td>Ключевой показатель эффективности</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Таблица служб аналитики SQL Server</td>
      <td>Таблица</td>
      <td>Таблица</td>
      <td>
        <font size=2> протокол: analysis-services <br>аутентификация: {windows, basic, anonymous, none} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>Службы отчетов SQL Server</td>
      <td>Контейнер</td>
      <td>сервер;</td>
      <td>
        <font size=2> протокол: reporting-services <br>проверка подлинности: {windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Службы отчетов SQL Server</td>
      <td>Отчет</td>
      <td>Отчет</td>
      <td>
        <font size=2> протокол: reporting-services <br>проверка подлинности: {windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: teradata <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> протокол: teradata <br>проверка подлинности: {protocol, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Контейнер</td>
      <td>Модель</td>
      <td>
        <font size="2"> протокол: mssql-mds <br>проверка подлинности: {windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Таблица</td>
      <td>Сущность</td>
      <td>
        <font size="2"> протокол: mssql-mds <br>проверка подлинности: {windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB.</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> протокол: document-db <br>проверка подлинности: {azure-access-key} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB.</td>
      <td>Коллекция</td>
      <td>Коллекция</td>
      <td>
        <font size=2> протокол: document-db <br>проверка подлинности: {azure-access-key} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; collection </font>
      </td>
    </tr>
    <tr>
      <td>Универсальные данные ODBC</td>
      <td>Контейнер</td>
      <td>База данных</td>
      <td>
        <font size=2> protocol: odbc <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Универсальные данные ODBC</td>
      <td>Таблица</td>
      <td>Таблица, представление</td>
      <td>
        <font size=2> protocol: odbc <br>Проверка подлинности: {basic, windows} <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>Другой (ни один из указанных выше)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> протокол: generic-asset <br>адрес: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>

