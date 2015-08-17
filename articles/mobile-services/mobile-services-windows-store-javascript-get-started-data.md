<properties 
	pageTitle="Добавление мобильных служб в существующее приложение (Магазин Windows — JavaScript) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении JavaScript магазина Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>


# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##Обзор
В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Магазина Windows. В этом учебнике вам предстоит скачать проект Visual Studio 2013 для приложения, которое хранит данные в памяти, создать новую мобильную службу, интегрировать мобильную службу в приложение, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Набор Visual Studio 2013, который вы будете использовать для подключения проекта приложения Магазина Windows к мобильным службам.

##Загрузка пакета GetStartedWithData

Этот учебник основан на приложении [GetStartedWithMobileServices][Developer Code Samples site], представляющим собой проект приложения Магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения совпадает с интерфейсом приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1. Скачайте версию примера приложения GetStartedWithData, созданную на JavaScript , с веб-сайта [примеров кода для разработчиков]. 

2. Откройте загруженный проект в Visual Studio, разверните папку **js** и ознакомьтесь с файлом default.js.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в объекте **List** в локальной памяти.

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

   	![][0]

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

##<a name="create-service"></a>Создание мобильной службы в Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>В обозревателе решений разверните папки **services**, **mobile services**, **&lt;ваша_служба>**, откройте файл сценария service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот сценарий была добавлена в файл default.html, эта переменная доступна для всех файлов сценариев, на которые также указывает ссылка на этой странице.</p>
</li>
</ol>

##Добавление новой таблицы для хранения данных

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##Обновление приложения для использования мобильной службы

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../../includes/mobile-services-windows-javascript-update-data-app.md)]

##Тестирование приложения новой мобильной службы

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5. В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить**

  	Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6. В файле проекта default.js замените существующую функцию **RefreshTodoItems** следующим кодом, который будет отфильтровывать завершенные элементы:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. В приложении отметьте другой элемент в списке, затем нажмите кнопку **Обновить**.

   	Обратите внимание, что отмеченный элемент теперь исчез из списка. Каждое обновление ведет к обмену данными с мобильной службой, которая теперь будет возвращать отфильтрованные данные.

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Рекомендуем прочитать один из следующих разделов:

* [Добавление проверки подлинности в приложение](mobile-services-windows-store-javascript-get-started-users.md) <br/>Узнайте, как проверять подлинность пользователей приложения.

* [Добавление push-уведомлений в приложение](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) <br/>Узнайте, как отправлять в приложение простейшие push-уведомления.

* [Справочник принципов использования мобильных служб HTML/JavaScript](mobile-services-html-how-to-use-client-library.md) <br/>Узнайте, как использовать мобильные службы с HTML и JavaScript.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[Портале управления]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[примеров кода для разработчиков]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=August15_HO6-->