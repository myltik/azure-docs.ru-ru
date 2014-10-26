<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Send Email Using SendGrid" pageTitle="Send email using SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"></tags>

# Отправить сообщение электронной почты с мобильных служб с помощью SendGrid

В этом разделе показано, как можно добавить функциональную возможность электронной почты в вашу мобильную службу. В этом учебнике вы добавляете сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid. По завершении работы ваша мобильная служба будет отправлять сообщение электронной почты каждый раз при вставке записи.

SendGrid — это [облачная служба электронной почты][облачная служба электронной почты], которая предоставляет надежные возможности [доставки электронной почты][доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Дополнительные сведения см. на веб-сайте <http://sendgrid.com>.

В этом учебнике рассматриваются следующие основные шаги для включения функциональных возможностей электронной почты:

1.  [Создание учетной записи SendGrid][Создание учетной записи SendGrid]
2.  [Добавление скрипта для отправки сообщения электронной почты][Добавление скрипта для отправки сообщения электронной почты]
3.  [Вставка данных для получения сообщения электронной почты][Вставка данных для получения сообщения электронной почты]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <a name="sign-up"></a><span class="short-header">Создание новой учетной записи</span>Создание новой учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up][sendgrid-sign-up]]

## <a name="add-script"></a><span class="short-header">Регистрация сценария</span>Регистрация нового сценария, отправляющего электронные сообщения

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу.

2.  На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][]

3.  В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][1]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

4.  Замените функцию вставки следующим кодом:

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

5.  Замените заполнители в приведенном выше сценарии на правильные значения:

    -   ***username* и *password***: учетные данные SendGrid, указанные в окне [Создание учетной записи SendGrid][Создание учетной записи SendGrid].

    -   ***email-address***: адрес, на который отправляется электронное сообщение. В реальном приложении для хранения и извлечения адресов электронной почты можно использовать таблицы. При тестировании приложения просто используйте свой собственный адрес электронной почты.

    -   ***from-address***: адрес, с которого отправляется электронное сообщение. Можно использовать адрес зарегистрированного домена, который принадлежит вашей организации.

     <div class="dev-callout"><b>Примечание.</b><br /> <p>Если зарегистрированного домена нет, вы можете использовать домен своей мобильной службы в формате <strong>notifications@<i>ваша_мобильная_служба</i>.azure-mobile.net</strong>. Однако сообщения, отправленные на домен вашей мобильной службы, будут игнорироваться.</p><br /></div></p>
6.  Нажмите кнопку **Сохранить**. Теперь вы настроили скрипт для отправки сообщения электронной почты каждый раз при вставке записи в таблицу **TodoItem**.

## <a name="insert-data"></a><span class="short-header">Вставка тестовых данных</span>Вставка тестовых данных для получения электронных сообщений

1.  В проекте клиентского приложения выполните приложение быстрого запуска.

    В этом разделе показана версия быстрого запуска для Магазина Windows

2.  В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

    ![][2]

3.  Обратите внимание на получение электронного сообщения, которое показано в уведомлении ниже.

    ![][3]

    Поздравляем, вы успешно настроили мобильные службы для отправки сообщения электронной почты с помощью SendGrid.

## <a name="nextsteps"> </a> Дальнейшие действия

Теперь, когда вы узнали, насколько простым может быть использование службы электронной почты SendGrid с мобильными службами, воспользуйтесь следующими ссылками, чтобы узнать больше о SendGrid.

-   Документация по интерфейсу API SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Специальное предложение SendGrid для клиентов Azure:
    <http://sendgrid.com/azure.html>

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставки электронной почты]: http://sendgrid.com/transactional-email
  [Создание учетной записи SendGrid]: #sign-up
  [Добавление скрипта для отправки сообщения электронной почты]: #add-script
  [Вставка данных для получения сообщения электронной почты]: #insert-data
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
  [1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
  [2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
  [3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png
