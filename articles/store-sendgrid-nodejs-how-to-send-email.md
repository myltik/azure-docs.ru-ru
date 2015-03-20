<properties 
	pageTitle="Как использовать службу электронной почты SendGrid (Node.js) - Azure" 
	description="Узнайте, как отправить электронное сообщение с помощью службы электронной почты SendGrid в Azure. Примеры написаны с использованием API Node.js." 
	services="" 
	documentationCenter="nodejs" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>





# Как отправлять электронную почту с помощью SendGrid из Node.js

В этом руководстве показано, как выполнять типовые задачи программирования с помощью
службы электронной почты SendGrid в Azure. Примеры написаны с использованием APINode.js. Здесь описаны такие сценарии, как **создание сообщение электронной почты**,
**отправка сообщения электронной почты**, **добавление вложений**, **использование фильтров** и
**Обновление свойств**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе [Дальнейшие действия][].

## Оглавление

* [Что такое служба электронной почты SendGrid?][]   
* [Создание учетной записи SendGrid][]
* [Ссылка на модуль Node.js SendGrid][]
* [Практическое руководство. Создание сообщения электронной почты][]   
* [Практическое руководство. Отправка сообщения электронной почты][]   
* [Практическое руководство. Добавление вложения][]   
* [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]   
* [Практическое руководство. Обновление свойств электронной почты][]   
* [Практическое руководство. Использование дополнительных служб SendGrid][]   
* [Дальнейшие действия][1]

## <a name="whatis"> </a>Что такое служба электронной почты SendGrid

SendGrid - [службы электронной почты на основе облака] , предоставляет надежные
[Доставка электронной почты], масштабируемости и аналитики в режиме реального времени и гибкий API, которые облегчают пользовательскую интеграцию. Наиболее распространенные сценарии использования SendGrid:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
-   Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте [https://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Создание учетной записи SendGrid

[AZURE.INCLUDE [sendgrid-sign вверх](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a>Ссылка на модуль Node.js SendGrid

Модуль SendGrid для Node.js можно установить через диспетчер пакетов узлов (npm) с помощью следующей команды:

    npm install sendgrid

После установки вы можете запросить наличие этого модуля в приложении, используя следующий код:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Модуль SendGrid экспортирует функции **SendGrid** и **Email**.
**SendGrid** отвечает за отправку почты через веб-интерфейс API, хотя **электронной почты** инкапсулирует сообщение электронной почты.

## <a name="createemail"> </a>Практическое руководство. Создание электронного сообщения

Создание сообщения электронной почты с помощью модуля SendGrid включает в себя сначала создается сообщение электронной почты с помощью функции Email и последующую отправку их с помощью функции SendGrid. Ниже приведен пример создания нового сообщения с помощью функции Email:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Вы также можете создать HTML-сообщение для клиентов, которые поддерживают этот формат, задав свойство html. Например:

    html: This is a sample <b>HTML<b> email message.

Задав свойства text и html, можно обеспечить корректный переход к текстовому содержимому для клиентов, которые не поддерживают формат HTML.

Дополнительные сведения обо всех свойствах, поддерживаемых функцией Email разделе [sendgrid nodejs][].

## <a name="sendemail"> </a>Практическое руководство. Отправка сообщения электронной почты

После создания сообщения электронной почты, используя функцию электронной почты, можно отправить его с помощью веб-API, предоставленных SendGrid. 

### Веб-интерфейс API

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE] Хотя в приведенных выше примерах показана передача объекта и обратным вызовом функции электронной почты, можно напрямую вызвать функции отправки непосредственно, задав свойства электронной почты. Например:  
>
>`````
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## <a name="addattachment"> </a>Практическое руководство. Добавление вложения

Можно добавлять вложения в сообщение, указав имя файла и путь к диску в **файлы** свойство. В следующем примере демонстрируется отправка вложения:

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

> [AZURE.NOTE] При использовании **файлы** свойства, файл должен быть доступен через [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Если вы хотите вложить файл, находящийся в службе хранилища Azure, например контейнер больших двоичных объектов, необходимо сначала скопировать этот файл в локальное хранилище или на диск Azure, прежде чем его можно будет отправить как вложение с помощью свойства **files**.

## <a name="usefilters"> </a>Практическое руководство. Использование фильтров для включения нижних колонтитулов и отслеживания

SendGrid поддерживает дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это параметры, которые можно добавить в сообщение электронной почты для включения определенных функций, например активации отслеживания щелчков, аналитики Google, отслеживания подписок и т. д. Полный список фильтров см. в разделе [настройки фильтра][].

Фильтры могут применяться к сообщению с использованием свойства **filters**.
Каждый фильтр определяется хэшем, содержащим параметры, которые связаны с данным конкретным фильтром.
В следующих примерах показаны фильтры нижних колонтитулов и щелкните отслеживания фильтров:

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

## <a name="updateproperties"> </a>Практическое руководство. Обновление свойств электронной почты

Некоторые свойства электронной почты можно перезаписать с помощью оператора **set*Property** или добавить с помощью оператора **add*Property**. Например можно добавить дополнительных получателей с помощью

    email.addTo('jeff@contoso.com');
    or set a filter by using

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Дополнительные сведения см. в разделе [sendgrid nodejs][].

## <a name="useservices"> </a>Практическое руководство. Использование дополнительных служб SendGrid

SendGrid предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции
SendGrid из вашего приложения Azure. Дополнительные сведения см. [документация по SendGrid API][].

## <a name="nextsteps"> </a>Дальнейшие действия

Ознакомившись с основами почтовой службы SendGrid, воспользуйтесь приведенными ниже ссылками, чтобы узнать больше.

-   Репозиторий модуля Node.js SendGrid: [sendgrid-nodejs][]
-   Документация по интерфейсу SendGrid API: <https://sendgrid.com/docs>
-   Специальное предложение SendGrid для клиентов Azure: [http://sendgrid.com/Azure.HTML](https://sendgrid.com/windowsazure.html)

  [Дальнейшие действия]: http://www.windowsazure.com/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Что такое служба электронной почты SendGrid?]: #whatis
  [Создание учетной записи SendGrid]: #createaccount
  [Ссылка на модуль Node.js SendGrid]: #reference
  [Практическое руководство. Создание сообщения электронной почты]: #createemail
  [Практическое руководство. Отправка сообщения электронной почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Обновление свойств электронной почты]: #updateproperties
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [специальное предложение]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  
  [Настройки фильтра]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Документация по интерфейсу API SendGrid]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [Доставка электронной почты]: https://sendgrid.com/transactional-email

<!--HONumber=47-->
