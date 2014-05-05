<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Отправка сообщения электронной почты с помощью SendGrid" pageTitle="Отправка сообщения электронной почты с помощью SendGrid — мобильные службы Azure" metaKeywords="Azure SendGrid, служба SendGrid, отправка по электронной почте в Azure, мобильные службы электронной почты" description="Узнайте, как использовать службу SendGrid для отправки электронной почты из приложения мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Отправка электронной почты с мобильных служб с помощью SendGrid" authors="" solutions="" manager="" editor="" />




# Отправка электронной почты с мобильных служб с помощью SendGrid

В этом разделе показано, как можно добавить функциональную возможность электронной почты в вашу мобильную службу. В этом учебнике вы добавляете сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid. По завершении работы ваша мобильная служба будет отправлять сообщение электронной почты каждый раз при вставке записи.

SendGrid — это [облачная служба электронной почты], которая предоставляет надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Дополнительные сведения см. в разделе <http://sendgrid.com>.

В этом учебнике рассматриваются следующие основные шаги для включения функциональных возможностей электронной почты:

1. [Создание учетной записи SendGrid]
2. [Добавление скрипта для отправки сообщения электронной почты]
3. [Вставка данных для получения сообщения электронной почты]

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

<h2><a name="sign-up"></a><span class="short-header">Создание новой учетной записи</span>Создание новой учетной записи SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a><span class="short-header">Регистрация скрипта</span>Регистрация нового скрипта, отправляющего сообщения электронной почты</h2>

1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

2. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

	![][1]

3. В **todoitem** откройте вкладку **Скрипт** и выберите **Вставка**.
   
	![][2]

	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

4. Замените функцию insert следующим кодом:

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

5. Замените заполнители в приведенном выше скрипте на правильные значения:

	- **_username_ и _password_**: учетные данные SendGrid, которые вы задаете в окне [Создание учетной записи SendGrid].

	- **_email-address_**: адрес, по которому отправляется сообщение электронной почты. В реальном приложении для хранения и извлечения адресов электронной почты можно использовать таблицы. При тестировании приложения просто используйте свой собственный адрес электронной почты.

	- **_from-address_**: адрес, с которого отправляется сообщение. Можно использовать адрес зарегистрированного домена, который принадлежит вашей организации. 

     <div class="dev-callout"><b>Примечание.</b>
     <p>Если зарегистрированного домена нет, можно использовать домен мобильной службы в формате <strong>notifications@<i>ваша_мобильная_служба</i>.azure-mobile.net</strong> Тем не менее сообщения, отправленные на ваш домен мобильных служб, игнорируются.</p>
    </div> 

6. Нажмите кнопку **Сохранить**. Теперь вы настроили скрипт для отправки сообщения электронной почты каждый раз при вставке записи в таблицу **TodoItem**.

<h2><a name="insert-data"></a><span class="short-header">Вставка тестовых данных</span>Вставка тестовых данных для получения сообщения электронной почты</h2>

1. В проекте клиентского приложения выполните приложение быстрого запуска. 

	В этом разделе показана версия быстрого запуска для Магазина Windows

2. В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

	![][3]

3. Обратите внимание на получение сообщения электронной почты, которое указано в отобразившемся ниже уведомлении. 

	![][4]

	Поздравляем, вы успешно настроили мобильные службы для отправки сообщения электронной почты с помощью SendGrid.

## <a name="nextsteps"> </a>Дальнейшие действия

Теперь, когда вы знаете, насколько просто можно использовать службу электронной почты SendGrid с мобильными службами, перейдите по
следующим ссылкам, чтобы узнать больше о службе SendGrid.

-   Документация по интерфейсу SendGrid API:
    <http://docs.sendgrid.com/documentation/api/>
-   Специальное предложение SendGrid для клиентов Azure:
    <http://sendgrid.com/azure.html>

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
[Страница регистрации]: http://sendgrid.com/azure.html
[Страница "Учетные данные нескольких пользователей"]: http://sendgrid.com/credentials
[Портал управления Azure]: https://manage.windowsazure.com/
[Облачная служба электронной почты]: http://sendgrid.com/solutions
[доставки электронной почты]: http://sendgrid.com/transactional-email


