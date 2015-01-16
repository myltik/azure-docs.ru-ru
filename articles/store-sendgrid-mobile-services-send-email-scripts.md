<properties urlDisplayName="Send Email Using SendGrid" pageTitle="Отправка электронной почты с помощью SendGrid - мобильные службы Azure" metaKeywords="Azure SendGrid, служба SendGrid, электронная почта в Azure, мобильные службы электронной почты" description="Узнайте, как использовать службу SendGrid для отправки электронной почты из приложения мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/27/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />


# Отправить сообщение электронной почты с мобильных служб с помощью SendGrid

В этом разделе объясняется, как добавить в мобильную службу функции работы с электронной почтой. В этом учебнике вы добавляете сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid. После завершения учебника каждый раз при вставке записи мобильная служба будет отправлять сообщение электронной почты.

SendGrid - это [облачная служба электронной почты], обеспечивающая надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Дополнительные сведения см. на веб-сайте <http://sendgrid.com>.

В этом учебнике рассматриваются следующие основные шаги для включения функциональных возможностей электронной почты:

1. [Создание учетной записи SendGrid]
2. [Добавление скрипта для отправки сообщения электронной почты]
3. [Вставка данных для получения сообщения электронной почты]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

<h2><a name="sign-up"></a>Создайте новую учетную запись SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a>Зарегистрируйте новый скрипт, который отправляет электронную почту</h2>

1. Выполните вход на [Портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

2. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

	![][1]

3. В **TodoItem** выберите вкладку **Скрипт**, после чего выберите **Вставить**.
   
	![][2]

	При этом будет показана функция, вызываемая при выполнении вставки в таблицу **TodoItem**.

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

	- **_username_ and _password_**: учетные данные SendGrid, указанные в окне [Создание учетной записи SendGrid].

	- **_email-address_**: адрес, на который отправляется электронное сообщение. В реальном приложении для хранения и извлечения адресов электронной почты можно использовать таблицы. При тестировании приложения просто используйте свой адрес электронной почты.

	- **_from-address_**: адрес, с которого отправляется электронное сообщение. Можно использовать адрес зарегистрированного домена, который принадлежит вашей организации. 

     <div class="dev-callout"><b>Примечание.</b>
     <p>Если зарегистрированного домена нет, можно использовать домен мобильной службы в формате <strong>notifications@<i>ваша_мобильная_служба</i>.azure-mobile.net</strong> Тем не менее сообщения, отправленные на ваш домен мобильных служб, игнорируются.</p>
    </div> 

6. Нажмите кнопку **Сохранить**. Вы только что настроили скрипт для отправки сообщения электронной почты при каждой вставке записи в таблицу**TodoItem**.

<h2><a name="insert-data"></a>Вставьте тестовые данные для получения письма электронной почты</h2>

1. В проекте клиентского приложения выполните приложение быстрого запуска. 

	В этом разделе показана версия быстрого запуска для Магазина Windows

2. В приложении введите текст в поле **Вставка TodoItem**, а затем нажмите кнопку **Сохранить**.

	![][3]

3. Обратите внимание на получение сообщения электронной почты, которое указано в отобразившемся ниже уведомлении. 

	![][4]

	Поздравляем, вы успешно настроили мобильные службы для отправки сообщения электронной почты с помощью SendGrid.

## <a name="nextsteps"> </a>Дальнейшие действия

Теперь, когда вы узнали, насколько просто пользоваться службой электронной почты SendGrid с мобильными службами, воспользуйтесь следующими ссылками, чтобы получить более подробные сведения о SendGrid.

-   Документация по интерфейсу SendGrid API:
    <https://sendgrid.com/docs>
-   Специальное предложение SendGrid для клиентов Azure:
    <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Создание учетной записи SendGrid]: #sign-up
[Добавление скрипта для отправки сообщения электронной почты]: #add-script
[Вставка данных для получения сообщения электронной почты]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[страница регистрации]: https://sendgrid.com/windowsazure.html
[Страница учетных данных нескольких пользователей]: https://sendgrid.com/credentials
[Портал управления Azure]: https://manage.windowsazure.com/
[облачная служба электронной почты]: https://sendgrid.com/email-solutions
[доставка электронной почты]: https://sendgrid.com/transactional-email

