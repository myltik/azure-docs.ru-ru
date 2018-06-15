---
title: Соединители для Azure Logic Apps | Документация Майкрософт
description: Выберите соединители из списка всех доступных соединителей корпорации Майкрософт, чтобы создать приложения логики и выполнить их сборку
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: estfan; ladocs
ms.openlocfilehash: caeb3bbf6e3106aff498b12906b730191e56a3d5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294832"
---
# <a name="connectors-list"></a>Список соединителей
Сведения о поиске триггеров и действий, определяемых описанием Swagger каждого соединителя и любыми ограничениями соединителя, см. в статье [Connectors](/connectors/) (Соединители).

Соединители являются неотъемлемой частью при создании приложений логики. С помощью этих соединителей вы можете расширить свои локальные и облачные приложения, чтобы выполнять различные действия с данными, которые вы создадите, а также с существующими данными. Соединители доступны как встроенные действия или управляемые соединители.

**Встроенные действия.** Подсистема Logic Apps предоставляет встроенные действия для взаимодействия с конечными точками и выполнения задач. Например, эти действия можно использовать для вызова конечных точек HTTP, службы "Функции Azure" и операций управления API Azure, а также для управления сообщениями с помощью операций с данными и переменных.

**Управляемые соединители.** Предоставляйте доступ к API-интерфейсам для различных служб, создавая подключения API, которые размещает служба Logic Apps, управляющая ими. Управляемые соединители разделяются на следующие категории:

* **Стандартные соединители.** Автоматически доступны при использовании приложений логики. Примеры включают соединители служебной шины, Power BI, OneDrive и пр.

* **Локальные соединители.** Подключайтесь к серверным приложениям локально с помощью [локального шлюза данных][gatewaydoc]. Локальные соединители предоставляют возможность подключения к серверным приложениям, таким как SharePoint Server, SQL Server, база данных Oracle, общие файловые ресурсы и другие.

* **Соединители для учетной записи интеграции.** Доступны при приобретении учетной записи интеграции. С помощью этих соединителей вы можете преобразовать и проверять XML, обрабатывать сообщения типа "бизнес — бизнес" с помощью AS2, X12 или EDIFACT, а также кодировать и декодировать неструктурированные файлы. Если вы работаете с BizTalk Server, тогда эти соединители подойдут для расширения рабочих процессов BizTalk в Azure.  

    У BizTalk Server также есть [адаптер Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx), который поддерживает получение данных из приложения логики, а также их передачу в это приложение.

* **Корпоративные соединители.** Сюда относятся MQ и SAP. Доступны за дополнительную плату. 

Дополнительные сведения о ценах см. в статье [Цены на Приложения логики](https://azure.microsoft.com/pricing/details/logic-apps/) и [Модель ценообразования приложений логики](../logic-apps/logic-apps-pricing.md) для Logic Apps. 

## <a name="popular-connectors"></a>Популярные соединители
Тысячи приложений и миллионы выполнений успешно обрабатывают данные и сведения с помощью этих соединителей. 

### <a name="built-in-actions"></a>Встроенные действия
Система Logic Apps предоставляет действия, с помощью которых можно управлять данными, подключаться по протоколу HTTP и управлять потоком определения приложения логики. Ниже перечислены некоторые из этих действий.

| |  |  |  |
| --- | --- | --- | --- |
| [![Значок API][HTTPicon]<br/>**HTTP**][httpdoc] | Используйте приложения логики для взаимодействия с любой конечной точкой через протокол HTTP.| [![Значок API][Azure-Functionsicon]<br/>**Функции Azure**][azure-functionsdoc] | Создавайте функции, с помощью которых можно выполнять настраиваемые фрагменты кода C# или Node.js, а затем используйте эти функции в приложении логики.  |
| [![Значок API][HTTP-Requesticon]<br/>**Запрос**][HTTP-Requestdoc] | Предоставляет вызываемый URL-адрес HTTPS, который обычно используется в качестве веб-перехватчика в других приложениях. Приложение логики запускается при получении запроса к этому URL-адресу. | [![Значок API][Recurrenceicon]<br/>**Расписание**][recurrencedoc] | Запускайте приложения логики с учетом простого или сложного расписания повторов. Например, создайте расписания с простым условием повторения (повтор каждый час) или сложным условием повторения (последняя пятница каждого месяца ежечасно между 9:00 и 17:00). |
| [![Значок API][CallLogicApp-icon]<br/>**Вызов<br/>приложения логики**][nested-logic-appdoc] | Вызовите вложенное приложение логики. Любое приложение логики с триггером запроса можно вызвать как вложенное приложение логики.| [![Значок API][API/Web-Appicon]<br/>**Приложение API**][api/web-appdoc] | Вызовите приложение API службы приложений. Приложения API со Swagger отображается так же, как другие действия первого класса.|

### <a name="standard-connectors"></a>Стандартные соединители
В таблице ниже приведены самые распространенные соединители, включая те, которым отдают предпочтение пользователи.

| |  |  |  |
| --- | --- | --- | --- |
| [![Значок API][AzureBlobStorageicon]<br/>**Хранилище<br/>BLOB-объектов Azure**][AzureBlobStoragedoc] | Если вы хотите автоматизировать какие-либо задачи с помощью учетной записи хранения, тогда вам стоит рассмотреть этот вариант. Поддерживает выполнение операций CRUD (создание, чтение, обновление и удаление). | [![Значок API][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Это один из самых популярных соединителей. Он содержит триггеры и действия, позволяющие автоматизировать рабочие процессы на основе интересов и даже больше. |
| [![Значок API][Event-Hubs-icon]<br/>**Концентраторы событий**][event-hubs-doc] | Используйте события и публикуйте их в концентраторе событий. Например, вы можете получить выходные данные из приложения логики с помощью соединителя концентраторов событий, а затем отправить эти сведения любому поставщику аналитики в реальном времени. | [![Значок API][FTPicon]<br/>**FTP**][FTPdoc] | Если вы можете получить доступ к FTP-серверу из Интернета, вы можете автоматизировать рабочие процессы для работы с файлами и папками. <br/><br/>С соединителем SFTP также доступен протокол SFTP. |
| [![Значок API][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Доступны множество триггеров и действий для использования в рабочих процессах почты и событий Office 365. <br/><br/>Этот соединитель предоставляет действие для *письма с запросом на утверждение*, чтобы утвердить заявления на отпуск, отчеты по расходам и т. д. <br/><br/>С соединителем Office 365 Users также предоставляется доступ к пользователям Office 365.| [![Значок API][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Получайте доступ к различным объектам, например к интересам, выполнив вход с помощью учетной записи Salesforce. | 
| [![Значок API][Service-Busicon]<br/>**Служебная шина**][Service-Busdoc] | Самый популярный соединитель для приложений логики. Он содержит триггеры и действия для выполнения асинхронной передачи сообщений, публикации и подписки с помощью очередей, подписок и разделов. |  [![Значок API][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Если вы знакомы с SharePoint и заинтересованы в преимуществах автоматизации, мы советуем выбрать именно этот соединитель. Вы можете использовать его с локальным SharePoint и SharePoint Online. |
| [![Значок API][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Один из наиболее распространенных соединителей, который способен подключаться к локальному серверу SQL Server и базе данных SQL Azure. | [![Значок API][Twittericon]<br/>**Twitter**][Twitterdoc] | Выполняйте быстрый вход с помощью учетной записи Twitter и запускайте рабочий процесс после публикации твита. Затем сохраняйте эти твиты в базу данных SQL или список SharePoint. | 

### <a name="on-premises-connectors"></a>Локальные соединители 

Локальные соединители предоставляют доступ к данным на локальных серверах.  Для создания подключения к локальному серверу требуется [локальный шлюз данных][gatewaydoc], предоставляющий безопасный коммуникационный канал без необходимости настройки сетевой инфраструктуры.  Ниже перечислены некоторые соединители.

|  |  |  |  |
| --- | --- | --- | --- |
| [![Значок API][db2icon]<br/>**DB2**][db2doc] | [![Значок API][oracle-DB-icon]<br/>**База данных Oracle**][oracle-db-doc] | [![Значок API][sharepointicon]<br/>**SharePoint</br> Server**][sharepointserver] | [![Значок API][filesystem-icon]<br/>**Файловая</br> система**][filesystemdoc] |
[![API Icon][sql-servericon]<br/>**SQL</br> Server**][sql-serverdoc] | ![Значок API][Biztalk-Servericon]<br/>**BizTalk</br> Server**| |

### <a name="integration-account-connectors"></a>Соединители для учетной записи интеграции 

Пакет интеграции Enterprise (EIP) содержит соединители, широко известные в сообществе BizTalk Server. При покупке [учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) вы также получаете следующие соединители: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![Значок API][as2icon]<br/>**Декодирование</br>AS2**][as2decode] | [![Значок API][as2icon]<br/>**Кодирование</br>AS2**][as2encode] | [![Значок API][x12icon]<br/>**Декодирование</br>EDIFACT**][EDIFACTdecode] | [![Значок API][x12icon]<br/>**Кодирование</br>EDIFACT**][EDIFACTencode] |
[![Значок API][flatfileicon]<br/>**Кодирование</br>неструктурированного файла**][flatfiledoc] | [![Значок API][flatfiledecodeicon]<br/>**Декодирование</br>неструктурированного файла**][flatfiledecodedoc] | [![Значок API][integrationaccounticon]<br/>**Учетная запись<br/>интеграции**][integrationaccountdoc] | [![Значок API][xmltransformicon]<br/>**Преобразование<br/>XML**][xmltransformdoc] |
| [![Значок API][x12icon]<br/>**Декодирование</br>Х12**][x12decode] | [![Значок API][x12icon]<br/>**Кодирование</br>Х12**][x12encode] | [![Значок API][xmlvalidateicon]<br/>**Проверка<br/>XML**][xmlvalidatedoc] | [![Значок API][liquidicon]<br/>**Преобразование <br/>JSON**][JSONliquidtransformdoc] |

### <a name="enterprise-connectors"></a>Корпоративные соединители

Подключайтесь к корпоративным приложениям в приложениях логики.

|  |  |
| --- | --- |
|[![Значок API][MQicon]<br/>**MQ**][mqdoc]|[![Значок API][SAPicon]<br/>**SAP**][sapconnector]|

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
| **Select** | Выберите определенные свойства массива для дальнейшей обработки. Если вы выведете на экран список записей в SQL и получите 15 столбцов, выберите несколько столбцов для дальнейшей обработки. Выходные данные представляют массив, содержащий выбранные вами свойства. |

## <a name="custom-connectors-and-azure-certification"></a>Пользовательские соединители и сертификация Azure 

Чтобы вызывать API-интерфейсы, которые запускают пользовательский код или недоступны в качестве соединителей, можно расширить платформу Logic Apps, [создавая приложения API на основе REST](../logic-apps/logic-apps-create-api-app.md). Вы также можете создать свои [настраиваемые соединители](../logic-apps/custom-connector-overview.md), к которым можно предоставить доступ для любого приложения логики в вашей подписке.

Если нужно сделать пользовательские приложения API общедоступными для использования в Azure, вы можете [отправить соединители для сертификации Майкрософт](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-help"></a>Получение справки

Чтобы задать вопросы, получить на них ответы и узнать, что делают другие пользователи, посетите [форум по Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Чтобы улучшить Azure Logic Apps и соединители, голосуйте за идеи или предлагайте собственные на [сайте обратной связи Logic Apps](http://aka.ms/logicapps-wish).

Мы не рассмотрели соединители или не указали какие-то другие сведения, которые вы считаете важными? Если это так, помогите нам, добавив эти сведения в уже существующие разделы или создав собственные. Наша документация включает открытый исходный код и размещается на сайте GitHub. Начало работы с нашим [репозиторием GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Дополнительная информация
* [Создание приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Создание настраиваемых API для приложений логики](../logic-apps/logic-apps-create-api-app.md)
* [См. статью Мониторинг приложений логики.](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "Подключение к локальным источникам данных из приложений логики с помощью локального шлюза данных"
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
[mqdoc]: ./connectors-create-api-mq.md "Подключение к MQ в локальной среде или Azure для отправки и получения сообщений"
[recurrencedoc]:  ./connectors-native-recurrence.md "Активация повторяющихся действий для приложений логики"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Подключение к учетной записи Salesforce. Управление учетными записями, потенциальными клиентами, возможностями и другие действия"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Подключение к локальному серверу SAP"
[service-busdoc]: ./connectors-create-api-servicebus.md "Отправка сообщений из очередей и разделов служебной шины, а также получение сообщений из очередей и подписок служебной шины"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Подключение к SharePoint Online. Управление документами, элементами списка и другие действия"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Подключение к локальному серверу SharePoint Управление документами, элементами списка и другие действия"
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
[JSONliquidtransformdoc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Дополнительные сведения о преобразованиях JSON с помощью Liquid"


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
[AppServices-icon]: ./media/apis-list/AppServices.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
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
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
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
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
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
[liquidicon]: ./media/apis-list/liquidtransform.png
