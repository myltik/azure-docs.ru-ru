<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Веб-сайт с WebMatrix" pageTitle="Веб-сайт .NET с помощью WebMatrix — учебные курсы Azure" metaKeywords="WebMatrix Azure, WebMatrix Azure, веб-сайт Azure с WebMatrix, Azure — веб-сайт WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Узнайте, как разрабатывать и развертывать веб-сайт Azure с помощью WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix" authors="" solutions="" manager="" editor="" />





#Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix
В этом руководстве описывается использование Microsoft WebMatrix для создания и развертывания веб-сайта в Azure.  Будет использоваться пример приложения из шаблона сайта WebMatrix.

Вы узнаете:

* Как войти в Azure с помощью WebMatrix
* Как создать сайт, используя встроенный шаблон, с помощью WebMatrix 
* Как выполнить развертывание настраиваемого веб-сайта непосредственно из WebMatrix в Azure.


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Вход в Azure

1. Запуск WebMatrix.
2. Если это первый случай использования WebMatrix 3, будет выведен запрос на вход в Azure.  В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись**  Выберите **Вход** с использованием учетной записи Майкрософт.

	![Добавление учетной записи][addaccount]

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт:

	![Вход в Azure][signin]	


## Создание сайта с помощью встроенного шаблона для Azure

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов:

	![Новый сайт из коллекции шаблонов][sitefromtemplate]

2. В коллекции шаблонов отображается список доступных шаблонов, которые можно запустить локально или в Azure.  Выберите в списке шаблонов пункт **Пекарня**, введите **bakerysample** в качестве **имени веб-сайта** и нажмите кнопку **Далее**.

	![Создание веб-сайта на основе шаблона][sitefromtemplatedetails]

3. После входа в Azure вы можете создать веб-сайт Azure для локального сайта.  Выберите уникальное имя и выберите центр обработки данных, где планируется создать сайт: 

	![Создание сайта в Azure][sitefromtemplateazure]

	По завершении создания веб-сайта WebMatrix откроется интегрированная среда разработки WebMatrix:

	![Интегрированная среда разработки WebMatrix][howtowebmatrixide] 

## Проверка веб-сайта

Пример с пекарней включает модель формы заказа, которая отправляет сообщение электронной почты с заказанной номенклатурой в предоставленную учетную запись Windows Live Hotmail.

1. В левой навигационной панели WebMatrix разверните папку **bakerysample**.

	![][modify1]

2. Откройте страницу *Order.cshtml*, дважды щелкнув имя файла.

	![][modify2]

3. Найдите комментарий "//конфигурация SMTP для Hotmail".

	![][modify3]

4. Измените значения в следующих строках на своего поставщика электронной почты:

		WebMail.SmtpServer = "smtp.live.com";
		WebMail.SmtpPort  = 25;
		WebMail.EnableSsl = true; 

		//Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

	Измените значение WebMail.SmtpServer на имя сервера электронной почты, который обычно используется для отправки электронной почты. Затем введите значения имени пользователя и пароля. Задайте для свойства "From" свой адрес электронной почты.

4. На ленте WebMatrix нажмите кнопку **Запуск**, чтобы протестировать сайт.

	![][modify4]

5. Нажмите кнопку **Заказать** на одном из продуктов и отправьте заказ самому себе.

6. Проверьте свою электронную почту и убедитесь, что получено подтверждение заказа. При возникновении проблем с отправкой электронной почты см. раздел [Проблемы при отправке электронной почты][sendmailissues] в "Руководстве по устранению неполадок веб-страниц ASP.NET (Razor)".
 

## Развертывание настраиваемого веб-сайта из WebMatrix в Azure.

1. В WebMatrix нажмите кнопку  **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для веб-сайта.

	![Просмотр публикации WebMatrix][howtopublishpreview]

2. Установите флажок рядом с файлом bakery.sdf и нажмите кнопку **Продолжить**.  После завершения публикации URL-адрес обновленного веб-сайта на платформе Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix.  

	![Публикация завершена][publishcomplete]

3. Щелкните ссылку, чтобы открыть веб-сайт в браузере:

	![Пример сайта "Пекарня"][bakerysample]

	URL-адрес веб-сайта можно также найти на портале Azure, щелкнув **Веб-сайты** для отображения всех веб-сайтов в вашей подписке. URL-адрес для каждого веб-сайта отображается в столбце URL-адреса на странице веб-сайтов.

## Изменение веб-сайта и его повторная публикация на веб-сайте Azure

С помощью WebMatrix можно изменить веб-сайт и повторно опубликовать его на своем веб-сайте Azure. В следующей процедуре вам предстоит добавить флажок, чтобы указать, что это заказ подарка.

1. Откройте страницу *Order.cshtml*.

2. Найдите определение формы класса "shiping". Вставьте следующий код сразу после блока &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Найдите в файле строку "var shipping = Request["orderShipping"];" и вставьте следующую строку кода, непосредственно за ней.

		var gift = Request["isGift"];

4. Сразу после блока кода, который проверяет, что адрес доставки не пуст, в следующем фрагменте кода.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Файл *order.cshtml* должен выглядеть, как на рисунке ниже.

	![][modify6]

5. Сохраните файл, запустите сайт локально и отправьте себе тестовый заказ. Убедитесь, что проверяется флажок создания.

	![][modify7]

6. Повторно опубликуйте сайт: нажмите кнопку **Публикация** на ленте **Главная**.

7. В диалоговом окне **Просмотр публикации**, убедитесь, что отмечены оба файла Order.cshtml, а затем нажмите кнопку "Продолжить".

8. Щелкните ссылку, чтобы открыть веб-сайт в браузере и протестировать обновление на веб-сайте Azure.

# Дальнейшие действия

Было показано, как создать и развернуть веб-сайт из WebMatrix в Azure. Для получения дополнительных сведений о WebMatrix посетите перечисленные ниже ресурсы:

* [WebMatrix для Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Веб-сайт WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png

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

