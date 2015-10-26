<properties 
	pageTitle="Использование службы электронной почты SendGrid (Node.js) | Microsoft Azure" 
	description="Узнайте, как отправить электронное сообщение с помощью службы электронной почты SendGrid в Azure. Примеры написаны с использованием API Node.js." 
	services="" 
	documentationCenter="nodejs" 
	authors="erikre" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="08/31/2015" 
	ms.author="erikre"/>
# Как отправлять электронную почту с помощью SendGrid из Node.js

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры написаны с использованием интерфейса API Node.js. Описываемые здесь сценарии включают в себя **создание сообщения электронной почты**, **отправку сообщения электронной почты**, **добавление вложений**, **использование фильтров** и **обновление свойств**. Дополнительные сведения о SendGrid и отправке эл. почты см. в разделе [Дальнейшие действия](#next-steps).

## Что такое служба электронной почты SendGrid?

SendGrid — это [облачная служба электронной почты], которая обеспечивает надежную [доставку электронной почты], масштабируемость и аналитику в реальном времени, а также предоставляет гибкие интерфейсы API для пользовательской интеграции. Ниже перечислены наиболее распространенные сценарии использования SendGrid.

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
-   Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте [https://sendgrid.com](https://sendgrid.com).

## Создание учетной записи SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## Ссылка на модуль Node.js SendGrid

Модуль SendGrid для Node.js можно установить через диспетчер пакетов узлов (npm) с помощью следующей команды:

    npm install sendgrid

После установки вы можете запросить наличие этого модуля в приложении, используя следующий код:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Модуль SendGrid экспортирует функции **SendGrid** и **Email**. **SendGrid** отвечает за отправку почты через SMTP или веб-API, а **Email** инкапсулирует сообщение электронной почты.

## Практическое руководство. Создание сообщения эл. почты

Для создания электронного сообщения с помощью модуля SendGrid сначала создается электронное сообщение с помощью функции Email, а затем оно отправляется с помощью функции SendGrid. В следующем примере показано создание нового сообщения с помощью функции Email:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Вы также можете создать HTML-сообщение для клиентов, которые поддерживают этот формат, задав свойство html. Например:

    html: This is a sample <b>HTML<b> email message.

Задав свойства text и html, можно обеспечить корректный переход к текстовому содержимому для клиентов, которые не поддерживают формат HTML.

Дополнительную информацию обо всех свойствах, поддерживаемых функцией Email, см. в разделе [sendgrid-nodejs][].

## Практическое руководство. Отправка сообщения эл. почты

После создания электронного сообщения с помощью функции Email его можно отправить с помощью веб-интерфейса API, предоставленного SendGrid.

### Веб-интерфейс API

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE]Хотя в приведенных выше примерах показана передача для объекта электронной почты и функции обратного вызова, вы можете также напрямую вызвать функции отправки, задав свойства электронной почты. Например:
>
>`````
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## Практическое руководство. Добавление вложения

В сообщение можно добавить вложения, указав имена файлов и пути к ним в свойстве **files**. В следующем примере показана отправка вложения.

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [AZURE.NOTE]При использовании свойства **files** файл должен быть доступен через [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Если вы хотите вложить файл, находящийся в службе хранилища Azure, например контейнер больших двоичных объектов, необходимо сначала скопировать этот файл в локальное хранилище или на диск Azure, прежде чем его можно будет отправить как вложение с помощью свойства **files**.

## Практическое руководство. Использование фильтров для включения нижних колонтитулов и отслеживания

SendGrid поддерживает дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это параметры, которые можно добавить в сообщение электронной почты для включения определенных функций, например активации отслеживания щелчков, аналитики Google, отслеживания подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

Фильтры могут применяться к сообщению с использованием свойства **filters**. Каждый фильтр определяется хэшем, содержащим параметры, которые связаны с данным конкретным фильтром. В следующих примерах показаны фильтры нижних колонтитулов и отслеживания щелчков:

### Нижний колонтитул

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### Отслеживание щелчков

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });
    
    sendgrid.send(email);

## Практическое руководство. Обновление свойств электронной почты

Некоторые свойства электронной почты можно перезаписать с помощью оператора **set*Property*** или добавить с помощью оператора **add*Property***. Например, вы можете добавить дополнительных получателей с помощью

    email.addTo('jeff@contoso.com');

или задать фильтр с помощью

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Дополнительные сведения см. в разделе [sendgrid-nodejs][].

## Практическое руководство. Использование дополнительных служб SendGrid

SendGrid поддерживает различные веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid, доступные в приложении Azure. Дополнительные сведения см. в [документации по интерфейсу API SendGrid][].

## Дальнейшие действия

Вы получили основные сведения о службе доставки электронной почты SendGrid. Дополнительные сведения вы найдете по следующим ссылкам.

-   Репозиторий модуля Node.js SendGrid: [sendgrid-nodejs][]
-   Документация по интерфейсу API SendGrid: <https://sendgrid.com/docs>
-   Специальное предложение SendGrid для клиентов Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)
  [специальное предложение]: https://sendgrid.com/windowsazure.html
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [параметры фильтра]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [документация по API SendGrid]: https://sendgrid.com/docs
  [облачная служба электронной почты]: https://sendgrid.com/email-solutions
  [доставка электронной почты]: https://sendgrid.com/transactional-email

<!---HONumber=Oct15_HO3-->