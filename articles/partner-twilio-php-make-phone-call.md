<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="Как в Azure выполнить телефонный звонок с помощью Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, телефонные звонки Azure, Azure twilio, Azure SMS, Azure SMS, голосовые вызовы Azure, голосовые вызовы azure, текстовые сообщения Azure, текстовые сообщения Azure, PHP twilio Azure" description="Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры, для приложения PHP." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="twilio" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Как в Azure выполнить телефонный звонок с помощью Twilio в PHP-приложении 

В следующем примере показано, как выполнить звонок с веб-страницы PHP, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Azure Call Form Using Twilio and PHP][twilio_php]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получение учетной записи Twilio и токена подтверждения подлинности. Перед началом работы с Twilio вы можете ознакомиться с ценовой политикой на странице [http://www.twilio.com/pricing][twilio_pricing]. Также вы можете подписаться на пробную учетную запись на странице [https://www.twilio.com/try-twilio][try_twilio]. Дополнительные сведения о предоставляемых службой Twilio API-интерфейсах см. на странице [http://www.twilio.com/api][twilio_api].
2. Получите библиотеку Twilio для PHP. Ее можно загрузить с сайта Github ([https://github.com/twilio/twilio-php][twilio_php_github]) или установить в виде пакета PEAR. Дополнительные сведения см. в разделе [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].
3. Установите Azure SDK для PHP. Обзор пакета SDK и инструкции по его установке, см. в разделе [Настройка пакета Azure SDK для PHP][setup_php_sdk].

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
Приведенный ниже код иллюстрирует создание веб-страницы (**makecall.php**), которая вызывается, когда пользователь отправляет форму, отображаемую на странице **callform.html**. Показанный ниже код создает сообщение звонка и выполняет его. Вместо заполнителей **$sid** и **$token** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.

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

Помимо выполнения звонка, на странице **makecall.php** отображаются некоторые метаданные о нем (см. снимок экрана ниже). Дополнительные сведения о метаданных звонка см. на странице [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure Call Response Using Twilio and PHP][twilio_php_response]

## Выполнение приложения
Далее следует развернуть приложение в Azure Websites. Следующие статьи содержат сведения о создании веб-сайта и развертывании кода с помощью Git, FTP или WebMatrix (но не вся информация в каждой статье относится к этим темам).

* [Создание веб-сайта PHP-MySQL в Azure и его развертывание с помощью Git][website-git]
* [Создание веб-сайта PHP-MySQL в Azure и его развертывание с помощью FTP][website-ftp]
* [Создание и развертывание веб-сайта PHP-MySQL в Azure с помощью WebMatrix][website-webmatrix]

## Дальнейшие действия
Данный код демонстрирует базовую функциональность, доступную через PHP-библиотеку Twillio в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

* Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или базу данных SQL Azure. Дополнительные сведения об использовании BLOB-объектов Azure в PHP см. в разделе [Использование службы хранения BLOB-объектов Azure в PHP-приложениях][howto_blob_storage_php]. Дополнительные сведения об использовании базы данных SQL в PHP см. в разделе [Использование базы данных SQL в PHP-приложениях][howto_sql_azure_php].
* В коде **makecall.php** используется предоставляемый Twilio URL-адрес ([http://twimlets.com/message][twimlet_message_url]), по которому предоставляется ответ TwiML с инструкциями по обработке звонка в Twilio. Например, возвращаемый ответ TwiML может содержать команду <Say>, которая задает голосовое воспроизведение текста вызываемому абоненту. Вместо предоставляемого Twilio URL-адреса вы можете построить собственную службу, отвечающую на запросы Twilio. Дополнительные сведения см. в разделе [Использование Twilio для поддержки голосовых возможностей и SMS в PHP][howto_twilio_voice_sms_php]. Дополнительные сведения о TwiML см. на странице [http://www.twilio.com/docs/api/twiml][twiml]. Дополнительные сведения о команде <Say> и других командах Twilio см. на странице [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Рекомендации по безопасности Twilio приведены на странице [https://www.twilio.com/docs/security][twilio_docs_security].

Дополнительные сведения о Twilio см. на странице [https://www.twilio.com/docs][twilio_docs].

## См. также
* [Использование Twilio для поддержки голосовых возможностей и SMS в PHP](../partner-twilio-php-how-to-use-voice-sms)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_php]: https://github.com/twilio/twilio-php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: ../partner-twilio-php-how-to-use-voice-sms
[howto_blob_storage_php]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/ru-ru/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-git/
[website-ftp]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-ftp/
[website-webmatrix]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-webmatrix/
[twilio_php_github]: https://github.com/twilio/twilio-php

<!--HONumber=35.2-->
