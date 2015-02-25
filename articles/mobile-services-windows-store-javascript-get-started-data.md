<properties pageTitle="Начало работы с данными (магазин Windows JavaScript) | Центр разработчиков для мобильных устройств" description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении JavaScript магазина Windows." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>	

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"/>


# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">Серверная часть .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">Серверная часть JavaScript</a>
</div>


В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Магазина Windows. В этом учебнике предстоит загрузить проект 2013 Visual Studio для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

>[AZURE.NOTE]В этом разделе показано, как с помощью Visual Studio 2013 добавить мобильные службы Azure в проект для Магазина Windows. Вы можете добавить ту же серверную мобильную службу JavaScript в проект универсального приложения для Windows. Дополнительные сведения см. в [версии этого учебника для универсального приложения Windows](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data).

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Получение приложения для Магазина Windows] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013 упрощает подключение приложения для Магазина Windows к мобильным службам. Чтобы выполнить ту же базовую процедуру с использованием Visual Studio 2012, выполните действия, описанные в разделе <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Загрузка проекта GetStartedWithData</h2>

Этот учебник создан на основе [приложения GetStartedWithMobileServices][Веб-сайт с примерами кода для разработчиков], являющегося проектом приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения совпадает с интерфейсом приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.  

1. Скачайте версию примера приложения GetStartedWithData на JavaScript с [веб-сайта с примерами кода для разработчиков]. 

2. Откройте загруженный проект в Visual Studio 2012 Express для Windows 8, разверните папку **js** и просмотрите файл default.js.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в объекте **List** в локальной памяти.

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

##<a name="create-service"></a>Создание мобильной службы в Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>В обозревателе решений разверните папки **services**, **mobile services**, **&lt;ваша_служба&gt;**, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот скрипт была добавлена в файл default.html, эта переменная доступна для всех файлов скриптов, на которые также указывает ссылка на этой странице.</p>
</li>
</ol>

##<a name="add-table"></a>Добавление новой таблицы для хранения данных

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Новые таблицы создаются со столбцами "Id", "__createdAt", "__updatedAt" и "__version". Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Обновление приложения для использования мобильной службы

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Тестирование работы приложения с новой мобильной службой

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]
  
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

   	Обратите внимание, что отмеченный элемент теперь исчез из списка. Каждое обновление ведет к обмену данными с мобильной службой, которая теперь возвращает отфильтрованные данные.

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов с разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.

<!-- Anchors. -->

[Получение приложения для Магазина Windows]: #download-app
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
[Проверка и изменение данных с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Уточнение запросов с разбиением по страницам]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/


<!--HONumber=42-->
