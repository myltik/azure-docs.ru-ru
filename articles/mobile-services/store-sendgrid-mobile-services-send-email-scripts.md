<properties 
	pageTitle="Отправка почты с помощью SendGrid - мобильные службы Azure" 
	description="Узнайте, как использовать службу SendGrid для отправки электронной почты из приложения мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="Erikre" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="Erikre"/>


# Отправить сообщение электронной почты с мобильных служб с помощью SendGrid

В этом разделе объясняется, как добавить в мобильную службу функции работы с электронной почтой. В этом учебнике вы добавляете сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid. По завершении работы ваша мобильная служба будет отправлять сообщение электронной почты каждый раз при вставке записи.

SendGrid — это [облачная служба электронной почты], которая предоставляет надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Дополнительные сведения см. на веб-сайте <http://sendgrid.com>.

В этом учебнике рассматриваются следующие основные шаги для включения функциональных возможностей электронной почты:

1. [Создание учетной записи SendGrid]
2. [Добавление скрипта для отправки электронной почты]
3. [Вставка данных для получения электронной почты]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами].

## <a name="sign-up"></a>Создание учетной записи SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>Регистрация нового скрипта для отправки электронной почты

1. Выполните вход на [портал управления Azure], щелкните по пункту **Мобильные службы**, а затем щелкните по своей мобильной службе.

2. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

	![][1]

3. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.
   
	![][2]

	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

4. Замените функцию вставки следующим кодом:

        var SendGrid = require('sendgrid').SendGrid;
        
        function insert(item, user, request) {    
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');       
                
                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. Замените заполнители в приведенном выше сценарии на правильные значения:

	- **_username_ и _password_**: учетные данные SendGrid, которые вы задаете в окне [Создание учетной записи SendGrid].

	- **_email-address_**: адрес, по которому отправляются сообщения электронной почты. В реальном приложении для хранения и извлечения адресов электронной почты можно использовать таблицы. При тестировании приложения просто используйте свой адрес электронной почты.

	- **_from-address_**: адрес, с которого отправляются сообщения электронной почты. Можно использовать адрес зарегистрированного домена, который принадлежит вашей организации.

     >[AZURE.NOTE]Если зарегистрированного домена нет, вы можете использовать домен своей мобильной службы в формате *notifications@_your-mobile-service_.azure-mobile.net*. Однако сообщения, отправленные на домен вашей мобильной службы, будут игнорироваться.

6. Нажмите кнопку **Сохранить**. Теперь вы настроили скрипт для отправки сообщения электронной почты каждый раз при вставке записи в таблицу **TodoItem**.

## <a name="insert-data"></a>Вставка тестовых данных для получения электронной почты

1. В проекте клиентского приложения выполните приложение быстрого запуска. 

	В этом разделе показана версия быстрого запуска для Магазина Windows

2. В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

	![][3]

3. Обратите внимание на получение электронного сообщения, которое показано в уведомлении ниже.

	![][4]

	Поздравляем, вы успешно настроили мобильные службы для отправки сообщения электронной почты с помощью SendGrid.

## <a name="nextsteps"> </a>Дальнейшие действия

Теперь, когда вы узнали, насколько просто пользоваться службой электронной почты SendGrid с мобильными службами, воспользуйтесь следующими ссылками, чтобы получить более подробные сведения о SendGrid.

-   Документация по интерфейсу API SendGrid: <https://sendgrid.com/docs>
-   Специальное предложение SendGrid для клиентов Azure: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Создание учетной записи SendGrid]: #sign-up
[Добавление скрипта для отправки электронной почты]: #add-script
[Вставка данных для получения электронной почты]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[портал управления Azure]: https://manage.windowsazure.com/
[облачная служба электронной почты]: https://sendgrid.com/email-solutions
[доставки электронной почты]: https://sendgrid.com/transactional-email

 

<!---HONumber=July15_HO5-->