<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="Служба электронной почты SendGrid" pageTitle="Как использовать службу электронной почты SendGrid (Node.js)" metaKeywords="Azure SendGrid, служба электронной почты Azure, Azure SendGrid Node.js, служба электронной почты Azure Node.js" description="Узнайте, как отправлять сообщения электронной почты с помощью службы электронной почты SendGrid в Azure. Примеры написаны с помощью Node.js API." metaCanonical="" services="" documentationCenter="Node.js" title="Как отправлять электронную почту с помощью SendGrid из Node.js" authors="" solutions="" manager="" editor="" />





# Как отправлять электронную почту с помощью SendGrid из Node.js

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты
SendGrid в Azure. Примеры написаны с помощью Node.js
API. Здесь описаны такие сценарии, как **создание сообщение электронной почты**,
**отправка сообщения электронной почты**, **добавление вложений**, **использование фильтров** и
**обновление свойств**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе [Дальнейшие действия][].

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

SendGrid – это [облачная служба электронной почты], которая предоставляет надежные возможности
[доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной
    отправки клиентам электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам,
как заблокированная электронная почта и реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
-   Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте [http://sendgrid.com](http://sendgrid.com).

## <a name="createaccount"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Ссылка на модуль Node.js SendGrid

Модуль SendGrid для Node.js можно установить через диспетчер
пакетов узлов (npm) с помощью следующей команды:

    npm install sendgrid

После установки можно запросить этот модуль в приложение,
используя следующий код:

    var SendGrid = require('sendgrid')

Модуль SendGrid экспортирует функции **SendGrid** и **Email**.
**SendGrid** отвечает за отправку почты через SMTP или веб-API, а **Email** инкапсулирует сообщение электронной почты.

## <a name="createemail"> </a>Практическое руководство. Создание сообщения электронной почты

Для создания сообщения электронной почты с помощью модуля SendGrid сначала
создается сообщение электронной почты с помощью функции Email, а затем оно отправляется
с помощью функции SendGrid. Ниже приведен пример создания
нового сообщения с помощью функции Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Можно также создать сообщение HTML для клиентов, которые это
поддерживают, задав свойство html. Например:

    html: This is a sample <b>HTML<b> email message.

Задание свойств text и html обеспечивает корректный переход к
текстовому содержимому для клиентов, не поддерживающих сообщения в формате HTML.

Дополнительные сведения обо всех свойствах, поддерживаемых функцией Email, см. в описании [sendgrid-nodejs][].

## <a name="sendemail"> </a>Практическое руководство. Отправка сообщения электронной почты

После создания сообщения электронной почты с помощью функции Email можно отправить его с помощью SMTP или веб-API, предоставленных SendGrid. Дополнительные сведения
о преимуществах и различиях каждого интерфейса API см. в разделе [SMTP и веб-интерфейс API][]
в документации по SendGrid.

Для использования SMTP API или веб-API сначала требуется инициализировать
функцию SendGrid, используя имя пользователя и ключ учетной записи SendGrid
следующим образом:

    var sender = new SendGrid.SendGrid('user','key');

Теперь сообщение можно отправить с помощью SMTP или веб-API. Вызовы являются
практически идентичными, передавая сообщения электронной почты и необязательную
функцию обратного вызова. Обратный вызов используется для определения
успеха или сбоя операции. На следующих примерах показано, как отправить
сообщение с использованием и SMTP, и веб-интерфейса API.

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
<p>Хотя в приведенных выше примерах показана передача для объекта электронной почты
и функции обратного вызова, можно напрямую вызвать функции отправки и SMTP,
задав свойства электронной почты. Например:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Практическое руководство. Добавление вложения

В сообщение можно добавить вложения, указав имена файлов и путь к ним в свойстве **files**. В следующем примере демонстрируется
отправка вложения.

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
<p>При использовании свойства <strong>files</strong> файл должен быть доступен через <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Если вы хотите приложить файл, находящийся в хранилище Azure, например контейнер BLOB-объектов, необходимо сначала скопировать этот файл в локальное хранилище или на диск Azure, прежде чем его можно будет отправить как вложение с помощью свойства <strong>files</strong>.</p>
</div>

## <a name="usefilters"> </a>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и Twitter

SendGrid предоставляет дополнительные функциональные возможности электронной почты благодаря использованию
фильтров. Это параметры, которые могут быть добавлены в сообщение электронной почты для включения определенных функций, например включения отслеживания щелчков, отслеживание подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

Фильтры могут применяться к сообщению с использованием свойства **filters**.
Каждый фильтр определяется хэшем, содержащим параметры, которые связаны с данным конкретным фильтром.
В следующих примерах показаны фильтры нижних колонтитулов, отслеживания щелчков и
фильтры Twitter.

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

Некоторые свойства электронной почты могут быть перезаписаны с помощью операторов **set*свойство*** или добавлены с помощью **add*свойство***. Например, можно добавить дополнительных
получателей с помощью

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

SendGrid предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid из вашего приложения Azure. Исчерпывающие сведения см. в документации по [интерфейсу SendGrid API][].

## <a name="nextsteps"> </a>Дальнейшие действия

Вы узнали основные сведения о службе доставки электронной почты SendGrid. Для получения дополнительных сведений используйте следующие ссылки.

-   Репозиторий модуля Node.js SendGrid: [sendgrid-nodejs][]
-   Документация по интерфейсу SendGrid API:
    <http://docs.sendgrid.com/documentation/api/>
-   Специальное предложение по SendGrid для клиентов Azure:
    [http://sendgrid.com/azure.html](http://sendgrid.com/azure.html)

  [Дальнейшие действия]: http://www.windowsazure.com/ru-ru/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Что такое служба электронной почты SendGrid?]: #whatis
  [Создание учетной записи SendGrid]: #createaccount
  [Ссылка на модуль Node.js SendGrid]: #reference
  [Практическое руководство. Создание сообщения электронной почты]: #createemail
  [Практическое руководство. Отправка сообщения электронной почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Обновление свойств электронной почты]: #updateproperties
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [специальное предложение]: http://www.sendgrid.com/azure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP и веб-интерфейс API]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  
  [Параметры фильтров]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [интерфейсу SendGrid API]: http://docs.sendgrid.com/documentation/api/
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставка электронной почты]: http://sendgrid.com/transactional-email

