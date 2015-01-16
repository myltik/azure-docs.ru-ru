<properties urlDisplayName="Website with WebMatrix" pageTitle="Веб-сайт .NET с WebMatrix - учебные курсы Azure" metaKeywords ="WebMatrix Azure, WebMatrix Azure, веб-сайт Azure WebMatrix, вебсайт Azure WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Узнайте, как разработать и развернуть веб-сайт Azure с помощью WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/27/2014" ms.author="tomfitz" />





#Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix
В этом руководстве описывается использование Microsoft WebMatrix для создания и развертывания веб-сайта в Azure.  Будет использоваться пример приложения из шаблона сайта WebMatrix.

Вы узнаете:

* Как войти в Azure с помощью WebMatrix.
* Как создать сайт, используя встроенный шаблон, с помощью WebMatrix. 
* Как выполнить развертывание настраиваемого веб-сайта непосредственно из WebMatrix в Azure.


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Вход в Azure

1. Запуск WebMatrix.
2. Если это первый случай использования WebMatrix 3, будет выведен запрос на вход в Azure.  В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись**.  Выберите **Вход** с использованием учетной записи Майкрософт.

	![Add Account][addaccount]

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

	![Sign into Azure][signin]	


## Создание сайта с помощью встроенного шаблона для Azure

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов:

	![New site from Template Gallery][sitefromtemplate]

2. В коллекции шаблонов отображается список доступных шаблонов, которые можно запустить локально или в Azure.  Выберите в списке шаблонов пункт **Пекарня**, введите **bakerysample** в качестве **имени веб-сайта** и нажмите кнопку **Далее**.

	![Create Site from Template][sitefromtemplatedetails]

3. После входа в Azure вы можете создать веб-сайт Azure для локального сайта.  Выберите уникальное имя и выберите центр обработки данных, где планируется создать сайт: 

	![Create site on Azure][sitefromtemplateazure]

	По завершении создания веб-сайта WebMatrix откроется интегрированная среда разработки WebMatrix.

	![WebMatrix IDE][howtowebmatrixide] 

## Настройка электронной почты

Пример с пекарней включает форму условного заказа, которая отправляет сообщение электронной почты с заказанным товаром. Для отправки писем с веб-сайта будет использоваться служба электронной почты SendGrid в Azure.

1. Следуйте инструкциям учебника [Отправка электронной почты с помощью SendGrid в Azure][sendgridexample] по настройке учетной записи SendGrid и получению сведений о подключении. Нет необходимости проходить весь учебник - только до момента получения сведений о подключении.

2. Добавление в проект WebMatrix пакета NuGet SendGrid. Нажмите кнопку NuGet.

    ![Add SendGrid][addsendgrid]

    Выполните поиск по SendGrid и установите пакет для него.

    ![Install SendGrid][installsendgrid]

    После завершения установки пакета сборки SendGrid будут добавлены в папку bin.

    ![SendGrid added][binsendgrid]

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


5. Добавьте код для отправки сообщений электронной почты с помощью SendGrid вместо WebMail. Добавьте следующий код вместо кода, который был удален на предыдущем шаге.

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
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. На ленте WebMatrix нажмите кнопку **Запуск**, чтобы протестировать сайт.

	![][modify4]

7. Нажмите кнопку **Заказать** для одного из продуктов и отправьте заказ самому себе.

8. Проверьте свою электронную почту и убедитесь, что получено подтверждение заказа. При возникновении проблем с отправкой электронной почты см. раздел [Проблемы при отправке электронной почты][sendmailissues] в "Руководстве по устранению неполадок веб-страниц ASP.NET (Razor)".
 

## Развертывание настраиваемого веб-сайта из WebMatrix в Azure

1. В WebMatrix нажмите кнопку **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для веб-сайта.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Установите флажок рядом с файлом bakery.sdf и нажмите кнопку **Продолжить**.  После завершения публикации URL-адрес обновленного веб-сайта на платформе Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix.  

	![Publishing Complete][publishcomplete]

3. Щелкните по ссылке, чтобы открыть веб-сайт в браузере.

	![Bakery Sample Site][bakerysample]

	URL-адрес веб-сайта можно также найти на портале Azure, щелкнув **Веб-сайты** для отображения всех веб-сайтов в вашей подписке. URL-адрес для каждого веб-сайта отображается в столбце URL-адреса на странице веб-сайтов.

## Изменение веб-сайта и его повторная публикация на веб-сайте Azure

С помощью WebMatrix можно изменить веб-сайт и повторно опубликовать его на своем веб-сайте Azure. В следующей процедуре вам предстоит добавить флажок, обозначающий заказ как подарок.

1. Откройте страницу *Order.cshtml*.

2. Найдите определение формы класса "shiping". Вставьте следующий код сразу после блока &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Найдите в файле строку "var shipping = Request["orderShipping"];" и вставьте следующую строку кода непосредственно после нее.

		var gift = Request["isGift"];

4. Сразу после блока кода, который проверяет, что адрес доставки не пуст, в следующем фрагменте кода.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Файл *order.cshtml* должен выглядеть так, как показано на рисунке ниже.

	![][modify6]

5. Сохраните файл, запустите сайт локально и отправьте себе тестовый заказ. Проверьте работу нового флажка.

	![][modify7]

6. Повторно опубликуйте сайт: нажмите кнопку **Публикация** на ленте **Главная**.

7. В диалоговом окне **Просмотр публикации** убедитесь, что отмечены оба файла Order.cshtml, а затем нажмите кнопку "Продолжить".

8. Щелкните по ссылке, чтобы открыть веб-сайт в браузере и протестировать обновление на веб-сайте Azure.

# Дальнейшие действия

Вы узнали, как создать и развернуть веб-сайт из WebMatrix в Azure. Для получения дополнительных сведений о WebMatrix посетите перечисленные ниже ресурсы.

* [WebMatrix для Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





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
[sendgridexample]: http://azure.microsoft.com/ru-ru/documentation/articles/sendgrid-dotnet-how-to-send-email/
