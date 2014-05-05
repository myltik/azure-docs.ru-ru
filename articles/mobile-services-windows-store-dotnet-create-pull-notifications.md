<properties linkid="develop-mobile-tutorials-create-pull-notifications-dotnet" urlDisplayName="Определение пользовательского интерфейса API с поддержкой pull-уведомлений" pageTitle="Определение пользовательского интерфейса API с поддержкой pull-уведомлений - Мобильные службы Azure" metaKeywords="" description="Узнайте, как определить пользовательский интерфейс API, который поддерживает периодические уведомления в приложениях Магазина Windows, использующих мобильные службы Azure." metaCanonical="" services="" documentationCenter="" title="Определение настраиваемого интерфейса API с поддержкой периодических уведомлений" authors="glenga" solutions="" manager="" editor="" />




# Определение настраиваемого интерфейса API с поддержкой периодических уведомлений

<div class="dev-center-tutorial-selector"> 
	<a href="/ru-ru/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/create-pull-notifications-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
</div>

В этом разделе показано, как использовать настраиваемый интерфейс API для поддержки периодических уведомлений в приложениях Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке настраиваемого интерфейса API и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск". Дополнительные сведения см. в разделе [Периодические уведомления]. 

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для включения периодических уведомлений]
3. [Тестирование приложения] 

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными].  

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

   	![][0]

2. Откройте вкладку **API**, затем щелкните **Создать настраиваемый API**.

   	![][1]

	Откроется диалоговое окно **Создать настраиваемый API**.

3. Измените **Разрешение GET** на **Все**, введите _tiles_ в поле **API-имя**и нажмите кнопку "Проверить".

   	![][2]

	При этом создается новый интерфейс API с открытым доступом GET.

4. Щелкните новую запись tiles в таблице API.

	![][3]

5. Откройте вкладку **Скрипт** и замените существующий код на приведенный ниже код:

		exports.get = function(request, response) {
		    var wns = require('wns');
		    var todoItems = request.service.tables.getTable('TodoItem');
		    todoItems.where({
		        complete: false
		    }).read({
		        success: sendResponse
		    });
		
		    function sendResponse(results) {
		        var tileText = {
		            text1: "My todo list"
		        };
		        var i = 0;
		        console.log(results)
		        results.forEach(function(item) {
		            tileText["text" + (i + 2)] = item.text;
		            i++;
		        });
		        var xml = wns.createTileSquareText01(tileText);
		        response.set('content-type', 'application/xml');
		        response.send(200, xml);
		    }
		};

	Этот код возвращает 3 первых незавершенных элемента из таблицы TodoItem, а затем загружает их в объект JSON, передаваемый функции **wns**.**createTileSquareText01**. Эта функция возвращает следующий XML-код шаблона плитки:

		<tile>
			<visual>
				<binding template="TileSquareText01">
					<text id="1">My todo list</text>
					<text id="2">Task 1</text>
					<text id="3">Task 2</text>
					<text id="4">Task 3</text>
				</binding>
			</visual>
		</tile>

	Используется функция **exports.get**, так как клиент отправляет запрос GET, чтобы открыть шаблон плитки.

   	<div class="dev-callout"><b>Note</b>
   		<p>This custom API script uses the Node.js <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns module</a>, which is referenced by using the <strong>require</strong> function. This module is different from the <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">wns object</a> returned by the <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554217.aspx">push object</a>, which is used to send push notifications from server scripts.</p>
   	</div>

Затем вам предстоит изменить пример приложения для запуска периодических уведомлений, которые обновляют динамическую плитку, отправляя запросы к новому настраиваемому интерфейсу API.

<h2><a name="update-app"></a><span class="short-header">Обновление приложения </span>Обновление приложения для включения периодических уведомлений</h2>

1. В Visual Studio нажмите клавишу F5, запускать пример приложения из предыдущего учебника.

2. Убедитесь, что отображается по крайней мере один элемент. Если элементов нет, введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

3. В Visual Studio откройте файл App.xaml.cs и добавьте следующие операторы using:

		using Windows.UI.Notifications;

4. Добавьте следующий код в обработчик событий **OnLaunched**:

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

	Этот код включает периодические уведомления для запроса данных шаблона плитки у настраиваемого интерфейса API **tiles**. Выберите значение [PeriodicUpdateRecurrance], соответствующее частоте обновления данных.

## <a name="test-app"></a>Тестирование приложения

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение еще раз.

	При этом периодические уведомления будут включены.

2. Перейдите на начальный экран, найдите динамическую плитку приложения и обратите внимание, что теперь плитка показывает данные элемента.

 	![][4]

## Дальнейшие действия

После создания периодического уведомления изучите следующие статьи о мобильных службах:

* [Приступая к работе с push-уведомлениями]
	<br/>Периодическими уведомлениями управляет Windows, и они создаются только на основе стандартного расписания. Push-уведомления могут отправляться мобильной службой по требованию и могут быть всплывающими, необработанными уведомлениями и уведомлениями на плитке.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых интерфейсов API.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для включения периодических уведомлений]: #update-app
[Тестирование приложения]: #test-app
[Следующие шаги]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript и HTML]: mobile-services-win8-javascript/

[Портал управления Azure]: https://manage.windowsazure.com/
[Периодические уведомления]: http://msdn.microsoft.com/ru-ru/library/windows/apps/jj150587.aspx

[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library


