<properties urlDisplayName="SendGrid Email Service" pageTitle="Использование службы электронной почты SendGrid (Node.js) - Azure" metaKeywords="Azure SendGrid, служба электронной почты Azure, Azure SendGrid Node.js, Node.js для электронной почты Azure" description="Узнайте, как отправить электронное сообщение с помощью службы электронной почты SendGrid в Azure. Примеры написаны с использованием API Node.js." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />





# Как отправлять электронную почту с помощью SendGrid из Node.js

В этом руководстве показано, как выполнять типовые задачи программирования с помощью
службы электронной почты SendGrid в Azure. Примеры написаны с использованием интерфейса API Node.js. Описываемые здесь сценарии включают в себя **создание электронного сообщения**,**отправку электронного сообщения**, **добавление вложений**, **использование фильтров** и**обновление свойств**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе [Дальнейшие действия][].

## Оглавление

* [Что такое служба электронной почты SendGrid?][]   
* [Создание учетной записи SendGrid][]   
* [Справочная информация о модуле Node.js SendGrid][]   
* [Практическое руководство. Создание сообщения электронной почты][]   
* [Практическое руководство. Отправка сообщения электронной почты][]   
* [Практическое руководство. Прикрепление вложения][]   
* [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]   
* [Практическое руководство. Обновление свойств электронной почты][]   
* [Практическое руководство. Использование дополнительных служб SendGrid][]   
* [Дальнейшие действия][1]

## <a name="whatis"> </a>Что такое служба электронной почты SendGrid?

SendGrid - это [облачная служба электронной почты], обеспечивающая надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространены следующие сценарии использования SendGrid:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для отправки клиентам ежемесячно
    электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и 
    реагирование на запросы клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
-   Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте [https://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Ссылка на модуль Node.js SendGrid

Модуль SendGrid для Node.js можно установить через диспетчер пакетов узлов (npm) с помощью следующей команды:

    npm install sendgrid

После установки вы можете запросить наличие этого модуля в приложении, используя следующий код:

    var SendGrid = require('sendgrid')

Модуль SendGrid экспортирует функции **SendGrid** и **Email**. **SendGrid** отвечает за отправку почты через SMTP или веб-API, а **Email** инкапсулирует сообщение электронной почты.

## <a name="createemail"> </a>Практическое руководство. Создание электронного сообщения

Создание сообщения электронной почты с помощью модуля SendGrid состоит из двух этапов:
создание сообщения электронной почты с помощью функции Email и его отправка
с помощью функции SendGrid. Ниже приведен пример создания
нового сообщения с помощью функции Email.

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Вы также можете создать HTML-сообщение для клиентов, которые поддерживают этот формат, задав свойство html. Например: 

    html: This is a sample <b>HTML<b> email message.

Задав свойства text и html, можно обеспечить корректный переход к текстовому содержимому для клиентов, которые не поддерживают формат HTML.

Дополнительную информацию о всех свойствах, поддерживаемых функцией Email, см. в разделе [sendgrid-nodejs][].

## <a name="sendemail"> </a>Практическое руководство. Отправка сообщения электронной почты

После создания электронного сообщения с помощью функции Email его можно отправить с помощью протокола SMTP или веб-интерфейса API, предоставленных SendGrid. Дополнительные сведения о преимуществах и различиях каждого интерфейса API см. в разделе[SMTP и веб-интерфейс API][] в документации по SendGrid.

Для использования интерфейса API SMTP или веб-интерфейса API сначала необходимо инициализировать функцию SendGrid, используя имя пользователя и ключ учетной записи SendGrid, как показано ниже.

    var sender = new SendGrid ('пользователь','ключ');

Теперь сообщение можно отправить с помощью SMTP или веб-интерфейса API. Вызовы являются практически идентичными. Они передают электронные сообщения и необязательную функцию обратного вызова. Обратный вызов используется, чтобы определить успешное завершение операции или ее сбой. В следующих примерах показано, как отправить сообщение с помощью сразу двух интерфейсов: SMTP и веб-интерфейса API.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### Веб-интерфейс API

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Хотя в приведенных выше примерах показана передача для объекта электронной почты и функции обратного вызова, вы можете напрямую вызвать функции отправки и SMTP, задав свойства электронной почты. Например: </p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Практическое руководство. Добавление вложения

К сообщению можно прикрепить вложения, указав имена файлов и путь к ним в свойстве **files**. В следующем примере показана отправка вложения.

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>Примечание.</strong>
<p>В случае использования свойства <strong>files</strong> файл должен быть доступен
через <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Если вы хотите приложить файл, находящийся в хранилище Azure, например контейнер BLOB-объектов, необходимо сначала скопировать этот файл в локальное хранилище или на диск Azure, прежде чем его можно будет отправить как вложение с помощью свойства <strong>files</strong>.</p>
</div>

## <a name="usefilters"> </a>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и Twitter

SendGrid поддерживает дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это параметры, которые могут быть добавлены в сообщение электронной почты для включения определенных функций, например включения отслеживания щелчков, аналитики Google, отслеживания подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

Фильтры можно применять к сообщению с помощью свойства **filters**. Каждый фильтр определяется хэшем, содержащим параметры, которые связаны с данным конкретным фильтром. В следующих примерах показаны фильтры нижних колонтитулов, отслеживания щелчков и фильтры Twitter.

### Нижний колонтитул

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Отслеживание щелчков

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

## <a name="updateproperties"> </a>Практическое руководство. Обновление свойств электронной почты

Некоторые свойства электронной почты могут быть перезаписаны с помощью **set*Property*** или добавлены с помощью **add*Property***. Например, вы можете добавить дополнительных получателей с помощью

    email.addTo('jeff@contoso.com');

или задать фильтр с помощью

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Дополнительные сведения см. в разделе [sendgrid-nodejs][].

## <a name="useservices"> </a>Практическое руководство. Использование дополнительных служб SendGrid

SendGrid поддерживает различные веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid, доступные в приложении Azure. Дополнительные сведения см. в [документации по API-интерфейсу SendGrid][].

## <a name="nextsteps"> </a>Дальнейшие действия

Вы получили основные сведения о службе доставки электронной почты SendGrid. Дополнительные сведения вы найдете по следующим ссылкам.

-   Репозиторий модуля SendGrid Node.js: [sendgrid-nodejs][]
-   Документация по интерфейсу SendGrid API:
    <https://sendgrid.com/docs>
-   Специальное предложение SendGrid для клиентов Azure:
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [Дальнейшие действия]: http://www.windowsazure.com/ru-ru/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Что такое служба электронной почты SendGrid?]: #whatis
  [Создание учетной записи SendGrid]: #createaccount
  [Ссылка на модуль Node.js SendGrid]: #reference
  [Практическое руководство. Создание электронного сообщения]: #createemail
  [Практическое руководство. Отправка сообщения электронной почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Обновление свойств электронной почты]: #updateproperties
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [специальное предложение]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
