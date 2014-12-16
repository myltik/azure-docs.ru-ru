<properties urlDisplayName="Get Started with Data" pageTitle="Начало работы с данными (магазин Windows JavaScript) | Центр разработчиков для мобильных устройств "metaKeywords ="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />


# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">Серверная служба .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">Серверная служба JavaScript</a>
</div>


В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Магазина Windows. В этом учебнике предстоит загрузить проект Visual Studio 2013 для приложения, которое хранит данные в памяти, создать новые мобильные службы, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

>[WACOM.NOTE]В этом разделе показано, как использовать Visual Studio 2013 для добавления мобильных служб Azure в проект для Магазина Windows. Вы можете добавить ту же серверную мобильную службу JavaScript в проект универсального приложения для Windows. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data) этого учебника.

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Get the Windows Store app] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013 упрощает подключение приложения для Магазина Windows к мобильным службам. Чтобы выполнить ту же базовую процедуру с помощью Visual Studio 2012, выполните действия, описанные в разделе <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Загрузка пакета GetStartedWithData</h2>

Этот учебник основан на проекте [GetStartedWithMobileServices app][Developer Code Samples site] - проекте приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс этого приложения идентичен приложению, которое создается при быстром начале работы с мобильными службами, но, что добавленные элементы хранятся локально в памяти.  

1. Загрузите версию примера приложения GetStartedWithData на JavaScript с веб-сайта [Примеры кода для разработчиков]. 

2. В Visual Studio 2012 Express для Windows 8 откройте загруженный проект, откройте папку **js** и просмотрите файл default.js.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в локальной памяти **List**.

3. Нажмите клавишу **F5** для повторной сборки проекта, затем запустите приложение.

4. В приложении введите любой текст в поле **Вставить TodoItem**, затем щелкните **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запросить и обновить данные**.

##<a name="create-service"></a>Создание мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>В обозревателе решений разверните **службы**, **мобильные службы**, папки **вашей_службы**, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот сценарий была добавлена в файл default.html, эта переменная доступна для всех файлов сценариев, на которые также указывает ссылка на этой странице.</p>
</li>
</ol>

##<a name="add-table"></a>Добавление новой таблицы для хранения данных

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]Новые таблицы создаются со столбцами Id, __createdAt, __updatedAt и __version. Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Обновление приложения для использования мобильной службы

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Тестирование приложения новой мобильной службы

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**. 

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5. В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить**. 

  	Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6. В файле проекта default.js замените существующую функцию **RefreshTodoItems** следующим кодом, который будет отфильтровывать выполненные элементы:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. В приложении отметьте еще один элемент из списка, а затем нажмите кнопку **Обновить**.

   	Обратите внимание, что отмеченный элемент исчез из списка. Каждое обновление ведет к обмену данными с мобильной службой, которая теперь возвращает отфильтрованные данные.

На этом прохождение учебника **Начало работы с данными** завершается.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными из мобильных служб. Далее рассмотрите выполнение одного из следующих учебников, который основан на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью сценариев]
  <br/>Использование сценариев сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.

<!-- Anchors. -->

[Разработка приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Проверка и изменение данных с помощью сценариев]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/
