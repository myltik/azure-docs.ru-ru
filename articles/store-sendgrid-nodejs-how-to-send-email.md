<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Как отправлять электронную почту с помощью SendGrid из Node.js

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры написаны с использованием API Node.js. Описываемые здесь сценарии включают в себя **создание электронного сообщения**, **отправку электронного сообщения**, **добавление вложений**, **использование фильтров** и **обновление свойств**. Дополнительную информацию о SendGrid и отправке электронной почты см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое служба электронной почты SendGrid?][Что такое служба электронной почты SendGrid?]
-   [Создание учетной записи SendGrid][Создание учетной записи SendGrid]
-   [Ссылка на модуль Node.js SendGrid][Ссылка на модуль Node.js SendGrid]
-   [Практическое руководство. Создание электронного сообщения][Практическое руководство. Создание электронного сообщения]
-   [Практическое руководство. Отправка сообщения электронной почты][Практическое руководство. Отправка сообщения электронной почты]
-   [Практическое руководство. Добавление вложения][Практическое руководство. Добавление вложения]
-   [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]
-   [Практическое руководство. Обновление свойств электронной почты][Практическое руководство. Обновление свойств электронной почты]
-   [Практическое руководство. Использование дополнительных служб SendGrid][Практическое руководство. Использование дополнительных служб SendGrid]
-   [Дальнейшие действия][1]

## <a name="whatis"> </a>Что такое служба электронной почты SendGrid?

SendGrid — это [облачная служба электронной почты][облачная служба электронной почты], обеспечивающая надежные возможности [доставки электронной почты][доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API , которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
-   Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте [][]<http://sendgrid.com></a>.

## <a name="createaccount"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Ссылка на модуль Node.js SendGrid

Модуль SendGrid для Node.js можно установить через диспетчер пакетов узлов (npm) с помощью следующей команды:

    npm install sendgrid

После установки вы можете запросить наличие этого модуля в приложении, используя следующий код:

    var SendGrid = require('sendgrid')

Модуль SendGrid экспортирует функции **SendGrid** и **Email**. Функция **SendGrid** отвечает за отправку электронной почты через SMTP или веб-API, а **Email** — за инкапсуляцию электронного сообщения.

## <a name="createemail"> </a>Практическое руководство. Создание электронного сообщения

Для создания электронного сообщения с помощью модуля SendGrid сначала создается электронное сообщение с помощью функции Email, а затем оно отправляется с помощью функции SendGrid. В следующем примере показано создание нового сообщения с помощью функции Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Вы также можете создать HTML-сообщение для клиентов, которые поддерживают этот формат, задав свойство html. Например:

    html: This is a sample <b>HTML<b> email message.

Задав свойства text и html, можно обеспечить корректный переход к текстовому содержимому для клиентов, которые не поддерживают формат HTML.

Дополнительную информацию о всех свойствах, поддерживаемых функцией Email, см. в разделе [sendgrid-nodejs][sendgrid-nodejs].

## <a name="sendemail"> </a>Практическое руководство. Отправка сообщения электронной почты

После создания электронного сообщения с помощью функции Email его можно отправить с помощью протокола SMTP или веб-API, предоставленных SendGrid. Дополнительную информацию о преимуществах каждого из этих API и различиях между ними см. в разделе [SMTP и веб-API][SMTP и веб-API] документации по SendGrid.

Для использования API SMTP или веб-API сначала требуется инициализировать функцию SendGrid, используя имя пользователя и ключ учетной записи SendGrid, как показано ниже:

    var sender = new SendGrid('user','key');

Теперь сообщение можно отправить с помощью SMTP или веб-API. Вызовы являются практически идентичными, передавая электронные сообщения и необязательную функцию обратного вызова. Обратный вызов используется, чтобы определить успешное завершение операции или ее сбой. В следующих примерах показано, как отправить сообщение с помощью одновременно двух интерфейсов: SMTP и веб-API.

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
<p>Хотя в приведенных выше примерах показана передача для объекта электронной почты и
функции обратного вызова, вы можете напрямую вызвать функции send и smtp,
задав свойства электронной почты. Например:</p>
<pre class="prettyprint">sender.send({
    на: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Практическое руководство. Добавление вложения

В сообщение можно добавить вложения, указав имена файлов и путь к ним в свойстве **files**. В следующем примере показана отправка вложения:

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
<p>При использовании свойства <strong>files</strong> файл должен быть доступен
через <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Если вы хотите вложить файл, находящийся в службе хранилища Azure, например контейнер больших двоичных объектов, необходимо сначала скопировать этот файл в локальное хранилище или на диск Azure, прежде чем его можно будет отправить как вложение с помощью свойства <strong>files</strong>.</p>
</div>

## <a name="usefilters"> </a>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и Twitter

SendGrid поддерживает дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это параметры, которые могут быть добавлены в сообщение электронной почты для  включения определенных функций, например включения отслеживания щелчков, аналитики Google , отслеживание подписок и т. д. Полный список фильтров см. в разделе[Параметры фильтрации][Параметры фильтрации].

Фильтры могут применяться к сообщению с помощью свойства **filters**. Каждый фильтр определяется хэшем, содержащим параметры, которые связаны с данным конкретным фильтром. В следующих примерах показаны фильтры нижних колонтитулов, отслеживания щелчков и фильтры Twitter:

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

Некоторые свойства электронной почты могут быть перезаписаны с помощью оператора **set*свойство*** или добавлены с помощью оператора **add*свойство***. Например, вы можете добавить дополнительных получателей с помощью

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

Дополнительные сведения см. в разделе [sendgrid-nodejs][sendgrid-nodejs].

## <a name="useservices"> </a>Практическое руководство. Использование дополнительных служб SendGrid

SendGrid поддерживает различные веб-API, с помощью которых вы можете использовать дополнительные функции SendGrid, доступные в приложении Azure. Дополнительную информацию см. в [документации по интерфейсу API SendGrid][документации по интерфейсу API SendGrid].

## <a name="nextsteps"> </a> Дальнейшие действия

Вы познакомились с основами службы доставки электронной почты SendGrid. Для получения дополнительных сведений используйте следующие ссылки.

-   Репозиторий модуля Node.js SendGrid: [sendgrid-nodejs][sendgrid-nodejs]
-   Документация по интерфейсу API SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Специальное предложение SendGrid для клиентов Azure:
    [][2]<http://sendgrid.com/azure.html></a>

  [Дальнейшие действия]: http://www.windowsazure.com/ru-ru/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Создание учетной записи SendGrid]: #createaccount
  [Ссылка на модуль Node.js SendGrid]: #reference
  [Практическое руководство. Создание электронного сообщения]: #createemail
  [Практическое руководство. Отправка сообщения электронной почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Обновление свойств электронной почты]: #updateproperties
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  [1]: #nextsteps
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставки электронной почты]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP и веб-API]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [Параметры фильтрации]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [документации по интерфейсу API SendGrid]: http://docs.sendgrid.com/documentation/api/
  [2]: http://sendgrid.com/azure.html
