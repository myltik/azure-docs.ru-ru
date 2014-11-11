<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Как в Azure выполнить телефонный звонок с помощью Twilio в PHP-приложении

В следующем примере показано, как выполнить звонок с веб-страницы PHP, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Форма звонка Azure с использованием службы Twilio и PHP][Форма звонка Azure с использованием службы Twilio и PHP]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1.  Получение учетной записи Twilio и токена подтверждения подлинности. Перед началом работы с Twilio вы можете ознакомиться с ценовой политикой на странице [][]<http://www.twilio.com/pricing></a>. Также вы можете подписаться на пробную учетную запись на странице [][1]<https://www.twilio.com/try-twilio></a>. Дополнительные сведения о предоставляемых службой Twilio API-интерфейсах см. на странице [][2]<http://www.twilio.com/api></a>.
2.  Подтвердите в Twilio номер своего телефона в качестве идентификатора исходящего абонента. Для получения сведений о подтверждении своего номера телефона см. [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Вместо использования существующего номера вы можете приобрести телефонный номер Twilio. Для нужд даннoго примера используйте подтвержденный номер в качестве значения **Из** на странице **callform.php** (описана ниже).
3.  Получите библиотеку Twilio для PHP. Ее можно загрузить с сайта Github ([][4]<https://github.com/twilio/twilio-php></a>) или установить в виде пакета PEAR. Дополнительные сведения см. в разделе [][5]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.
4.  Установите Azure SDK для PHP. Обзор пакета SDK и инструкции по его установке, см. в разделе [Настройка пакета Azure SDK для PHP][Настройка пакета Azure SDK для PHP].

## Создание веб-формы для выполнения звонка

В следующем HTML-коде показано, как построить веб-страницу (**callform.html**), позволяющую извлечь данные пользователя для выполнения звонка:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## Создание кода для выполнения звонка

Следующий код призван показать процесс построения веб-страницы (**makecall.php**), которая вызывается, когда пользователь отправляет форму с **callform.html**. Показанный ниже код создает сообщение звонка и выполняет его. Вместо заполнителей **$sid** и **$token** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Помимо выполнения звонка, на странице **makecall.php** отображаются некоторые метаданные о нем (см. снимок экрана ниже). Дополнительные сведения о метаданных звонка см. в разделе [][6]<https://www.twilio.com/docs/api/rest/call#instance-properties></a>.

![Ответ на звонок Azure с использованием службы Twilio и PHP][Ответ на звонок Azure с использованием службы Twilio и PHP]

## Выполнение приложения

Далее следует развернуть приложение в Azure Websites. Следующие статьи содержат сведения о создании веб-сайта и развертывании кода с помощью Git, FTP или WebMatrix (но не вся информация в каждой статье относится к этим темам).

-   [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git][Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git]
-   [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью FTP][Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью FTP]
-   [Создание и развертывание веб-сайта PHP-MySQL Azure с использованием WebMatrix][Создание и развертывание веб-сайта PHP-MySQL Azure с использованием WebMatrix]

## Дальнейшие действия

Данный код демонстрирует базовую функциональность, доступную через PHP-библиотеку Twillio в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

-   Вместо использования веб-формы для хранения телефонных номеров и текста вызова можно применить большие двоичные объекты хранилища Azure или SQL Database. Дополнительные сведения об использовании BLOB-объектов Azure в PHP см. в разделе [Использование службы хранения BLOB-объектов Azure в PHP-приложениях][Использование службы хранения BLOB-объектов Azure в PHP-приложениях]. Дополнительные сведения об использовании базы данных SQL в PHP см. в разделе [Использование базы данных SQL в PHP-приложениях][Использование базы данных SQL в PHP-приложениях].
-   Код **makecall.php** использует URL-адрес, предоставляемый Twilio ([][7]<http://twimlets.com/message></a>), для выдачи ответа в формате Twilio Markup Language (TwiML), информирующего Twilio о способе обработки вызова. Например, возвращаемый ответ TwiML может содержать команду `<Say>`, которая задает голосовое воспроизведение текста вызываемому абоненту. Вместо предоставляемого Twilio URL-адреса вы можете построить собственную службу, отвечающую на запросы Twilio. Дополнительные сведения см. в разделе [Использование Twilio для поддержки голосовых возможностей и SMS в PHP][Использование Twilio для поддержки голосовых возможностей и SMS в PHP]. Дополнительные сведения о TwiML см. на странице [][8]<http://www.twilio.com/docs/api/twiml></a>. Дополнительные сведения о команде `<Say>` и других командах Twilio см. на странице [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Руководства по безопасности работы с Twilio — [][10]<https://www.twilio.com/docs/security></a>.

Дополнительные сведения о Twilio см. на странице [][11]<https://www.twilio.com/docs></a>.

## См. также

-   [Использование Twilio для поддержки голосовых возможностей и SMS в PHP][Использование Twilio для поддержки голосовых возможностей и SMS в PHP]

  [Форма звонка Azure с использованием службы Twilio и PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: https://github.com/twilio/twilio-php
  [5]: https://github.com/twilio/twilio-php/blob/master/README.md
  [Настройка пакета Azure SDK для PHP]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
  [6]: https://www.twilio.com/docs/api/rest/call#instance-properties
  [Ответ на звонок Azure с использованием службы Twilio и PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
  [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-git/
  [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью FTP]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-ftp/
  [Создание и развертывание веб-сайта PHP-MySQL Azure с использованием WebMatrix]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-webmatrix/
  [Использование службы хранения BLOB-объектов Azure в PHP-приложениях]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh674502(v=vs.103).aspx
  [Использование базы данных SQL в PHP-приложениях]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh674500(v=vs.103).aspx
  [7]: http://twimlets.com/message
  [Использование Twilio для поддержки голосовых возможностей и SMS в PHP]: ../partner-twilio-php-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
