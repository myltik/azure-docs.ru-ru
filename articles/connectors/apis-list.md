<properties
	pageTitle="Список соединителей, управляемых корпорацией Майкрософт, для использования в приложениях логики Microsoft Azure | Служба приложений Microsoft Azure"
	description="Полный список соединителей, управляемых корпорацией Майкрософт, которые можно использовать для создания приложений логики в службе приложений Azure"
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
	ms.date="05/31/2016"
	ms.author="deonhe"/>

# Список соединителей

>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview. Чтобы узнать версию схемы 2014-12-01-preview, щелкните [Список соединителей](../app-service-logic/app-service-logic-connectors-list.md).

Сведения о ценах и список состава каждого уровня обслуживания см. в разделе [цен на службу приложений Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Если вы хотите опробовать мобильные приложения, не создавая учетную запись Azure, перейдите на [эту страницу](https://tryappservice.azure.com/?appservice=logic). Там вы сможете быстро создать в службе приложений простое приложение логики для кратковременного использования. Никаких кредитных карт и обязательств.

## Стандартные соединители

Выберите значок и узнайте, как быстро задействовать соединители для создания приложений, которые вызывают эти службы. Эти соединители можно использовать для создания приложений логики, приложений PowerApps и потоков.

|Соединители||||
|-----------|-----------|-----------|-----------|
|[![Значок API][blobicon]<br/>**Большой двоичный объект Azure**][azureblobdoc]|[![Значок API][boxicon]<br/>**Box**][boxDoc]|[![Значок API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![Значок API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![Значок API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Значок API][ftpicon]<br/>**FTP**][ftpdoc]|[![Значок API][githubicon]<br/>**GitHub**][githubdoc]|[![Значок API][googledriveicon]<br/>**Диск Google**][googledrivedoc]|
|[![Значок API][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![Значок API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Значок API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Значок API][office365icon]<br/>**Office 365**<br/>**Пользователи**][office365usersdoc]|
|[![Значок API][office365icon]<br/>**Office 365**<br/>**Видео**][office365videodoc]|[![Значок API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Значок API][onedriveicon]<br/>**OneDrive<br/>для бизнеса**][onedriveforbusinessdoc]|[![Значок API][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![Значок API][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![Значок API][rssicon]<br/>**RSS**][rssdoc]|[![Значок API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Значок API][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![Значок API][servicebusicon]<br/>**Служебная шина**][servicebusdoc]|[![Значок API][sftpicon]<br/>**SFTP**][sftpdoc]|[![Значок API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Значок API][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![Значок API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Значок API][sqlicon]<br/>**SQL Azure**][sqldoc]|[![Значок API][trelloicon]<br/>**Trello**][trellodoc]|[![Значок API][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![Значок API][twittericon]<br/>**Twitter**][twitterdoc]|[![Значок API][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![Значок API][yammericon]<br/>**Yammer**][yammerdoc] | |

## Соединители пакета интеграции Enterprise (EIP)
Используйте соединители EIP, чтобы создавать приложения логики для реализации сценария B2B, который включает EAI и EDI.
 
|Соединители EIP ||||
|-----------|-----------|-----------|-----------|
|[![Значок API][as2icon]<br/>**Кодирование или декодирование</br>AS2**][as2doc]|[![Значок API][x12icon]<br/>**Кодирование или декодирование</br>X12**][x12Doc]|[![Значок API][xmlvalidateicon]<br/>**Проверка <br/>XML-файла**][xmlvalidatedoc]|[![Значок API][xmltransformicon]<br/>**Преобразование <br/>XML-файла**][xmltransformdoc]|
|[![Значок API][flatfileicon]<br/>**Кодирование</br>неструктурированного файла**][flatfiledoc]|[![Значок API][flatfiledecodeicon]<br/>**Декодирование</br>неструктурированного файла**][flatfiledecodedoc]|||


### Соединители могут быть триггерами.
Некоторые соединители служат триггерами, которые могут уведомлять приложение о возникновении определенных событий. Например, соединитель FTP имеет триггер OnUpdatedFile. Вы можете создать приложение логики, приложение PowerApp или поток, которые прослушивают этот триггер и предпринимают определенные действия при его активации.

Существует два типа триггеров:

* Триггеры опроса. Эти триггеры опрашивают вашу службу с указанной частотой для поиска новых данных. Когда новые данные обнаруживаются, запускается новый экземпляр приложения с этими данными в качестве входных данных. Чтобы предотвратить многократное использование одних и тех же данных, триггер может выполнять очистку данных, которые были прочитаны и отправлены в ваше приложение.
* Триггеры отправки. Эти триггеры прослушивают данные в конечной точке или происходящее событие. Затем они запускают новый экземпляр вашего приложения. Один из примеров — соединитель Twitter.


### Соединители могут быть действиями
Соединители могут также использоваться как действия в приложениях. Действия удобно использовать для поиска данных, которые можно использовать при выполнении вашего приложения. Например, при обработке заказа может потребоваться найти в базе данных SQL дополнительные сведения о клиенте либо записать, обновить или удалить данные в таблице назначения. Это можно сделать с помощью действий, предоставляемых соединителями. Действия сопоставляются с операциями, определенными в метаданных Swagger.


[Новая версия схемы 2015-08-01-preview](../app-service-logic/app-service-logic-schema-2015-08-01.md)  
[Создание нового приложения логики, подключающего службы SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md)  
[Что такое Microsoft PowerApps Enterprise?](../power-apps/powerapps-get-started-azure-portal.md)  
[Перенос приложений логики в версию схемы 2015-08-01-preview](connectors-schema-migration.md)

<!--Connectors Documentation-->
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
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Подключается к корпоративной учетной записи Microsoft OneDrive для отправки, удаления и составления списка файлов, а также выполнения других задач."
[outlookdoc]: ./connectors-create-api-outlook.md "Подключается к почтовому ящику Outlook для доступа к электронной почте и выполнения других задач."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Подключается к Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "Соединитель RSS позволяет пользователям публиковать и извлекать элементы веб-канала. Он также позволяет пользователям активировать операции при публикации нового элемента в веб-канале."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Подключается к учетной записи Salesforce для управления учетными записями, интересами, возможностями и выполнения других задач."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Подключается к Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Может отправлять сообщения из очередей и разделов служебной шины, а также получать сообщения из очередей и подписок служебной шины."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Подключается к SharePoint Online, чтобы управлять документами и элементами списка."
[slackdoc]: ./connectors-create-api-slack.md "Подключение к Slack резерв и публикация сообщений в каналы Slack."
[sftpdoc]: ./connectors-create-api-sftp.md "Подключается к SFTP и может отправлять, получать и удалять файлы, а также выполнять другие задачи."
[githubdoc]: ./connectors-create-api-github.md "Подключается к GitHub для отслеживания проблем."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Оптимизация отправляемой электронной почты."
[smtpdoc]: ./connectors-create-api-smtp.md "Подключается к SMTP-серверу и отправляет электронные сообщения с вложениями."
[sqldoc]: ./connectors-create-api-sqlazure.md "Подключается к базе данных SQL Azure. Вы можете создавать, обновлять, получать и удалять записи в таблице базы данных SQL."
[trellodoc]: ./connectors-create-api-trello.md "Trello — это бесплатное универсальное и визуальное средство организации данных."
[twiliodoc]: ./connectors-create-api-twilio.md "Подключается к Twilio для отправки и получения сообщений, получения доступных номеров, управления входящими телефонными номерами и выполнения других задач."
[twitterdoc]: ./connectors-create-api-twitter.md "Подключается к Twitter для получения временных шкал, публикации твитов и выполнения других задач."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Синхронизация повседневной активности."
[yammerdoc]: ./connectors-create-api-yammer.md "Подключается к Yammer для публикации и получения новых сообщений."
[as2doc]: ../app-service-logic/app-service-logic-enterprise-integration-as2.md "Узнайте об AS2 в рамках корпоративной интеграции."
[x12doc]: ../app-service-logic/app-service-logic-enterprise-integration-x12.md "Узнайте о соглашении X12 в рамках корпоративной интеграции."
[flatfiledoc]: ../app-service-logic/app-service-logic-enterprise-integration-flatfile.md "Узнайте о неструктурированном файле в рамках корпоративной интеграции."
[flatfiledecodedoc]: ../app-service-logic/app-service-logic-enterprise-integration-flatfile.md "Узнайте о неструктурированном файле в рамках корпоративной интеграции."
[xmlvalidatedoc]: ../app-service-logic/app-service-logic-enterprise-integration-xml.md "Узнайте о проверке XML в рамках корпоративной интеграции."
[xmltransformdoc]: ../app-service-logic/app-service-logic-enterprise-integration-transform.md "Узнайте о преобразованиях в рамках корпоративной интеграции."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png
[as2icon]: ./media/apis-list/as2new.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png

<!----HONumber=AcomDC_0706_2016-->
