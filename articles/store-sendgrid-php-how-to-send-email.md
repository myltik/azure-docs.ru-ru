<properties title="Как использовать службу электронной почты SendGrid (PHP) — Azure" pageTitle="Как использовать службу электронной почты SendGrid (PHP) — Azure" metaKeywords="Azure SendGrid, служба электронной почты Azure, Azure SendGrid PHP, служба электронной почты Azure PHP" description="Как отправлять сообщения электронной почты с помощью службы электронной почты SendGrid в Azure. Примеры кода написаны в PHP." documentationCenter="PHP" services="" />

# Как использовать службу электронной почты SendGrid в языке PHP

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры написаны на PHP.
Описываемые здесь сценарии включают **создание сообщения эл. почты**, **отправку сообщения эл. почты** и **добавление вложений**. Дополнительные сведения о SendGrid и отправке эл. почты см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Что такое служба электронной почты SendGrid][]
-   [Создание учетной записи SendGrid][]
-   [Использование SendGrid в своем приложении PHP][]
-   [Практическое руководство. Отправка сообщения эл. почты][]
-   [Практическое руководство. Добавление вложения][]
-   [Практическое руководство. использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]
-   [Практическое руководство. Использование дополнительных служб SendGrid][]
-   [Дальнейшие действия][]

## <a name="bkmk_WhatIsSendGrid"> </a>Что такое служба электронной почты SendGrid?

SendGrid – это [облачная служба электронной почты], которая предоставляет надежные возможности
[доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений
- Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
- Уведомления от приложения по электронной почте

Дополнительные сведения см. в разделе [http://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Использование SendGrid в своем приложении PHP

Использование SendGrid в приложении Azure PHP не нуждается в какой-либо особой конфигурации или написании специального кода. Так как SendGrid — это служба, доступ к ней можно получить как из облачного приложения, так и из приложения, работающего на локальных ресурсах.

После добавления в свое приложение поддержку электронной почты, можно упаковать и развернуть свое приложение, следуя приведенным здесь методам:
[Упаковка и развертывание приложений PHP для Azure][].

## <a name="bkmk_HowToSendEmail"> </a>Практическое руководство. Отправка сообщения эл. почты

Можно отправить сообщение эл. почты, используя SMTP или веб-интерфейс API, предоставляемые в SendGrid. Дополнительные сведения о преимуществах и различиях каждого интерфейса API см. в разделе
[SMTP и веб-интерфейс API][] в документации по SendGrid.

### Интерфейс SMTP API

Чтобы отправить сообщение эл. почты с помощью интерфейса SMTP API SendGrid, используйте *Swift Mailer*, библиотеку на основе компонентов, которая предназначена для отправки сообщений эл. почты из приложений PHP. Библиотеку *Swift Mailer* можно загрузить из
[http://swiftmailer.org/download][]. Отправка сообщений эл. почты с помощью этой библиотеки
включает создание экземпляров классов
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> и
<span class="auto-style2">Swift\_Message</span>, настройку соответствующих свойств и вызов метода
<span class="auto-style2">Swift\_Mailer::send</span>.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

**Примечание.**Приведенный выше пример сценария взят из документации по SendGrid,
доступной здесь:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/][].

Дополнительные сведения об интерфейсе API SMTP и заголовке X-SMTPAPI см. в руководстве разработчика по интерфейсу SMTP API в документации SendGrid, которую можно взять здесь:
[http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/][].
Дополнительные примеры по использованию интерфейса SMTP API с PHP см. в документации по SendGrid, которую можно взять здесь:
[http://docs.sendgrid.com/documentation/api/smtp-api/php-example/][].

### Веб-интерфейс API

Используйте [функцию curl][] в PHP для отправки электронной почты с помощью веб-интерфейса API в SendGrid.

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@sendgrid.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

**Примечание.**Приведенный выше пример сценария взят из документации по SendGrid, доступный здесь:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/][].

**Примечание.**Веб-интерфейс API SendGrid очень похож на интерфейс REST API, хотя по сути он не является полноценным интерфейсом REST API, поскольку в большинстве вызовов команды GET и POST взаимозаменяемы.

Обзор веб-интерфейса API см. в документации по SendGrid, которую можно получить здесь:
[http://docs.sendgrid.com/documentation/api/web-api/][].

Полный список параметров и общие примеры по использованию интерфейса API для отправки почты см. в разделе [http://docs.sendgrid.com/documentation/api/web-api/mail/][].

## <a name="bkmk_HowToAddAttachment"> </a>Практическое руководство. Добавление вложения

### Интерфейс SMTP API

Отправка вложений с использованием интерфейса SMTP API осуществляется одной строкой кода, которая добавляется в пример сценария для отправки сообщения эл. почты с помощью Swift Mailer.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - "
          print_r($failures);
     }

**Примечание.**Приведенный выше пример сценария взят из документации по SendGrid, доступный здесь:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/][].

Дополнительная строка кода выглядит следующим образом:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName(ï¿½file_nameï¿½));

В этой строке кода вызывается метод вложения для объекта
<span class="auto-style2">Swift\_Message</span> и используется статический
метод <span class="auto-style2">fromPath</span> класса
<span class="auto-style2">Swift\_Attachment</span> для получения файла и вложения его в сообщение.

### Веб-интерфейс API

Отправка вложения с помощью веб-интерфейса API очень похожа на отправку электронной почты с помощью веб-интерфейса API. Однако обратите внимание, что в примере, который приведен ниже, массив параметров должен содержать этот элемент:

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@sendgrid.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

**Примечание.**Приведенный выше пример сценария взят из документации по SendGrid, доступный здесь:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/][].

## <a name="bkmk_HowToUseFilters"> </a>Практическое руководство. использование фильтров для включения нижних колонтитулов, отслеживания и аналитики

SendGrid предоставляет дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это параметры, которые могут быть добавлены в сообщение электронной почты для включения определенных функций, например включения отслеживания щелчков, отслеживание подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

Фильтры могут применяться к сообщению с использованием свойства фильтров. Каждый фильтр определяется хэшем, содержащим параметры, связанные с тем или иным фильтром. В следующем примере используется фильтр нижнего колонтитула и указывается текстовое сообщение, которое будет приведено в нижней части сообщения эл. почты:

    <?php
     /*
      * Этот пример используется для Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // Список адресов, на которые будет отправлено это сообщение
     // [Этот список используется для отправки нескольких сообщений эл. почты с использованием ОДНОГО запроса SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Укажите имена получателей
     $nameList = array('Name 1', 'Name 2');
     
     // Используется в качестве примера подстановки переменной
     $timeList = array('4 PM', '5 PM');
     
     // Задаются все вышеперечисленные переменные
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Укажите, что это исходное сообщение контакта
     $hdr->setCategory("initial");
     
     // При необходимости можно настроить в этом примере отдельные фильтры; мы включили фильтр нижнего колонтитула
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Благодарим за сотрудничество!");
     
     // Тема вашего сообщения эл. почты
     $subject = 'Пример службы электронной почты SendGrid';
     
     // Откуда отправлено это сообщение. Например, это сообщение может быть от support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Название вашей компании');
     
     // Если выше не был указан список отправителей, здесь можно указать пользователя. Если 
     // список отправителей указан выше, этот адрес электронной почты не используется.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Создание текста сообщения (обычного текста и HTML-версии). 
     # text – это сообщение эл. почты с обычным текстом 
     # html – это html-версия сообщения эл. почты
     # если получатель может просмотреть сообщения эл. почты в формате html, тогда только html
     # будет отображаться сообщение эл. почты
     
     /*
     * Обратите внимание на используемую здесь подстановку переменной =)
     */
     $text = <<<EOM 
     Здравствуйте, -имя-!
     Благодарим вас за проявленный интерес к нашей продукции. Мы организовали встречу, чтобы позвонить вам в -time- по магаданскому времени и обсудить более конкретно ваши потребности.
     С уважением,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Здравствуйте, -имя-!<br>
     Благодарим вас за проявленный интерес к нашей продукции. Мы организовали встречу, чтобы позвонить вам в -time- по магаданскому времени и обсудить более конкретно ваши потребности.
     
     С уважением,
     
     Вася, как вы?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Ваши учетные данные учетной записи SendGrid
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Создание нового подключения swift и проверка подлинности
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Создание сообщения (тема)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****ВАЖНОЕ ПРИМЕЧАНИЕ*****
     // Функция SendGrid asJSON нуждается в escape-последовательности символов. Если используется Swift Mailer
     // Функции PHP Mailer, функция getTextHeader также нуждается в escape-последовательности символов.
     // Это может привести к удалению фильтра.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // вложения основной части сообщения эл. почты
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // отправка сообщения
     if ($recipients = $swift->send($message, $failures))
     {
     // Это даст нам знать, сколько пользователей получило это сообщение
     // Если мы укажем имена в заголовке X-SMTPAPI, это всегда будет равным 1.
     echo 'Сообщение отправлено следующему количеству пользователей: '.$recipients.'';
     }

     // что-то пошло не так =(
     else
     {
     echo "Что-то пошло не так – ";
     print_r($failures);
     }

**Примечание.**Приведенный выше пример сценария взят из документации по SendGrid, доступный здесь:
[http://docs.sendgrid.com/documentation/api/smtp-api/php-example/][].

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Практическое руководство. Использование дополнительных служб SendGrid

SendGrid предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid из вашего приложения Azure. Исчерпывающие сведения см. в документации по [интерфейсу SendGrid API][].

## <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы узнали основные сведения о службе доставки электронной почты SendGrid. Для получения дополнительных сведений используйте следующие ссылки.

-   Документация SendGrid API: <http://docs.sendgrid.com/documentation/api/>
-   Специальное предложение SendGrid для клиентов Azure:
    [http://sendgrid.com/azure.html][]

  [Дальнейшие действия]: #bkmk_NextSteps
  [Что такое служба электронной почты SendGrid]: #bkmk_WhatIsSendGrid
  [Создание учетной записи SendGrid]: #bkmk_CreateSendGrid
  [Использование SendGrid в своем приложении PHP]: #bkmk_UsingSendGridfromPHP
  [Практическое руководство. Отправка сообщения эл. почты]: #bkmk_HowToSendEmail
  [Практическое руководство. Добавление вложения]: #bkmk_HowToAddAttachment
  [Практическое руководство. использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #bkmk_HowToUseFilters
  [Практическое руководство. Использование дополнительных служб SendGrid]: #bkmk_HowToUseAdditionalSvcs

  [http://sendgrid.com]: http://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [специальное предложение]: http://www.sendgrid.com/azure.html
  [http://docs.sendgrid.com/documentation/get-started/]: http://docs.sendgrid.com/documentation/get-started/
  [http://sendgrid.com/features]: http://sendgrid.com/features
  [Упаковка и развертывание приложений PHP для Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh674499(v=VS.103).aspx
  [SMTP и веб-интерфейс API]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/
  [http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/]: http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/
  [http://docs.sendgrid.com/documentation/api/smtp-api/php-example/]: http://docs.sendgrid.com/documentation/api/smtp-api/php-example/
  [функцию curl]: http://php.net/curl
  [http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/
  [http://docs.sendgrid.com/documentation/api/web-api/]: http://docs.sendgrid.com/documentation/api/web-api/
  [http://docs.sendgrid.com/documentation/api/web-api/mail/]: http://docs.sendgrid.com/documentation/api/web-api/mail/
  [Параметры фильтров]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [интерфейсу SendGrid API]: http://docs.sendgrid.com/documentation/api/
  [http://sendgrid.com/azure.html]: http://sendgrid.com/azure.html
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставка электронной почты]: http://sendgrid.com/transactional-email

