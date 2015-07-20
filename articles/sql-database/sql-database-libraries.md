<properties
	pageTitle="Библиотеки подключений для базы данных SQL и SQL Server"
	description="Приведен минимальный номер версии каждого драйвера, которым могут воспользоваться клиентские программы для подключения к базе данных SQL Azure или Microsoft SQL Server. Ссылка на сведения о версиях драйверов, выпущенных сообществом разработчиков, а не корпорацией Майкрософт."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="pehteh"/>


# Библиотеки подключений для базы данных SQL и SQL Server


В этой теме приведен минимальный номер версии для каждой библиотеки или драйвера, которыми могут воспользоваться клиентские программы для подключения к базе данных SQL Azure или Microsoft SQL Server.


Эта тема состоит из двух разделов:


- *Таблица с библиотеками драйверов, выпущенными корпорацией Майкрософт*, охватывает библиотеки, выпущенные Майкрософт. Майкрософт поддерживает актуальность сведений, содержащихся в этом разделе.
- В разделе *Библиотеки сторонних производителей* представлен список библиотек, выпущенных и поддерживаемых сторонними производителями, не имеющими отношения к Майкрософт. **Этот раздел поддерживается только открытым сообществом разработчиков. Майкрософт не будет поддерживать этот раздел.**


## Таблица с библиотеками драйверов, выпущенными корпорацией Майкрософт


В следующей таблице представлены библиотеки, выпущенные Майкрософт. В столбце **Библиотеки** находятся ссылки, по которым можно скачать каждую библиотеку. В столбце **Версия** представлен минимальный номер версии, рекомендованной для взаимодействия с базой данных SQL Azure и Microsoft SQL Server.


| платформа | Oper Sys | Библиотеки<br/>для загрузки | Версия<br/>драйвера | Описание<br/>драйвера | Дополнительные<br/>сведения |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | Кроссплатформенная (.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5+ | Поставщик SQL Server для платформы .NET Framework | . |
| PHP | Windows | [PHP для SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2.0+ | Драйвер PHP для SQL Server | [Ссылка](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC для SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2.0+ | Драйвер JDBC типа 4, который обеспечивает подключение к базе данных через стандартные API JDBC | [Ссылка](http://msdn.microsoft.com/library/dn425070.aspx) |
| ODBC | Windows | [ODBC для SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11.0+ | Драйвер Microsoft ODBC для SQL Server | [Ссылка](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC для SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11.0+ | Драйвер Microsoft ODBC для SQL Server | . |
| ODBC | Redhat Linux | [ODBC для SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11.0+ | Драйвер Microsoft ODBC для SQL Server | . |


### OLEDB для DB2 и SQL Server для разработки DRDA


Поставщик данных Microsoft OLE DB для DB2 версии 5.0 (поставщик данных) позволяет создавать распределенные приложения, предназначенные для баз данных IBM DB2. Поставщик данных использует архитектуру доступа к данным Microsoft SQL Server вместе с сетевым клиентом Microsoft для DB2, который выполняет роль приложения, выполняющего запрос для распределенной реляционной базы данных DRDA. Поставщик данных преобразует команды COM OLE DB и типы данных в кодовых точках протокола и форматы данных DRDA.


Дополнительные сведения см. в следующих статьях:


- [Поставщик Microsoft OLE DB для DB2 версии 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Поставщик Microsoft OLE DB для DB2 версии 4.0 для Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)


## Библиотеки сторонних производителей


> [AZURE.IMPORTANT]В следующей таблице представлены библиотеки, выпускаемых сторонними производителями в соответствии с условиями лицензии для стороннего программного обеспечения. Вы несете ответственность за проверку и соблюдение соответствующих лицензий сторонних производителей для использования этих библиотек. Вы берете на себя риски, связанные с использованием этих библиотек. Корпорация Майкрософт не предоставляет никаких гарантий, явных или подразумеваемых, относительно предоставленной здесь информации и предоставляет эти данные исключительно для удобства пользователей. Данный документ не подразумевает какого-либо одобрения со стороны корпорации Майкрософт. <br/><br/>Обновление и поддержка информации, находящейся в разделе "Библиотеки сторонних производителей", возлагается на открытое сообщество разработчиков за счет использования репозитория [azure-content](http://github.com/Azure/azure-content/), который принадлежит **Azure** на сайте GitHub.com. Корпорация Майкрософт рекомендует разработчикам самим обновлять этот раздел. Персонал Майкрософт *НЕ* намерен поддерживать размещенную в нем информацию, в частности из-за того, что по каждой конкретной библиотеке стороннего производителя есть другие, более опытные специалисты. Спасибо.


В следующей таблице представлены библиотеки, выпускаемые сторонними производителями, например другими компаниями или сообществами. Библиотеки, выпускаемых корпорацией Майкрософт, представлены в предыдущем разделе этой темы.


| платформа | Библиотеки |
| :-- | :-- |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk, github io)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=July15_HO2-->