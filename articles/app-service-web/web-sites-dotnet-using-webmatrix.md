<properties 
	pageTitle="Развертывание и разработка веб-приложения с помощью Microsoft WebMatrix" 
	description="Узнайте, как разрабатывать и развертывать веб-приложения ASP.NET в веб-приложения службы приложений Azure с помощью Microsoft WebMatrix." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="tomfitz"/>


# Развертывание и разработка веб-приложения с помощью Microsoft WebMatrix

## Обзор

В этом руководстве описывается использование Microsoft WebMatrix для создания и развертывания веб-сайта .NET в веб-приложениях [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Будет использоваться пример приложения из шаблона сайта WebMatrix.

Вы узнаете:

* Как войти в Azure с помощью WebMatrix.
* Как создать сайт, используя встроенный шаблон, с помощью WebMatrix. 
* Как выполнить развертывание настраиваемого веб-сайта непосредственно из WebMatrix в Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Вход в Azure

1. Запуск WebMatrix.
2. Если это первый случай использования WebMatrix 3, будет выведен запрос на вход в Azure. В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись** Выберите **Вход** с использованием учетной записи Майкрософт.

	![Добавление учетной записи][addaccount]

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

	![Вход в Azure][signin]


## Создание сайта с помощью встроенного шаблона для Azure

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов.

	![Новый сайт из коллекции шаблонов][sitefromtemplate]

2. В коллекции шаблонов отображается список доступных шаблонов, которые можно запустить локально или в Azure. Выберите в списке шаблонов пункт **Пекарня**, введите **bakerysample** в качестве **имени веб-сайта** и нажмите кнопку **Далее**.

	![Создание веб-сайта на основе шаблона][sitefromtemplatedetails]

3. После входа в Azure можно будет создать экземпляр веб-приложений службы приложений Azure для локального сайта. Выберите уникальное имя и центр обработки данных, где планируется создать экземпляр веб-приложений:

	![Создание сайта в Azure][sitefromtemplateazure]

	После того как WebMatrix завершит создавать локальный сайт и экземпляр веб-приложений в Azure, откроется интегрированная среда разработки WebMatrix.

	![Интегрированная среда разработки WebMatrix][howtowebmatrixide]

## Настройка электронной почты

Пример с пекарней включает модель формы заказа, которая отправляет сообщение электронной почты с заказанной номенклатурой. Для отправки сообщений электронной почты с вашего веб-сайта в Azure будет использоваться служба электронной почты SendGrid.

1. Следуйте инструкциям, приведенным в учебнике [Отправка электронной почты с помощью SendGrid в Azure][sendgridexample] по настройке учетной записи SendGrid и получению сведений о подключении. Нет необходимости выполнять все инструкции из учебника. Выполните только те инструкции, которые касаются получения сведений о подключении.

2. Добавьте пакет SendGrid NuGet в проект WebMatrix. Во-первых, нажмите кнопку NuGet.

    ![Добавление SendGrid][addsendgrid]

    Выполните поиск по SendGrid и установите пакет для него.

    ![Установка SendGrid][installsendgrid]

    После завершения установки пакета сборки SendGrid будут добавлены в папку bin.

    ![Добавлен SendGrid][binsendgrid]

3. Откройте страницу *Order.cshtml*, дважды щелкнув имя файла.

	![][modify2]

4. В начале файла добавьте следующий код:

        @using SendGrid;
        @using System.Net.Mail;

4. Найдите комментарий "//SMTP Configuration for Hotmail" и удалите или закомментируйте весь код для использования WebMail.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Добавьте код для использования SendGrid вместо WebMail для отправки сообщений электронной почты. Добавьте следующий код вместо кода, который был удален на предыдущем шаге. При создании объекта NetworkCredential укажите свое имя пользователя SendGrid и пароль.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name]", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.DeliverAsync(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. На ленте WebMatrix нажмите кнопку **Запуск**, чтобы протестировать сайт.

	![][modify4]

7. Нажмите кнопку **Заказать** на одном из продуктов и отправьте заказ самому себе.

8. Проверьте свою электронную почту и убедитесь, что подтверждение заказа пришло. При возникновении проблем с отправкой электронной почты см. раздел [Проблемы при отправке электронной почты][sendmailissues] в "Руководстве по устранению неполадок веб-страниц ASP.NET (Razor)".
 

## Развертывание настраиваемого веб-сайта из WebMatrix в Azure

1. В WebMatrix нажмите кнопку **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для веб-сайта.

	![Просмотр публикации WebMatrix][howtopublishpreview]

2. Установите флажок рядом с файлом bakery.sdf и нажмите кнопку **Продолжить**. После завершения публикации URL-адрес обновленного веб-приложения в службе приложений Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix.

	![Публикация завершена][publishcomplete]

3. Щелкните ссылку, чтобы открыть веб-сайт (экземпляр веб-приложений в Azure) в браузере:

	![Пример сайта "Пекарня"][bakerysample]

	URL-адрес экземпляра веб-приложений также можно найти на [портале Azure](https://portal.azure.com), щелкнув **Обзор** > **Веб-приложения** для отображения всех экземпляров веб-приложений для подписки, а затем выбрав веб-приложение. URL-адрес для веб-приложения отображается в колонке веб-приложения.

## Изменение веб-сайта и его повторная публикация в веб-приложениях

С помощью WebMatrix можно изменить веб-сайт и повторно опубликовать его в своем экземпляре веб-приложений. В следующей процедуре вам предстоит добавить флажок, чтобы указать, что это заказ подарка.

1. Откройте страницу *Order.cshtml*.

2. Найдите определение формы класса shiping. Вставьте следующий код сразу после блока &lt;li&gt;
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Найдите в файле строку "var shipping = Request["orderShipping"];" и вставьте следующую строку кода непосредственно за ней.

		var gift = Request["isGift"];

4. Сразу после блока кода, который проверяет, что адрес доставки не пуст, в следующем фрагменте кода.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Файл *order.cshtml* должен выглядеть, как на рисунке ниже.

	![][modify6]

5. Сохраните файл, запустите сайт локально и отправьте себе тестовый заказ. Убедитесь, что проверяется флажок создания.

	![][modify7]

6. Повторно опубликуйте сайт, нажав кнопку **Публикация** на ленте **Главная**.

7. В диалоговом окне **Просмотр публикации** убедитесь, что отмечены оба файла Order.cshtml, а затем нажмите кнопку "Продолжить".

8. Щелкните ссылку, чтобы открыть веб-сайт в браузере и протестировать обновление в экземпляре веб-приложений.

## Дальнейшие действия

* [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).




[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/
 

<!---HONumber=July15_HO4-->