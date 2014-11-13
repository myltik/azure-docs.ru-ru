<properties linkid="develop-mobile-tutorials-create-pull-notifications-dotnet" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"/>
# Определение настраиваемого интерфейса API с поддержкой периодических уведомлений

<div class="dev-center-tutorial-selector"> 
    <a href="/ru-ru/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/create-pull-notifications-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
</div>

В этом разделе показано, как использовать настраиваемый интерфейс API для поддержки периодических уведомлений в приложениях Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке настраиваемого интерфейса API и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск". Дополнительные сведения см. в разделе [Периодические уведомления][Периодические уведомления].

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными]. Для этого требуются следующие действия:

1.  [Определение настраиваемого интерфейса API][Определение настраиваемого интерфейса API]
2.  [Обновление приложения для включения периодических уведомлений][Обновление приложения для включения периодических уведомлений]
3.  [Тестирование приложения][Тестирование приложения]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными].

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![](./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png)

2.  Нажмите вкладку **API**, затем нажмите **Создать настраиваемый API**.

    ![][1]

    Откроется диалоговое окно **Создать настраиваемый API**.

3.  Измените значение **Получить разрешение** на **Все**, введите *tiles* в поле **Имя API** и нажмите кнопку проверки.

    ![][2]

    При этом создается новый интерфейс API с открытым доступом GET.

4.  Щелкните новую запись tiles в таблице API.

    ![][3]

5.  Перейдите на вкладку **Скрипт** и замените существующий код на следующий:

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

    <div class="dev-callout"><b>Примечание.</b>
    <p>Этот пользовательский скрипт API использует <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">модуль wns</a> Node.js, на который можно ссылаться с помощью функции <strong>require</strong> . Этот модуль отличается от <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">объекта wns</a>, возвращаемого <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554217.aspx">push-объектом</a>, который используется для отправки push-уведомлений из серверных скриптов.</p>
</div>

Затем вам предстоит изменить пример приложения для запуска периодических уведомлений, которые обновляют динамическую плитку, отправляя запросы к новому настраиваемому интерфейсу API.

## <a name="update-app"></a><span class="short-header">Обновление приложения </span>Обновление приложения для включения периодических уведомлений

1.  В Visual Studio нажмите клавишу F5, чтобы запустить пример приложения из предыдущего учебника.

2.  Убедитесь, что отображается по крайней мере один элемент. Если элементов нет, введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

3.  В Visual Studio откройте файл проекта App.xaml.cs и добавьте следующие директивы using:

        using Windows.UI.Notifications;

4.  Добавьте следующий код в обработчик событий **OnLaunched**:

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

    Этот код включает периодические уведомления для запроса данных шаблона плитки у настраиваемого интерфейса API **tiles**. Выберите значение [PeriodicUpdateRecurrance], лучше всего соответствующее частоте обновления данных.

## <a name="test-app"></a> Тестирование приложения

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение еще раз.

    При этом периодические уведомления будут включены.

2.  Перейдите на начальный экран, найдите динамическую плитку приложения и обратите внимание, что теперь плитка показывает данные элемента.

    ![][4]

## Дальнейшие действия

После создания периодического уведомления изучите следующие статьи о мобильных службах:

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Периодическими уведомлениями управляет Windows, и они создаются только на основе стандартного расписания. Push-уведомления могут отправляться мобильной службой по требованию и могут быть всплывающими, необработанными и уведомлениями плитки.

-   [Справочник серверных скриптов мобильных служб][Справочник серверных скриптов мобильных служб]
    Дополнительные сведения о создании настраиваемых API.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
    Дополнительные сведения об использовании мобильных служб в .NET.

<!-- Anchors. --> 

  [Периодические уведомления]: http://msdn.microsoft.com/ru-ru/library/windows/apps/jj150587.aspx
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
  [Определение настраиваемого интерфейса API]: #define-custom-api
  [Обновление приложения для включения периодических уведомлений]: #update-app
  [Тестирование приложения]: #test-app
  [портал управления Azure]: https://manage.windowsazure.com/

<!-- Images. -->   
  [1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
  [2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
  [3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
  [4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->

  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
  [Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
