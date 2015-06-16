<properties 
   pageTitle="Использование соединителей" 
   description="Использование соединителей" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>

#Соединители#
Соединители — это приложения API, позволяющие подключаться к данным и службам, работающим в облаке или локально. Соединители упрощают получение данных с помощью множества встроенных триггеров и действий, доступ к которым можно легко получить в приложениях логики, и многое другое.

Службы приложений Azure предоставляет ряд соединителей по умолчанию.

##Стандартные соединители##
* [Соединитель служебной шины Azure]
* [Соединитель больших двоичных объектов службы хранилища Azure]
* Соединитель Azure Webjobs
* [Соединитель Box]
* [Соединитель Chatter]
* [Соединитель Dropbox]
* [Соединитель Facebook]
* [Соединитель File]
* [Соединитель FTP]
* [Соединитель HDInsight]
* [Соединитель HTTP]
* [Соединитель Office 365]
* [Соединитель OneDrive]
* [Соединитель Oracle]
* [Соединитель POP3]
* [Соединитель QuickBooks]
* [Соединитель Salesforce]
* [Соединитель SFTP]
* [Соединитель Sharepoint]
* [Соединитель Slack]
* [Соединитель SMTP]
* [Соединитель SQL]
* [Соединитель SugarCRM]
* [Соединитель Twilio]
* [Соединитель Twitter]
* Соединитель Wait
* [Соединитель Yammer]

##Соединители Premium и приложения API##
* Соединитель AS2
* BizTalk EDIFACT
* Кодировщик неструктурированных файлов BizTalk
* Служба преобразования BizTalk
* Правила BizTalk
* BizTalk X12
* Средство извлечения XPath BizTalk
* Средство проверки XML BizTalk
* Соединитель DB2
* Соединитель Informix
* Соединитель баз данных Oracle
* Соединитель MQ
* [Соединитель SAP]
 
Дополнительные сведения см. в справочнике по соединителям API [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).

##Соединители и приложения логики##
Приложения логики состоят из триггеров и действий. Некоторые соединители можно использовать как триггеры для создания экземпляра рабочего процесса на основе события или доступности некоторых данных. Соединители также используются как действия для чтения и записи данных или выполнения других действий, поддерживаемых соединителем.

###Соединители как триггеры###
Некоторые соединители предоставляют триггеры для приложений логики. Эти триггеры бывают двух типов.

1. Триггеры опроса. Эти триггеры опрашивают интересующую вас службу с указанной частотой для поиска новых данных. Когда новые данные обнаруживаются, запускается новый экземпляр приложения логики с этими данными в качестве входных данных. Триггер может выполнять дополнительные задачи, такие как очистка данных, которые были прочитаны и отправлены в приложение логики, чтобы предотвратить многократное использование одних и тех же данных. Примеры таких соединителей — File, SQL и хранилище Azure.
2. Триггеры Push. Эти триггеры прослушивают данные в конечной точке или происходящее событие и инициируют новый экземпляр приложения логики. Примеры таких соединителей — прослушиватель HTTP и Twitter.

###Соединители как действия###
Соединители также могут использоваться как действия в приложении логики. С помощью действий можно выполнять поиск данных, которые должны использоваться при выполнении приложения логики; например, может потребоваться найти в базе данных SQL дополнительные сведения о клиенте при обработке заказа. Или же вам может потребоваться запись, обновление или удаление данных в месте назначения, для чего могут использоваться действия, предоставляемые соединителями. Действия сопоставляются с операциями в приложениях API (в соответствии с их метаданными Swagger).


<!-- Links -->

[Соединитель Box]: app-service-logic-connector-box.md
[Соединитель Facebook]: app-service-logic-connector-facebook.md
[Соединитель Salesforce]: app-service-logic-connector-salesforce.md
[Соединитель Twitter]: app-service-logic-connector-twitter.md
[Соединитель SAP]: app-service-logic-connector-sap.md
[Соединитель FTP]: app-service-logic-connector-ftp.md
[Соединитель HTTP]: app-service-logic-connector-http.md
[Соединитель больших двоичных объектов службы хранилища Azure]: app-service-logic-connector-azurestorageblob.md
[Соединитель Office 365]: app-service-logic-connector-office365.md
[Соединитель Sharepoint]: app-service-logic-connector-sharepoint.md
[Соединитель SugarCRM]: app-service-logic-connector-sugarcrm.md
[Соединитель QuickBooks]: app-service-logic-connector-quickbooks.md
[Соединитель Yammer]: app-service-logic-connector-yammer.md
[Соединитель Twilio]: app-service-logic-connector-twilio.md
[Соединитель SMTP]: app-service-logic-connector-smtp.md
[Соединитель SFTP]: app-service-logic-connector-sftp.md
[Соединитель POP3]: app-service-logic-connector-pop3.md
[Соединитель Dropbox]: app-service-logic-connector-dropbox.md
[Соединитель Chatter]: app-service-logic-connector-chatter.md
[Соединитель HDInsight]: app-service-logic-connector-hdinsight.md
[Соединитель служебной шины Azure]: app-service-logic-connector-azureservicebus.md
[Соединитель Oracle]: app-service-logic-connector-oracle.md
[Соединитель SQL]: app-service-logic-connector-sql.md
[Соединитель OneDrive]: app-service-logic-connector-onedrive.md
[Соединитель File]: app-service-logic-connector-file.md
[Соединитель Slack]: app-service-logic-connector-slack.md


<!--HONumber=54--> 