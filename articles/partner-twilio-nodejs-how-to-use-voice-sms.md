<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Использование Twilio для голосовой связи, VoIP и SMS сообщений в Azure

В этом руководстве демонстрируется создание приложений, взаимодействующих с Twilio и node.js в Azure.

## Оглавление

-   [Что такое Twilio?][Что такое Twilio?]
-   [Подпишитесь на Twilio (скидка Microsoft)][Подпишитесь на Twilio (скидка Microsoft)]
-   [Создание и развертывание веб-сайта node.js][Создание и развертывание веб-сайта node.js]
-   [Настройка модуля Twilio][Настройка модуля Twilio]
-   [Осуществление исходящего вызова][Осуществление исходящего вызова]
-   [Отправка SMS-сообщения][Отправка SMS-сообщения]
-   [Дальнейшие действия][Дальнейшие действия]

<span id="whatis"></span></a>

## Что такое Twilio?

Twilio представляет собой платформу API, облегчающую разработчикам процессы осуществления и приема вызовов, отправки и получения текстовых сообщений и внедрения функций VoIP-вызовов в приложения на основе браузеров и в собственные приложения. Сначала вкратце рассмотрим принцип работы.

### Прием вызовов и получение текстовых сообщений

Twilio позволяет разработчикам [покупать программируемые телефонные номера][покупать программируемые телефонные номера], которые можно использовать для осуществления и приема вызовов, а также для отправки и получения текстовых сообщений. При получении на номер Twilio входящего вызова или текстового сообщения Twilio отправит на веб-приложение запрос HTTP POST или GET на получение инструкций по обработке вызова или текстового сообщения. Сервер отвечает на HTTP-запрос от Twilio с использованием [TwiML][TwiML], простого набора XML-тегов, которые содержат инструкции по обработке вызова или текстового сообщения. Чуть позже мы увидим примеры TwiML.

### Осуществление вызовов и отправка текстовых сообщений

Выполняя HTTP-запросы к API веб-службы Twilio, разработчики могут отправлять текстовые сообщения или совершать исходящие вызовы. Для исходящих вызовов разработчику необходимо также указать адрес URL, который возвращает инструкции TwiML по обработке исходящего вызова после его подключения.

### Внедрение возможностей VoIP в код пользовательского интерфейса (JavaScript, iOS и Android)

Twilio предоставляет клиентский пакет SDK, который может превратить в VoIP-телефон любой веб-браузер, приложение iOS или Android. В данной статье основное внимание уделяется использованию VoIP-вызова в веб-браузере. В дополнение к SDK Twilio JavaScript, исполняемому в обозревателе, должно использоваться серверное приложение (приложение node.js) для выдачи маркера "Возможность" для клиента JavaScript. Более подробные сведения об использовании VoIP с node.js [см. в блогах разработчиков Twilio][см. в блогах разработчиков Twilio].

<span id="signup"></span></a>

## Подпишитесь на Twilio (скидка Microsoft)

Перед использованием службы Twilio необходимо сначала [зарегистрироваться для получения учетной записи][зарегистрироваться для получения учетной записи]. Клиенты Microsoft Azure получают специальную скидку — [не забудьте зарегистрироваться здесь][зарегистрироваться для получения учетной записи]!

<span id="azuresite"></span></a>

## Создание и развертывание веб-сайта node.js

Затем необходимо создать на Azure веб-сайт node.js. [Официальная документация с описанием этого процесса находится здесь][Официальная документация с описанием этого процесса находится здесь]. В общих чертах будут выполняться следующие действия:

-   Регистрация для получения учетной записи Azure в случае ее отсутствия
-   Создание нового веб-сайта с помощью консоли администрирования Azure
-   Добавление поддержки системы управления версиями (подразумевается, что используется git)
-   Создание файла `server.js` с простым веб-приложением node.js
-   Развертывание простого приложения в Azure

<span id="twiliomodule"></span></a>

## Настройка модуля Twilio

Теперь начнем писать простое приложение node.js, которое использует интерфейс API Twilio. Прежде чем начать, необходимо настроить учетные данные учетной записи Twilio.

### Настройка учетных данных Twilio в системных переменных среды

Для выполнения проверенных запросов к серверной части Twilio понадобится SID учетной записи и маркер проверки подлинности, которые будут использоваться как имя пользователя и пароль для учетной записи Twilio. Наиболее безопасный способ их настройки для использования с модулем узла в Azure – через системные переменные среды, которые можно установить непосредственно в консоли администрирования Azure.

Выберите веб-сайт node.js и щелкните ссылку "НАСТРОИТЬ". Если немного прокрутить экран, появится область, где можно задать свойства конфигурации для приложения. Введите учетные данные учетной записи Twilio ([их можно найти на панели мониторинга Twilio][их можно найти на панели мониторинга Twilio]), как показано; убедитесь, что используется имя "TWILIOACCOUNTSID" и "TWILIOAUTHTOKEN" соответственно:

![Консоль администрирования Azure][Консоль администрирования Azure]

После настройки этих переменных перезапустите приложение на консоли Azure.

### Объявление модуля Twilio в package.json

Далее нам нужно создать package.json для управления зависимостями модуля узла через [npm][npm]. На том же уровне, что и файл "server.js", созданный в учебнике Azure/node.js, создайте файл с именем "package.json". Содержимое файла должно быть следующим:

	{
	 "name": "имя\_приложения",
	 "version": "0.0.1",
	 "private": true,
	 "scripts": {
	 "start": "node server"
	 },
	 "dependencies": {
	 "express": "3.1.0",
	 "ejs": "*",
	 "twilio":"*"
	 }
	 }

Этот код объявляет модуль twilio как зависимость, а также популярную [веб-платформу express][веб-платформу express] и подсистему шаблонов EJS. Итак, теперь все готово. Давайте напишем код!

<span id="makecall"></span></a>

## Осуществление исходящего вызова

Давайте создадим простую форму, которая будет осуществлять вызов на выбранный номер. Откройте server.js и введите следующий код. Обратите внимание на поле "CHANGE\_ME", в котором нужно указать имя веб-сайта Azure:

	// Зависимости модулей
	 var express = require('express'),
	 path = require('path'),
	 http = require('http'),
	 twilio = require('twilio');
	
	// Создание веб-приложения Express
	 var app = express();
	
	// Настройка Express
	 app.configure(function(){
	 app.set('port', process.env.PORT || 3000);
	 app.set('views', \_\_dirname + '/views');
	 app.set('view engine', 'ejs');
	 app.use(express.favicon());
	 app.use(express.logger('dev'));
	 app.use(express.bodyParser());
	 app.use(express.methodOverride());
	 app.use(app.router);
	 app.use(express.static(path.join(\_\_dirname, 'public')));
	 });
	 app.configure('development', function(){
	 app.use(express.errorHandler());
	 });
	
	// Формирование пользовательского интерфейса HTML для домашней страницы приложения
	 app.get('/', function(request, response) {
	 response.render('index');
	 });
	
	// Обработка запроса формы POST для совершения вызова
	 app.post('/call', function(request, response) {
	 var client = twilio();
	 client.makeCall({
	 // звоним на этот номер
	 to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

	});
	
	// Создание TwiML для обработки исходящего вызова
	 app.post('/outbound\_call', function(request, response) {
	 var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());

	});
	
	// Запуск сервера
	 http.createServer(app).listen(app.get('port'), function(){
	 console.log("Express server listening on port " + app.get('port'));
	 });

Затем в этом каталоге создайте каталог с именем "views" и создайте файл с именем "index.ejs", содержащий следующий код:

	\<!DOCTYPE html\>
	 \<html\>
	 \<head\>
	 \<title\>Проверка Twilio\</title\>
	 \<style\>
	 input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
	 \</style\>
	 \</head\>
	 \<body\>
	 \<h1\>Проверка Twilio\</h1\>
	 \<form action="/call" method="POST"\>
	 \<input placeholder="Введите номер телефона" name="number"/\>
	 \<br/\>
	 \<input type="submit" value="Позвонить по указанному номеру"/\>
	 \</form\>
	 \</body\>
	 \</html\>

Теперь разверните веб-сайт в Azure и откройте домашнюю страницу. Теперь вы можете ввести номер телефона в текстовом поле и получить вызов с вашего номера Twilio.

<span id="sendmessage"></span></a>

## Отправка SMS-сообщения

Теперь попробуем настроить пользовательский интерфейс и логику обработки формы для отправки текстовых сообщений. Откройте "server.js" и добавьте следующий код после последнего вызова "app.post":

	app.post('/sms', function(request, response) {
	 var client = twilio();
	 client.sendSms({
	 // отправляем текстовое сообщение на этот номер телефона
	 to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

	

В "views/index.ejs" добавьте еще одну форму для указания номера и текстового сообщения:

	\<form action="/sms" method="POST"\>
	 \<input placeholder="Введите номер телефона" name="number"/\>
	 \<br/\>
	 \<input placeholder="Введите сообщение для отправки" name="message"/\>
	 \<br/\>
	 \<input type="submit" value="Отправить сообщение на указанный номер"/\>
	 \</form\>

Повторно разверните приложение в Azure; теперь вы можете отправить форму, а также отправлять себе (или своим близким друзьям) текстовые сообщения.

<span id="nextsteps"></span></a>

## Дальнейшие действия

Вы узнали основные сведения об использовании node.js и Twilio для создания приложений, поддерживающих связь. Но эти примеры открывают лишь некоторые возможности Twilio и node.js. Для получения дополнительных сведений об использовании Twilio с node.js обратитесь к следующим ресурсам:

-   [Официальные документы по модулю][Официальные документы по модулю]
-   [Учебник по использованию VoIP с приложениями node.js][см. в блогах разработчиков Twilio]
-   [Votr приложение для SMS-голосования в реальном времени с node.js и CouchDB (три части)][Votr приложение для SMS-голосования в реальном времени с node.js и CouchDB (три части)]
-   [Парное программирование в браузере с использованием node.js][Парное программирование в браузере с использованием node.js]

Надеемся, вам понравиться работать с node.js и Twilio в Azure!

  [Что такое Twilio?]: #whatis
  [Подпишитесь на Twilio (скидка Microsoft)]: #signup
  [Создание и развертывание веб-сайта node.js]: #azuresite
  [Настройка модуля Twilio]: #twiliomodule
  [Осуществление исходящего вызова]: #makecall
  [Отправка SMS-сообщения]: #sendmessage
  [Дальнейшие действия]: #nextsteps
  [покупать программируемые телефонные номера]: https://www.twilio.com/user/account/phone-numbers/available/local
  [TwiML]: https://www.twilio.com/docs/api/twiml
  [см. в блогах разработчиков Twilio]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
  [зарегистрироваться для получения учетной записи]: http://ahoy.twilio.com/azure
  [Официальная документация с описанием этого процесса находится здесь]: http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [их можно найти на панели мониторинга Twilio]: https://www.twilio.com/user/account
  [Консоль администрирования Azure]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
  [npm]: http://npmjs.org
  [веб-платформу express]: http://expressjs.com
  [Официальные документы по модулю]: http://twilio.github.io/twilio-node/
  [Votr приложение для SMS-голосования в реальном времени с node.js и CouchDB (три части)]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
  [Парное программирование в браузере с использованием node.js]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
