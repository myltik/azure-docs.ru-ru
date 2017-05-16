---
title: "Соединители для Azure Logic Apps | Документация Майкрософт"
description: "Выберите соединители из списка всех доступных соединителей, управляемых корпорацией Майкрософт, чтобы создать приложения логики и выполнить их сборку"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 4f821602c3a48740cac3c220f47b7317e7896208
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="connectors-list"></a>Список соединителей
> [!TIP]
> Здесь содержится [полный список](#az) всех доступных соединителей, которые можно использовать в приложениях логики.

Соединители являются неотъемлемой частью при создании приложения логики. С помощью этих соединителей вы можете значительно расширить свои локальные и облачные приложения, чтобы выполнять различные действия с данными, которые вы создадите, а также с существующими данными. Доступны следующие категории соединителей: 

* **Стандартные соединители.** Автоматически доступны при использовании приложений логики. К примерам относятся соединитель служебной шины, DropBox, GoogleDrive, Power BI, Oracle Database, OneDrive и многие другие.

* **Соединители для учетной записи интеграции.** Доступны при приобретении учетной записи интеграции. С помощью этих соединителей вы можете преобразовать и проверять XML, обрабатывать сообщения типа "бизнес — бизнес" с помощью AS2, X12 или EDIFACT, а также кодировать и декодировать неструктурированные файлы. Если вы работаете с BizTalk Server, тогда эти соединители подойдут для расширения рабочих процессов BizTalk в Azure.  

    У BizTalk Server также есть [адаптер Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx), который поддерживает получение данных из приложения логики, а также их передачу в это приложение.

* **Корпоративные соединители.** Сюда относятся MQ и SAP. Для них предусмотрена дополнительная стоимость. 

[Страница цен на приложения логики](https://azure.microsoft.com/pricing/details/logic-apps/) и статья [Модель ценообразования приложений логики](../logic-apps/logic-apps-pricing.md) содержат дополнительные сведения о стоимости. 

## <a name="popular-connectors"></a>Популярные соединители
Тысячи приложений и миллионы выполнений успешно обрабатывают данные и сведения с помощью этих соединителей. В таблице ниже приведены самые распространенные соединители, включая те, которым отдают предпочтение пользователи.

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Хранилище BLOB-объектов<br/>Azure** ][AzureBlobStoragedoc] | Если вы хотите автоматизировать какие-либо задачи с помощью учетной записи хранения, тогда вам стоит рассмотреть этот вариант. Поддерживает выполнение операций CRUD (создание, чтение, обновление и удаление). | [![API Icon][Azure-Functionsicon]<br/>**Функции Azure**][azure-functionsdoc] | Создавайте функции, с помощью которых можно выполнять настраиваемые фрагменты кода C# или Node.js, а затем используйте эти функции в приложении логики.  |
| [![API Icon][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Это наиболее востребованный соединитель. Он содержит триггеры и действия, позволяющие автоматизировать рабочие процессы на основе интересов и даже больше. | [![API Icon][Event-Hubs-icon]<br/>**Концентраторы событий**][event-hubs-doc] | Используйте события и публикуйте их в концентраторе событий. Например, вы можете получить выходные данные из приложения логики с помощью соединителя концентраторов событий, а затем отправить их любому поставщику аналитики в реальном времени. |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | Если вы можете получить доступ к FTP-серверу из Интернета, вы можете автоматизировать рабочие процессы для работы с файлами и папками. <br/><br/>С соединителем SFTP также доступен протокол SFTP. | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | Используйте приложения логики для взаимодействия с любой конечной точкой через протокол HTTP. |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Доступны множество триггеров и действий для использования в рабочих процессах почты и событий Office 365. <br/><br/>Этот соединитель предоставляет действие для *письма с запросом на утверждение*, чтобы утвердить заявления на отпуск, отчеты по расходам и т. д. <br/><br/>С соединителем Office 365 Users также предоставляется доступ к пользователям Office 365.| [![API Icon][HTTP-Requesticon]<br/>**Запрос и ответ**][HTTP-Requestdoc] | Этот соединитель предоставляет URL-адрес для протокола HTTPS. Приложение логики запускается при получении запроса к этому URL-адресу. |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Получайте доступ к различным объектам, например к интересам, выполнив вход с помощью учетной записи Salesforce. |  [![API Icon][Service-Busicon]<br/>**Служебная шина**][Service-Busdoc] | Самый популярный соединитель для приложений логики. Он содержит триггеры и действия для выполнения асинхронной передачи сообщений, публикации и подписки с помощью очередей, подписок и разделов. |
|  [![API Icon][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Если вы знакомы с SharePoint и заинтересованы в преимуществах автоматизации, мы советуем выбрать именно этот соединитель. Вы можете использовать его с локальным SharePoint и SharePoint Online. | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Один из наиболее распространенных соединителей, который способен подключаться к локальному серверу SQL Server и базе данных SQL Azure. | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | Выполняйте быстрый вход с помощью учетной записи Twitter и запускайте рабочий процесс после публикации твита. Затем сохраняйте эти твиты в базу данных SQL или список SharePoint. | | | 

## <a name="integration-account-connectors"></a>Соединители для учетной записи интеграции 

Пакет интеграции Enterprise (EIP) содержит соединители, широко известные в сообществе BizTalk Server. При покупке [учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) вы также получаете следующие соединители: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][as2icon]<br/>**Декодирование</br> AS2**][as2decode] | [![API Icon][as2icon]<br/>**Кодирование</br> AS2**][as2encode] | [![API Icon][x12icon]<br/>**Декодирование</br> EDIFACT**][EDIFACTdecode] | [![API Icon][x12icon]<br/>**Кодирование</br> EDIFACT**][EDIFACTencode] |
[![API Icon][flatfileicon]<br/>**Кодирование</br> неструктурированных файлов**][flatfiledoc] | [![API Icon][flatfiledecodeicon]<br/>**Декодирование</br> неструктурированных файлов**][flatfiledecodedoc] | [![API Icon][integrationaccounticon]<br/>**Учетная запись<br/>интеграции**][integrationaccountdoc] | [![API Icon][xmltransformicon]<br/>**Преобразование<br/>XML**][xmltransformdoc] |
| [![API Icon][x12icon]<br/>**Декодирование</br> X12**][x12decode] | [![API Icon][x12icon]<br/>**Кодирование</br> X12**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**Проверка <br/>XML**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>Корпоративные соединители

Подключайтесь к корпоративным приложениям в приложениях логики.

|  |  |
| --- | --- |
|![Значок API][MQicon]<br/>**MQ**|[![API Icon][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>Полный список

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>Планирование отправок-прибытий<br/><br/><a name="a"></a>Список<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory<br/>Cлужба управления Azure API <br/>Службы приложений Azure<br/>Служба автоматизации Azure<br/>[Хранилище BLOB-объектов Azure][azureblobstoragedoc]<br/>Озеро данных Azure<br/>Azure DocumentDB.<br/>[Функции Azure][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Очереди Azure<br/>Диспетчер ресурсов Azure<br/>[База данных SQL Azure][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Электронная почта теста производительности<br/>Поиск Bing<br/>Bitbucket;<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Буфер<br/><br/><a name="c"></a>Campfire<br/>Capsule CRM<br/>Chatter<br/>API компьютерного зрения Cognitive Services<br/>API распознавания лиц Cognitive Services<br/>Cognitive Services LUIS<br/>Текстовая аналитика Cognitive Services<br/>Common Data Service<br/>[Настраиваемые API-интерфейсы и веб-приложения][api/web-appdoc]<br/><br/><a name="d"></a>Операции с данными<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>DropBox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Концентраторы событий][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[Файловая система][filesystemdoc]<br/>[Неструктурированный файл][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Календарь Google<br/>Контакты Google<br/>Google Диск<br/>Google Таблицы<br/>Google Задачи<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP и Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>Учетная запись интеграции<br/>Intercom | <a name="j"></a>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>Средний<br/>Microsoft Translator<br/>Магический квадрант<br/>MSN Погода<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Пользователи Office 365<br/>Office 365 Видео<br/>OneDrive<br/>OneDrive для бизнеса<br/>OneNote (Business)<br/>[База данных Oracle][oracle-db-doc]<br/>Клиент Outlook<br/>Задачи Outlook<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[Запрос и ответ][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[Сервер приложений SAP][sapconnector]<br/>[Сервер сообщений SAP][sapconnector]<br/>[Расписание][recurrencedoc]<br/>Область<br/>SendGrid<br/>[Служебная шина][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointdoc]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>Switch Case<br/>SurveyMonkey<br/><br/><a name="t"></a>Teradata<br/>Todoist<br/>Toodledo<br/>[Преобразование XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>Переменные<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[Проверка XML][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> Чтобы начать работу с Azure Logic Apps, не создавая учетную запись Azure, перейдите на [эту страницу](https://tryappservice.azure.com/?appservice=logic). Там вы сможете быстро создать простое кратковременное приложение логики. Никаких кредитных карт и обязательств.

## <a name="connectors-as-triggers-and-actions"></a>Соединители как триггеры и действия

**Триггер** запускает или выполняет экземпляр приложения логики. Некоторые соединители служат триггерами, которые могут уведомлять приложение о возникновении определенных событий. Например, у соединителя FTP есть триггер `OnUpdatedFile`, который запускает приложение логики при обновлении файла. 

Приложения логики содержат следующие типы триггеров:  

* *Триггеры опроса*. Эти триггеры опрашивают вашу службу с указанной частотой для поиска новых данных. 

    Когда обнаруживаются новые данные, с этими данными в качестве входных данных запускается новый экземпляр приложения логики. 

* *Извещающие триггеры.* Эти триггеры прослушивают данные в конечной точке или же планируемое событие, а затем инициируют новый экземпляр приложения логики.

* *Триггер повторения.* Этот триггер создает экземпляр приложения логики по определенному расписанию.

Соединители также предоставляют **действия**, которые можно использовать в рабочем процессе. Например, приложения логики могут выполнять поиск данных, а затем использовать эти данные позже. В частности, вы можете выполнить поиск пользовательских данных в базе данных SQL, а затем использовать эти данные для создания рабочего процесса. 

> [!TIP]
> Дополнительные сведения о триггерах и действиях см. в [обзоре соединителей](connectors-overview.md). 


## <a name="message-manipulation-actions"></a>Действия управления сообщениями

Приложения логики содержат встроенные действия, с помощью которых можно изменять полезные данные или же управлять ими. Встроенный соединитель **операций с данными** содержит следующие действия: 

| | |
|---|---|
| **Действие создания** | Позволяет создавать значения или объекты для последующего использования или же на протяжении создания рабочего процесса. Например, вы можете создать объект JSON с различными значениями, полученными на разных этапах, или же вычислить постоянную величину, которую можно использовать позже в приложении логики. |
| **Создание таблицы CSV**<br/>**Создание таблицы HTML** | Преобразуйте результирующий набор массива в таблицу CSV или HTML. Например, добавьте действие CRM List Records (Отображение списка записей) и фильтр для записей, которые были добавлены сегодня. Затем отправьте результаты как HTML-таблицу в сообщении электронной почты. |
| **Фильтрация массива** (запрос) | Отфильтруйте результирующий набор по интересующим вас записям. Например, выполните поиск всех твитов с термином `#Azure`, а затем отфильтруйте возвращенные твиты для отображения только результатов, соответствующих `Tweeted_by_followers > 50`. |
| **Join** | Объедините массив по определенному разделителю. Например, операция обнаружения ключевых фраз возвращает массив ключевых фраз. Вы можете объединить их с `,` или другим подобным разделителем. Таким образом, вместо `["Some", "Phrase"]` у вас будет `"Some, Phrase"`. |
| **Анализ JSON** | Проанализируйте значения и получите к ним доступ из объекта JSON в конструкторе. Например, если функция Azure возвращает полезные данные JSON, тогда вы можете проанализировать их, чтобы позже получить доступ к свойствам JSON на другом шаге. Это действие также проверяет соответствие JSON определенной схеме во время выполнения. | 
| **Выбор** | Выберите определенные свойства массива для дальнейшей обработки. Если вы выберете отображение списка записей из SQL и при этом вернется 15 столбцов, выберите всего несколько из них для дальнейшей обработки (выходные данные представляют собой массив, содержащий только те свойства, которые вы выбрали). |

## <a name="custom-connectors-and-azure-certification"></a>Пользовательские соединители и сертификация Azure 

Чтобы вызывать API-интерфейсы, которые запускают пользовательский код или недоступны в качестве соединителей, можно расширить платформу Logic Apps, [создавая приложения API на основе REST как настраиваемые соединители](../logic-apps/logic-apps-create-api-app.md). 

Если вы хотите сделать пользовательские приложения API открытыми и доступными для использования в Azure, отправьте заявку на участие в [программе Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

## <a name="get-help"></a>Получение справки

Чтобы задать вопросы, получить на них ответы и узнать, что делают другие пользователи, посетите [форум по Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Чтобы улучшить Azure Logic Apps и соединители, голосуйте за идеи или предлагайте собственные на [сайте обратной связи Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дальнейшие действия
* [Создание приложения логики](../logic-apps/logic-apps-create-a-logic-app.md)
* [Создание настраиваемых API для приложений логики](../logic-apps/logic-apps-create-api-app.md)
* [См. статью Мониторинг приложений логики.](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Интеграция приложений логики с приложениями API службы приложений."
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Управление файлами в контейнере больших двоичных объектов с помощью соединителя хранилища BLOB-объектов Azure"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Интеграция приложений логики с Функциями Azure"
[db2doc]: ./connectors-create-api-db2.md "Подключение к IBM DB2 в облаке или локальной среде. Обновление строки, таблицы и другие действия"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Подключение к Dynamics CRM Online для работы с данными CRM Online"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connect to Azure Event Hubs. Receive and send events between logic apps and Event Hubs" (Подключение к концентраторам событий Azure. Получение и отправка событий между приложением логики и концентраторами событий)
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Подключение к локальной файловой системе"
[ftpdoc]: ./connectors-create-api-ftp.md "Подключение к FTP и FTPS-серверу для выполнения разных FTP-задач, включая отправку, получение, удаление файлов и другие действия."
[httpdoc]: ./connectors-native-http.md "Вызовы HTTP с помощью соединителя HTTP"
[http-requestdoc]: ./connectors-native-reqres.md "Добавление действий для HTTP-запросов и ответов в приложения логики"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Вызовы HTTP с помощью соединителя HTTP + Swagger"
[informixdoc]: ./connectors-create-api-informix.md "Подключение к Informix в облаке или локальной среде. Чтение строк, отображение таблиц и другие действия"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Интеграция приложений логики с вложенным рабочим процессом."
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Подключение к учетной записи Office 365. Отправка и получение сообщений, управление календарем и контактами, а также другие действия"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connect to an Oracle database to add, insert, delete rows, and more" (Подключение к базе данных Oracle для добавления, вставки, удаления строк и других действий)
[recurrencedoc]:  ./connectors-native-recurrence.md "Активация повторяющихся действий для приложений логики"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Подключение к учетной записи Salesforce. Управление учетными записями, потенциальными клиентами, возможностями и другие действия"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Подключение к локальному серверу SAP"
[service-busdoc]: ./connectors-create-api-servicebus.md "Отправка сообщений из очередей и разделов служебной шины, а также получение сообщений из очередей и подписок служебной шины"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Подключение к SharePoint Online. Управление документами, элементами списка и другие действия"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Подключение к базе данных SQL Azure или серверу SQL. Создание, обновление, получение и удаление записей в таблице базы данных SQL."
[twitterdoc]: ./connectors-create-api-twitter.md "Подключение к Twitter. Получение обновлений, публикация твитов и выполнение других задач"
[webhookdoc]: ./connectors-native-webhook.md "Добавление действий и триггеров веб-перехватчика в приложения логики"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Дополнительные сведения об AS2 в рамках корпоративной интеграции."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Дополнительные сведения об X12 в рамках корпоративной интеграции."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Дополнительные сведения о неструктурированном файле в рамках корпоративной интеграции."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Дополнительные сведения о неструктурированном файле в рамках корпоративной интеграции."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Дополнительные сведения о проверке XML в рамках корпоративной интеграции."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Дополнительные сведения о преобразованиях в рамках корпоративной интеграции."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Дополнительные сведения о декодировании AS2 в рамках корпоративной интеграции."
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Дополнительные сведения о кодировке AS2 в рамках корпоративной интеграции."
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Дополнительные сведения о декодировании X12 в рамках корпоративной интеграции."
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Дополнительные сведения о кодировке X12 в рамках корпоративной интеграции."
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Дополнительные сведения о декодировании EDIFACT в рамках корпоративной интеграции."
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Дополнительные сведения о кодировке EDIFACT в рамках корпоративной интеграции."
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Поиск схем, карт, партнеров и другого в вашей учетной записи интеграции"


[boxDoc]: ./connectors-create-api-box.md "Подключение к Box. Отправка, получение, удаление файлов, отображение их списка и другие действия"
[delaydoc]: ./connectors-native-delay.md "Выполнение отложенных действий"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Подключение к Dropbox. Отправка, получение, удаление файлов, отображение их списка и другие действия"
[facebookdoc]: ./connectors-create-api-facebook.md "Подключение к Facebook. Публикация на стене, получение канала страниц и другие действия"
[githubdoc]: ./connectors-create-api-github.md "Подключение к GitHub и отслеживание проблем"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Подключение к Google Диску для работы с данными"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Подключение к Таблицам Google для редактирования таблиц"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Подключение к Задачам Google для управления задачами"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Подключается к Google Календарь и может управлять календарем."
[http-responsedoc]: ./connectors-native-reqres.md "Добавление действий для HTTP-запросов и ответов в приложения логики"
[instagramdoc]: ./connectors-create-api-instagram.md "Подключение к Instagram. Активация событий и реакция на них"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Подключения к учетной записи MailChimp. Управление сообщениями электронной почты и их автоматизация"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Подключение к Mandrill для обмена данными"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Подключение к Microsoft Translator. Перевод текста, обнаружение языков и другие действия" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Получение сведений о видео, списков и каналов видео, а также URL-адресов воспроизведения для видео Office 365"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Подключение к личному Microsoft OneDrive. Отправка, удаление, отображение файлов и другие действия"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Подключение к рабочему Microsoft OneDrive. Отправка, удаление, отображение файлов и другие действия"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Подключение к почтовому ящику Outlook. Управление электронной почтой, календарями, контактами и другие действия"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Подключение к Microsoft Project Online. Управление проектами, задачами, ресурсами и другие действия"
[querydoc]: ./connectors-native-query.md "Выбор и фильтрации массивов с помощью действия "Запрос""
[rssdoc]: ./connectors-create-api-rss.md "Публикация и получение элементов канала, активация операций при публикации нового элемента в RSS-канале."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Подключение к SendGrid. Отправка электронных сообщений и управление списками получателей"
[sftpdoc]: ./connectors-create-api-sftp.md "Подключение к учетной записи SFTP. Отправка, получение, удаление файлов и другие действия"
[slackdoc]: ./connectors-create-api-slack.md "Подключение к Slack и публикация сообщений в каналах Slack"
[smtpdoc]: ./connectors-create-api-smtp.md "Подключение к SMTP-серверу и отправка электронных сообщений с вложениями"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Подключение к SparkPost для обмена данными"
[trellodoc]: ./connectors-create-api-trello.md "Подключение к Trello. Управление проектами и организация всего и всех"
[twiliodoc]: ./connectors-create-api-twilio.md "Подключение к Twilio. Отправка и получение сообщений, получение доступных номеров, управление входящими телефонными номерами и выполнение других задач"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Подключение к Wunderlist. Управление задачами и списками дел и их синхронизация, а также другие действия"
[yammerdoc]: ./connectors-create-api-yammer.md "Подключение к Yammer. Публикация сообщений, получение новых сообщений и другие действия"
[youtubedoc]: ./connectors-create-api-youtube.md "Подключение к YouTube. Управление видео и каналами"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png

