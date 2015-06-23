<properties 
	pageTitle="Определение настраиваемого API с поддержкой pull-уведомлений мобильные службы — Azure" 
	description="Узнайте, как определить настраиваемый API, который поддерживает периодические уведомления в приложениях Магазина Windows, использующих мобильные службы Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="glenga"/>
# Определение настраиваемого интерфейса API с поддержкой периодических уведомлений

<div class="dev-center-tutorial-selector"> 
	<a href="/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Магазин Windows — C#">Магазин Windows — C#</a><a href="/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Магазин Windows — JavaScript" class="current">Магазин Windows — JavaScript</a>
</div>

В этом разделе показано, как использовать настраиваемый интерфейс API для поддержки периодических уведомлений в приложениях Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке настраиваемого интерфейса API и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск". Дополнительные сведения см. в разделе [Периодические уведомления].

Вы добавите эту функцию в приложение, созданное в ходе работы с учебником [Приступая к работе с мобильными службами] или [Добавление мобильных служб в существующее приложение]. Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для включения периодических уведомлений]
3. [Тестирование приложения] 

Этот учебник создан на основе краткого руководства по мобильным службам. Прежде чем начать работу с этим учебником, необходимо пройти задания учебника [Приступая к работе с мобильными службами] или [Добавление мобильных служб в существующее приложение].

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

   	![][0]

2. Нажмите вкладку **API**, затем нажмите **Создать настраиваемый API**.

   	![][1]

   	Откроется диалоговое окно **Создать настраиваемый API**.

3. Измените значение **Получить разрешение** на **Все**, введите _ tiles _в поле **Имя API** и нажмите кнопку проверки.

   	![][2]

	При этом создается новый интерфейс API с открытым доступом GET.

4. Щелкните новую запись tiles в таблице API.

	![][3]

5. Перейдите на вкладку **Скрипт** и замените существующий код на следующий:

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

	Этот код возвращает 3 первых незавершенных элемента из таблицы TodoItem, а затем загружает их в объект JSON, передаваемый функции **wns**.**createTileSquareText01**. Эта функция возвращает следующий XML шаблона плитки:

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

   	> [AZURE.NOTE]Этот пользовательский скрипт API использует [модуль wns](http://go.microsoft.com/fwlink/p/?LinkId=306750) Node.js, на который можно ссылаться с помощью функции **require** . Этот модуль отличается от [объекта wns](http://go.microsoft.com/fwlink/p/?LinkId=260591), возвращаемого [push-объектом](http://msdn.microsoft.com/library/windowsazure/jj554217.aspx), который используется для отправки push-уведомлений из серверных скриптов.

Затем вам предстоит изменить пример приложения для запуска периодических уведомлений, которые обновляют динамическую плитку, отправляя запросы к новому настраиваемому интерфейсу API.

##<a name="update-app"></a>Обновление приложения для включения периодических уведомлений

1. В Visual Studio нажмите клавишу F5, чтобы запустить пример приложения из предыдущего учебника.

2. Убедитесь, что отображается по крайней мере один элемент. Если элементов нет, введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

3. В обозревателе решений Visual Studio разверните папку `\js`, откройте проект default.js, а затем добавьте следующие строки кода после кода, определяющего переменную **client**:

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

	Этот код включает периодические уведомления для запроса данных шаблона плитки у настраиваемого интерфейса API **tiles**. Выберите значение **PeriodicUpdateRecurrance**, соответствующее частоте обновления данных.

## <a name="test-app"></a>Тестирование приложения

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение еще раз.

	При этом периодические уведомления будут включены.

2. Перейдите на начальный экран, найдите динамическую плитку приложения и обратите внимание, что теперь плитка показывает данные элемента.

 	![][4]

## Дальнейшие действия

После создания периодического уведомления изучите следующие статьи о мобильных службах:

* [Приступая к работе с push-уведомлениями] <br/>Периодическими уведомлениями управляет Windows, и они создаются только на основе стандартного расписания. Push-уведомления могут отправляться мобильной службой по требованию и могут быть всплывающими, необработанными и уведомлениями плитки.

* [Справочник серверных скриптов мобильных служб] <br/>Дополнительные сведения о создании настраиваемых API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для включения периодических уведомлений]: #update-app
[Тестирование приложения]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: mobile-services-javascript-backend-windows-store-javascript-get-started.md
[Добавление мобильных служб в существующее приложение]: ../mobile-services-windows-store-javascript-get-started.md
[Приступая к работе с push-уведомлениями]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md

[портал управления Azure]: https://manage.windowsazure.com/
[Периодические уведомления]: http://msdn.microsoft.com/library/windows/apps/jj150587.aspx


<!--HONumber=54--> 