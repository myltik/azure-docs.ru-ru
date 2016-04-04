<properties
   pageTitle="Использование соединителя SMTP в приложениях логики | Служба приложений Microsoft Azure"
   description="Как создать и настроить соединитель SMTP или приложение API и использовать его в приложении логики в службе приложений Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="rajram"/>


# Приступая к работе с соединителем SMTP: добавление в приложение логики
>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2014-12-01-preview. Чтобы узнать версию схемы 2015-08-01-preview, щелкните [API SMTP](../connectors/connectors-create-api-smtp.md).

Подключайтесь к серверу SMTP и отправляйте сообщения электронной почты, в том числе сообщения с вложениями. Действие "Отправить электронное письмо" соединителя SMTP позволяет отправить сообщение электронной почты по указанным адресам.

Приложения логики могут запускаться на основе разных источников данных и предлагать соединители для получения и обработки данных в рамках рабочего процесса. Соединитель SMTP можно добавить в рабочий процесс компании и обрабатывать данные в рамках этого процесса приложения логики.


## Триггеры и действия
*Триггеры* — это события, которые происходят. Например, когда обновляется заказ или добавляется новый клиент. *Действие* — это результат триггера. Например, при обновлении заказа или добавлении нового клиента этому клиенту может отправляться сообщение электронной почты.

Соединитель SMTP можно использовать как действие в приложении логики, он поддерживает данные в форматах JSON и XML. В настоящее время для этого соединителя не существует никаких триггеров.

Соединитель SMTP предоставляет следующие триггеры и действия.

триггеры; | Действия
--- | ---
None | Отправка электронной почты


## Создание соединителя SMTP
Соединитель можно создать в приложении логики или непосредственно в Azure Marketplace. Создание соединителя в Marketplace

1. На начальной панели Azure выберите **Marketplace**.
2. Выберите **Приложения API** и найдите "Соединитель SMTP".
3. **Создайте** соединитель.
4. Введите имя, план службы приложений и другие свойства.
5. Введите следующие параметры пакета:

	Имя | Обязательно | Описание
	--- | --- | ---
	Имя пользователя | Да | Введите имя пользователя, который может подключаться к SMTP-серверу.
	Пароль | Да | Введите пароль для этого имени пользователя.
	Адрес сервера | Да | Введите имя или IP-адрес SMTP-сервера.
	Порт сервера | Да | Введите номер порта SMTP-сервера.
	Включить SSL | Нет | Введите*true*для использования SMTP через защищенный канал SSL/TLS.

6. Нажмите кнопку **Создать**.

> [AZURE.IMPORTANT] На некоторых серверах SMTP могут возникать проблемы с работой этого соединителя (SendGrid и Gmail). Если вам нужно отправлять почту из SendGrid, в нашем [репозитории GitHub](https://github.com/logicappsio/SendGridAPI) есть специальный API, который напрямую взаимодействует с API SendGrid.

## Использование соединителя SMTP в приложении логики
После создания соединителя SMTP его можно использовать в качестве действия в приложении логики. Для этого:

1.	Создайте новое приложение логики: ![][2]
2.	Перейдите в раздел **Триггеры и действия**, чтобы открыть конструктор приложений логики и настроить рабочий процесс: ![][3]
3.	Соединитель SMTP отображается в разделе коллекции «Приложения API в этой группе ресурсов» с правой стороны. Выберите его: ![][4]
4.	Выберите соединитель SMTP, чтобы автоматически добавить его в конструктор рабочих процессов.

Теперь можно настроить соединитель SMTP для использования в рабочем процессе. Выберите действие **Отправить письмо** и настройте входные свойства следующим образом:

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

![][5] ![][6]

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в рабочий бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Если вы хотите начать работу с приложениями логики Azure до создания учетной записи Azure, перейдите на веб-сайт [пробного использования приложений логики](https://tryappservice.azure.com/?appservice=logic). На этом сайте вы сможете быстро создать кратковременное приложение логики начального уровня в службе приложений. Никаких кредитных карт и обязательств.

Справку по API REST Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Можно также просматривать статистику производительности и управлять безопасностью соединителя. См. статью [Управление встроенными приложениями API и соединителями, а также их мониторинг](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=AcomDC_0323_2016-->