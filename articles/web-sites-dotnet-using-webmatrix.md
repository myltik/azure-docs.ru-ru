<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle=".NET website with WebMatrix - Azure tutorials" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure web site WebMatrix, Azure website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix

В этом руководстве описывается использование Microsoft WebMatrix для создания и развертывания веб-сайта в Azure. Будет использоваться пример приложения из шаблона сайта WebMatrix.

Вы узнаете:

-   Как войти в Azure с помощью WebMatrix.
-   Как создать сайт, используя встроенный шаблон, с помощью WebMatrix.
-   Как выполнить развертывание настраиваемого веб-сайта непосредственно из WebMatrix в Azure.

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание](../includes/create-account-and-websites-note.md)]

## Вход в Azure

1.  Запуск WebMatrix.
2.  Если это первый случай использования WebMatrix 3, будет выведен запрос на вход в Azure. В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись** Выберите **Вход** с использованием учетной записи Майкрософт.

    ![Добавление учетной записи][Добавление учетной записи]

3.  Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт:

    ![Вход в Azure][Вход в Azure]

## Создание сайта с помощью встроенного шаблона для Azure

1.  На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов.

    ![Новый сайт из коллекции шаблонов][Новый сайт из коллекции шаблонов]

2.  В коллекции шаблонов отображается список доступных шаблонов, которые можно запустить локально или в Azure. Выберите в списке шаблонов пункт **Пекарня**, введите **bakerysample** в качестве **имени веб-сайта** и нажмите кнопку **Далее**.

    ![Создание веб-сайта на основе шаблона][Создание веб-сайта на основе шаблона]

3.  После входа в Azure вы можете создать веб-сайт Azure для локального сайта. Выберите уникальное имя и выберите центр обработки данных, где планируется создать сайт:

    ![Создание сайта в Azure][Создание сайта в Azure]

    По завершении создания веб-сайта WebMatrix откроется интегрированная среда разработки WebMatrix.

    ![Интегрированная среда разработки WebMatrix][Интегрированная среда разработки WebMatrix]

## Тестирование веб-сайта

Пример с пекарней включает модель формы заказа, которая отправляет сообщение электронной почты с заказанной номенклатурой в предоставленную учетную запись Windows Live Hotmail.

1.  На левой навигационной панели WebMatrix разверните папку **bakerysample**.

    ![][0]

2.  Откройте страницу *Order.cshtml*, дважды щелкнув имя файла.

    ![][1]

3.  Найдите комментарий "//конфигурация SMTP для Hotmail".

    ![][2]

4.  Измените значения в следующих строках на своего поставщика электронной почты:

        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort  = 25;
        WebMail.EnableSsl = true; 

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

    Измените значение WebMail.SmtpServer на имя сервера электронной почты, который обычно используется для отправки электронной почты. Затем введите значения имени пользователя и пароля. Задайте для свойства "From" свой адрес электронной почты.

5.  На ленте WebMatrix нажмите кнопку **Запуск**, чтобы протестировать сайт.

    ![][3]

6.  Нажмите кнопку **Заказать** на одном из продуктов и отправьте заказ самому себе.

7.  Проверьте свою электронную почту и убедитесь, что подтверждение заказа пришло. При возникновении проблем с отправкой электронной почты см. раздел [Проблемы при отправке электронной почты][Проблемы при отправке электронной почты] в "Руководстве по устранению неполадок веб-страниц ASP.NET (Razor)".

## Развертывание настраиваемого веб-сайта из WebMatrix в Azure

1.  В WebMatrix нажмите кнопку **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для веб-сайта.

    ![Просмотр публикации WebMatrix][Просмотр публикации WebMatrix]

2.  Установите флажок рядом с файлом bakery.sdf и нажмите кнопку **Продолжить**. После завершения публикации URL-адрес обновленного веб-сайта на платформе Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix.

    ![Публикация завершена][Публикация завершена]

3.  Щелкните по ссылке, чтобы открыть веб-сайт в браузере.

    ![Пример сайта "Пекарня"][Пример сайта "Пекарня"]

    URL-адрес веб-сайта можно также найти на портале Azure, щелкнув **Веб-сайты** для отображения всех веб-сайтов в вашей подписке. URL-адрес для каждого веб-сайта отображается в столбце URL-адреса на странице веб-сайтов.

## Изменение веб-сайта и его повторная публикация на веб-сайте Azure

С помощью WebMatrix можно изменить веб-сайт и повторно опубликовать его на своем веб-сайте Azure. В следующей процедуре вам предстоит добавить флажок, чтобы указать, что это заказ подарка.

1.  Откройте страницу *Order.cshtml*.

2.  Найдите определение формы класса shiping. Вставьте следующий код сразу после блока \<li\>.

        <li class="gift">
            <div class="fieldcontainer" data-role="fieldcontain">
                <label for="isGift">This is a gift</label>           
                <div>@Html.CheckBox("isGift")</div>
            </div>
        </li>

    ![][4]

3.  Найдите в файле строку "var shipping = Request["orderShipping"];" и вставьте следующую строку кода непосредственно за ней.

        var gift = Request["isGift"];

4.  Сразу после блока кода, который проверяет, что адрес доставки не пуст, в следующем фрагменте кода.

        if(gift != null) {
            body += "This is a gift." + "<br/>";
        }

    Файл *order.cshtml* должен выглядеть, как на рисунке ниже.

    ![][5]

5.  Сохраните файл, запустите сайт локально и отправьте себе тестовый заказ. Убедитесь, что проверяется флажок создания.

    ![][6]

6.  Повторно опубликуйте сайт, нажав кнопку **Публикация** на ленте **Главная**.

7.  В диалоговом окне **Просмотр публикации** убедитесь, что отмечены оба файла Order.cshtml, а затем нажмите кнопку "Продолжить".

8.  Щелкните по ссылке, чтобы открыть веб-сайт в браузере и протестировать обновление на веб-сайте Azure.

# Дальнейшие действия

Теперь вы увидели, как создать и развернуть веб-сайт из WebMatrix в Azure. Для получения дополнительных сведений о WebMatrix посетите перечисленные ниже ресурсы:

-   [WebMatrix для Azure][WebMatrix для Azure]

-   [Веб-сайт WebMatrix][Веб-сайт WebMatrix]

  [Добавление учетной записи]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
  [Вход в Azure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
  [Новый сайт из коллекции шаблонов]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
  [Создание веб-сайта на основе шаблона]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
  [Создание сайта в Azure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png
  [Интегрированная среда разработки WebMatrix]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
  [0]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
  [1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
  [2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
  [3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
  [Проблемы при отправке электронной почты]: http://go.microsoft.com/fwlink/?LinkId=253001#email
  [Просмотр публикации WebMatrix]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
  [Публикация завершена]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
  [Пример сайта "Пекарня"]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
  [4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
  [5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
  [6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png
  [WebMatrix для Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Веб-сайт WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
