<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="How to use the SendGrid email service (PHP) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Learn how send email with the SendGrid email service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Как использовать службу электронной почты SendGrid в языке PHP

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Образцы написаны на языке PHP.
 В число рассматриваемых сценариев входит **создание сообщений электронной почты**, **отправка сообщений электронной почты** и **добавление вложений**. Дополнительные сведения о SendGrid и отправке эл. почты см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Что такое служба электронной почты SendGrid][]
-   [Создание учетной записи SendGrid][]
-   [Использование SendGrid в своем приложении PHP][]
-   [Практическое руководство. Отправка сообщения электронной почты][]
-   [Практическое руководство. Добавление вложения][]
-   [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]
-   [Дальнейшие действия][]

## <a name="bkmk_WhatIsSendGrid"> </a>Что такое служба электронной почты SendGrid?

SendGrid — это [облачная служба электронной почты][], обеспечивающая надежные возможности
[доставки электронной почты][], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API
, которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной
     отправки клиентам электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и
     реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
-   Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте [][]<http://sendgrid.com></a>.

## <a name="bkmk_CreateSendGrid"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up][]]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Использование SendGrid в своем приложении PHP

Использование SendGrid в приложении Azure PHP не требует специальной
настройки или кода. Так как SendGrid — это служба, доступ к ней
можно получить как из облачного приложения, так и из приложения,
работающего на локальных ресурсах.

## <a name="bkmk_HowToSendEmail"> </a>Практическое руководство. Отправка сообщения электронной почты

Можно отправить сообщение эл. почты, используя SMTP или веб-интерфейс API, предоставляемые в SendGrid.

### Интерфейс SMTP API

Чтобы отправить сообщение эл. почты с помощью интерфейса SMTP API SendGrid, используйте *Swift Mailer*,
библиотеку на основе компонентов, которая предназначена для отправки сообщений эл. почты из приложений PHP.
Библиотеку *Swift Mailer* можно загрузить по ссылке
[][1]<http://swiftmailer.org/download></a>. Отправка сообщений электронной почты с использованием библиотеки
предполагает создание экземпляров классов
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2"> Swift\_Mailer</span> и
<span class="auto-style2"> Swift\_Message</span>, настройку
 соответствующих свойств, а также вызов метода
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

### Веб-интерфейс API

Используйте функцию [curl][] в PHP для отправки электронной почты с помощью веб-интерфейса API в SendGrid.

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

Веб-интерфейс API SendGrid очень похож на интерфейс REST API, хотя
по сути он не является полноценным интерфейсом RESTful API, поскольку в большинстве вызовов команды
 GET и POST взаимозаменяемы.

## <a name="bkmk_HowToAddAttachment"> </a>Практическое руководство. Добавление вложения

### Интерфейс SMTP API

Отправка вложений с использованием интерфейса SMTP API осуществляется одной строкой
 кода, которая добавляется в пример сценария для отправки сообщения эл. почты с помощью Swift Mailer.

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

Дополнительная строка кода выглядит следующим образом:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Эта строка кода вызывает метод вложения на объекте
<span class="auto-style2"> Swift\_Message</span> и использует статический
метод <span class="auto-style2">fromPath</span> в классе
<span class="auto-style2"> Swift\_Attachment</span> для получения и
 вложения файла к сообщению.

### Веб-интерфейс API

Отправка вложения с помощью веб-интерфейса API очень похожа на отправку
 электронной почты с помощью веб-интерфейса API. Однако обратите внимание, что в приведенном ниже примере
 массив параметров должен содержать этот элемент:

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

## <a name="bkmk_HowToUseFilters"> </a>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики

SendGrid предоставляет дополнительные функциональные возможности электронной почты благодаря использованию
 фильтров. Это параметры, которые могут быть добавлены в сообщение электронной почты для
 включения определенных функций, например включения отслеживания щелчков, аналитики Google
, отслеживание подписок и т. д.

Фильтры могут применяться к сообщению с использованием свойства фильтров. Каждый
 фильтр определяется хэшем, содержащим параметры, которые связаны с данным конкретным фильтром. В
 следующем примере используется фильтр нижнего колонтитула и указывается текстовое сообщение
, которое будет приведено в нижней части сообщения эл. почты:

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift 
     Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a> Дальнейшие действия

Вы познакомились с основами службы доставки электронной почты SendGrid. Для получения дополнительных сведений
 используйте следующие ссылки.

-   Документация SendGrid: <https://sendgrid.com/docs>
-   Специальное предложение SendGrid для клиентов Azure: <http://sendgrid.com/azure.html>

  [Дальнейшие действия]: #bkmk_NextSteps
  [Что такое служба электронной почты SendGrid]: #bkmk_WhatIsSendGrid
  [Создание учетной записи SendGrid]: #bkmk_CreateSendGrid
  [Использование SendGrid в своем приложении PHP]: #bkmk_UsingSendGridfromPHP
  [Практическое руководство. Отправка сообщения электронной почты]: #bkmk_HowToSendEmail
  [Практическое руководство. Добавление вложения]: #bkmk_HowToAddAttachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #bkmk_HowToUseFilters
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставки электронной почты]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [1]: http://swiftmailer.org/download
  [curl]: http://php.net/curl
