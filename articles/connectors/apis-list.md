<properties
	pageTitle="Список API, управляемых корпорацией Майкрософт | Служба приложений Microsoft Azure"
	description="Полный список API, управляемых Майкрософт, которые можно использовать для создания приложений логики в службе приложений Azure"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/28/2016"
	ms.author="deonhe"/>

# Список управляемых API

>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview. Чтобы узнать версию схемы 2014-12-01-preview, щелкните [Список соединителей](../app-service-logic/app-service-logic-connectors-list.md).

Сведения о ценах и список состава каждого уровня обслуживания см. в разделе [цен на службу приложений Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Если вы хотите опробовать мобильные приложения, не создавая учетную запись Azure, перейдите на [эту страницу](https://tryappservice.azure.com/?appservice=logic). Там вы сможете быстро создать в службе приложений простое кратковременное приложение логики. Никаких кредитных карт и обязательств.

Выберите значок API и узнайте, как внедрить его в приложение, вызывающее эти службы. Эти API можно использовать для создания приложений логики, приложений PowerApps или приложений обоих этих типов.

|Интерфейсы API||||
|-----------|-----------|-----------|-----------|
|[![Значок API][blobicon]<br/>**Большой двоичный объект Azure**][azureblobdoc]|[![Значок API][bingsearchicon]<br/>**Поиск Bing**][bingsearchdoc]|[![Значок API][boxicon]<br/>**Box**][boxDoc]|[![Значок API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![Значок API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![Значок API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Значок API][ftpicon]<br/>**FTP**][ftpdoc]|[![Значок API][googledriveicon]<br/>**Диск Google**][googledrivedoc]|
|[![Значок API][microsofttranslatoricon]<br/>**Переводчик**][microsofttranslatordoc]|[![Значок API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Значок API][office365icon]<br/>**Office 365**<br/>**Пользователи**][office365usersdoc]|[![Значок API][office365icon]<br/>**Office 365**<br/>**Видео**][office365videodoc]|
|[![Значок API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Значок API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Значок API][servicebusicon]<br/>**Служебная шина**][servicebusdoc]|[![Значок API][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![Значок API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Значок API][slackicon]<br/>**Slack**<br/>][slackdoc]|[![Значок API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Значок API][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![Значок API][twilioicon]<br/>**Twilio**][twiliodoc]|[![Значок API][twittericon]<br/>**Twitter**][twitterdoc]|[![Значок API][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Если вы создавали приложения логики с помощью схемы предварительной версии за 01.12.2014, вы заметите, что в списке выше нет интерфейсов API для корпоративной интеграции, например BizTalk. Мы знаем, что эти интерфейсы тоже нужны, поэтому мы работаем над тем, чтобы предоставить их вам как можно быстрее. Хотя мы не можем озвучить точную дату доступности этих интерфейсов, помните, что эта задача является одним из наших основных приоритетов. А пока вы можете получить доступ к своим [интерфейсам API v1 и API BizTalk из приложений логики](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Благодарим вас за понимание. Следите за новостями.


### API могут быть триггерами.
Некоторые API служат триггерами, уведомляющими приложение о возникновении определенных событий. Например, API FTP имеет триггер OnUpdatedFile. Вы можете создать приложение логики и приложение PowerApps, которое прослушивают этот триггер и предпринимают определенные действия, если он активируется.

Существует два типа триггеров:

* Триггеры опроса. Эти триггеры опрашивают вашу службу с указанной частотой для поиска новых данных. Когда новые данные обнаруживаются, запускается новый экземпляр приложения с этими данными в качестве входных данных. Чтобы предотвратить многократное использование одних и тех же данных, триггер может выполнять очистку данных, которые были прочитаны и отправлены в ваше приложение.
* Триггеры отправки. Эти триггеры прослушивают данные в конечной точке или происходящее событие. Затем они запускают новый экземпляр вашего приложения. Один из примеров таких API — это API Twitter.


### API могут быть действиями.
API могут также использоваться как действия в вашем приложении. Действия удобно использовать для поиска данных, которые можно использовать при выполнении вашего приложения. Например, при обработке заказа может потребоваться найти в базе данных SQL дополнительные сведения о клиенте либо записать, обновить или удалить данные в таблице назначения. Это можно сделать с помощью действий, предоставляемых API. Действия сопоставляются с операциями, определенными в метаданных Swagger.


[Новая версия схемы 2015-08-01-preview](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Создание нового приложения логики, подключающего службы SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md) [Что такое Microsoft PowerApps Enterprise?](../power-apps/powerapps-get-started-azure-portal.md) [Migrate existing Logic apps to the latest schema version](connectors-schema-migration.md) (Обновление версии схемы в приложениях логики)

<!--API Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Подключается к BLOB-объекту Azure для управления файлами в контейнере BLOB-объектов."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Поиск веб-страниц, изображений, новостей и видео с помощью службы Bing."
[boxDoc]: ./connectors-create-api-box.md "Подключается к Box для отправки, получения, удаления, составления списков и выполнения других задач с файлами."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Подключается к Dynamics CRM Online и расширяет возможности работы с данными CRM Online."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Подключается к Dropbox для получения, удаления, составления списков и выполнения других задач с файлами."
[exceldoc]: ./connectors-create-api-excel.md "Подключается к Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Подключается к Facebook и позволяет оставлять сообщения на стене, получать канал страниц и выполнять другие действия."
[ftpdoc]: ./connectors-create-api-ftp.md "Подключается к FTP и FTPS-серверу для выполнения разных FTP-задач, включая отправку, получение и удаление файлов и многое другое."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Подключается к GoogleDrive и взаимодействует с данными."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Соединитель Office 365 может отправлять и получать сообщения электронной почты, управлять календарем, а также управлять контактами с помощью учетной записи Office 365."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Подключается к вашей личной учетной записи Microsoft OneDrive для передачи, удаления, составления списка файлов и выполнения других задач."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Подключается к учетной записи Salesforce для управления учетными записями, интересами, возможностями и выполнения других задач."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Может отправлять сообщения из очередей и разделов служебной шины, а также получать сообщения из очередей и подписок служебной шины."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Подключается к SharePoint Online, чтобы управлять документами и элементами списка."
[slackdoc]: ./connectors-create-api-slack.md "Подключение к Slack резерв и публикация сообщений в каналы Slack."
[sftpdoc]: ./connectors-create-api-sftp.md "Подключается к SFTP и может отправлять, получать и удалять файлы, а также выполнять другие задачи."
[smtpdoc]: ./connectors-create-api-smtp.md "Подключается к SMTP-серверу и отправляет электронные сообщения с вложениями."
[sqldoc]: ./connectors-create-api-sqlazure.md "Подключается к базе данных SQL Azure. Вы можете создавать, обновлять, получать и удалять записи в таблице базы данных SQL."
[twiliodoc]: ./connectors-create-api-twilio.md "Подключается к Twilio для отправки и получения сообщений, получения доступных номеров, управления входящими телефонными номерами и выполнения других задач."
[twitterdoc]: ./connectors-create-api-twitter.md "Подключается к Twitter для получения временных шкал, публикации твитов и выполнения других задач."
[yammerdoc]: ./connectors-create-api-yammer.md "Подключается к Yammer для публикации и получения новых сообщений."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0330_2016-->