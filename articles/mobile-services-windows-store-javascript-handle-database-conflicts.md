<properties urlDisplayName="Optimistic concurrency" pageTitle="Обработка конфликтов записи базы данных с поддержкой оптимистичного параллелизма (магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" модуль записи="wesmc" description="Узнайте, как обрабатывать конфликты записи в базу данных как на сервере, так и в приложении магазина Windows." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Обработка конфликтов записи базы данных

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">C# в Магазине Windows</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript" class="current">JavaScript в Магазине Windows</a>
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	

Этот учебник поможет вам лучше понять, как обрабатывать конфликты, возникающие в приложениях Магазина Windows, когда несколько клиентов пытаются выполнить запись в один и тот же элемент базы данных. В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. Мобильные службы Azure предоставляют поддержку для обнаружения и разрешения таких конфликтов. В этом разделе описываются шаги, которые позволят обрабатывать конфликты записи базы данных как на сервере, так и в приложении.

В этом учебнике вы добавите функции в приложение быстрого запуска для обработки конфликтов, возникающих при обновлении базы данных TodoItem. В этом учебнике вы ознакомитесь со следующими основными действиями:

1. [Обновите приложение, чтобы разрешить обновления]
2. [Включите в приложении обнаружение конфликтов]
3. [Протестируйте в приложении конфликты записи базы данных]
4. [Автоматическое разрешение конфликтов в серверных скриптах]


Для работы с данным учебником требуется следующее

+ Microsoft Visual Studio 2013 Express для Windows или более поздней версии.
+ Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] и загрузить версию проекта для JavaScript. 
+ [Учетная запись Azure]
+ Windows Azure Mobile Services NuGet Package 1.1.5 или более поздняя версия. Для получения последней версии выполните следующие действия:
	1. Откройте проект в Visual Studio и щелкните правой кнопкой мыши проект в обозревателе решений, а затем щелкните **Управление пакетами NuGet**. 


	2. Разверните **В сети** и нажмите **Microsoft и .NET**. В поле "Поиск" введите **WindowsAzure.MobileServices.WinJS**. Нажмите кнопку **Установить** для пакета NuGet **Мобильные службы Windows Azure для WinJS**.

		![][20]


 

<h2><a name="uiupdate"></a>Обновите приложение, чтобы разрешить обновления</h2>

В этом разделе вы обновите пользовательский интерфейс, что позволит обновлять текст для каждого элемента. Шаблон будет содержать флажок и текстовое поле для каждого элемента в таблице базы данных. Можно будет обновить текстовое поле TodoItem. Приложение будет обрабатывать событие `keydown`, чтобы при нажатии клавиши **Ввод** обновлялся элемент.


1. Откройте в Visual Studio версию проекта TodoList на языке JavaScript, который вы загрузили в ходе работы с учебником [Начало работы с мобильными службами].
2. В обозревателе решений Visual Studio откройте файл default.html и замените определение тега div `TemplateItem` на тег, показанный ниже, и сохраните изменения. При этом добавляется элемент управления textbox, позволяющий изменить текст элемента TodoItem.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
              <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
              </div>
          </div>
        </div>


3. В обозревателе решений в Visual Studio разверните папку **js**. Откройте файл default.js и замените функцию `updateTodoItem` на следующее определение, которое не удаляет обновленные элементы из пользовательского интерфейса.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          todoTable.update(todoItem);
          };


4. В файле default.js добавьте следующий обработчик событий для события `keydown`, чтобы элемент обновлялся при нажатии клавиши **Ввод**.

        listItems.onkeydown = function (eventArgs) {
          if (eventArgs.key == "Enter") {
            var todoItem = eventArgs.target.dataContext.backingData;
            todoItem.text = eventArgs.target.value;
            updateTodoItem(todoItem);
            }
          };

Теперь, когда пользователь нажимает клавишу **Ввод**, приложение записывает изменения текста в каждом элементе обратно в базу данных.

<h2><a name="enableOC"></a>Включите в приложении обнаружение конфликтов</h2>

Мобильные службы Azure поддерживают управление оптимистичным параллелизмом путем отслеживания изменений каждого элемента с помощью столбца системного свойства `__version`, который добавляется к каждой таблице. В этом разделе мы сделаем так, чтобы приложение определяло эти конфликты записи с помощью системного свойства `__version`. После включения этого системного свойства для todoTable приложение во время попытки обновления будет получать уведомления об исключении `MobileServicePreconditionFailedException`, если запись была изменена с момента последнего запроса. Затем оно сможет выбрать, зафиксировать свои изменения в базе данных или оставить без изменений последнее изменение в базе данных. Дополнительные сведения о системных свойствах мобильных служб см. в [Системные свойства]

1. В файле default.js в объявлении переменной `todoTable`  добавьте следующий код, чтобы включить в него системное свойство **__version**, что обеспечит поддержку обнаружения конфликтов записи.

        var todoTable = client.getTable('TodoItem');
        todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;


2. После добавления свойства `Version` к системным свойствам таблицы приложение во время обновления будет получать уведомления об исключении `MobileServicePreconditionFailedException`, если запись была изменена с момента последнего запроса. Это исключение будет перехвачено в JavaScript с функцией ошибки. Сообщение об ошибке содержит последнюю версию элемента с сервера, что используется для разрешения конфликтов. В файле default.js обновите функцию `updateTodoItem` для перехвата ошибки и вызова функции `resolveDatabaseConflict`.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          // If the server version of the record has been updated, we get the updated
          // record from the Precondition Failed error in order to resolve the conflict.
          var serverItem = null;
          todoTable.update(todoItem).then(null, function (error) {
            if (error.message == "Precondition Failed") {
              serverItem = error.serverInstance;
            }
            else {
              var msgDialog =
                new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                msgDialog.showAsync();
            }
          }).done(function () {
            if (serverItem != null)
              resolveDatabaseConflict(todoItem, serverItem);
          });
        };


3. Добавьте в файл default.js определение функции `resolveDatabaseConflict()`, ссылка на которую указана в функции `updateTodoItem`. Обратите внимание, что для разрешения конфликта перед обновлением элемента в базе данных необходимо в качестве версии локального элемента задать обновленную версию с сервера. В противном случае конфликт будет возникать постоянно.


        var resolveDatabaseConflict = function (localItem, serverItem) {
          var content = "This record has been changed as follows on the server already..\n\n" +
              "id : " + serverItem.id + "\n" +
              "text : " + serverItem.text + "\n" +
              "complete : " + serverItem.complete + "\n\n" +
              "Do you want to overwrite the server instance with your data?";
          var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
          msgDialog.showAsync().done(function (command) {
              if (command.label == "Yes") {
                  localItem.__version = serverItem.__version;
                  updateTodoItem(localItem);
              }
          });
        }


<h2><a name="test-app"></a>Протестируйте в приложении конфликты записи базы данных</h2>

В этом разделе вы построите пакет приложения для Магазина Windows, чтобы устанавливать приложение на втором компьютере или виртуальной машине. Затем вы выполните приложение на обоих компьютерах, создавая конфликт записи, чтобы проверить код. Оба экземпляра приложения будут пытаться обновить свойство `text`  одного и того же элемента, что потребует от пользователя разрешения конфликта.


1. Создайте пакет приложения для Магазина Windows, чтобы устанавливать приложение на втором компьютере или виртуальной машине. Чтобы сделать это, нажмите в Visual Studio **Проект**->**Магазин**->**Создать пакеты приложения**.

	![][0]

2. На экране "Создать свои пакеты" щелкните **Нет**, поскольку этот пакет не будет загружен в Магазин Windows. Затем нажмите **Далее**.

	![][1]

3. На экране "Выбор и настройка пакетов" примите значения по умолчанию и нажмите кнопку **Создать**.

	![][10]

4. На экране "Завершение создания пакета" нажмите ссылку **Расположение вывода**, чтобы открыть место расположения пакета.

   	![][11]

5. Скопируйте папку пакета, "todolist_1.0.0.0_AnyCPU_Debug_Test", на второй компьютер. На этом компьютере откройте папку пакета и щелкните правой кнопкой мыши скрипт PowerShell **Add-AppDevPackage.ps1** и нажмите кнопку **Запуск с помощью PowerShell**, как показано ниже. Следуйте инструкциям на экране для установки приложения.

	![][12]
  
6. Запустите экземпляр 1 приложения в Visual Studio, нажав **Отладка**->**Начать отладку**. На экране запуска второго компьютера щелкните стрелку вниз, чтобы увидеть "Приложения по имени". Затем щелкните приложение **todolist** для запуска экземпляра 2 приложения. 

	Экземпляр 1 приложения	
	![][2]

	Экземпляр 2 приложения	
	![][2]


7. В экземпляре 1 приложения обновите текст последнего элемента до **Тест записи 1**, затем нажмите **ВВОД**, чтобы обновить базу данных. На следующем снимке экрана показан пример.
	
	Экземпляр 1 приложения	
	![][3]

	Экземпляр 2 приложения	
	![][2]

8. На этом этапе последний элемент в экземпляре 2 приложения содержит старую версию элемента. В данный экземпляр приложения введите **Тест записи 2** для свойства `text` последнего пункта и нажмите **Ввод**, чтобы обновить базу данных старым свойством `_version`.

	Экземпляр 1 приложения	
	![][4]

	Экземпляр 2 приложения	
	![][5]

9. Поскольку значение `__version`, используемое при попытке обновления не совпадает со значением `__version` с сервера, то Mobile Services SDK поднимает `MobileServicePreconditionFailedException` в качестве ошибки в функции `updateTodoItem`, позволяя приложению разрешить этот конфликт. Чтобы разрешить конфликт, нажмите **Да** для сохранения значений из экземпляра. Или же нажмите **Нет**, чтобы отклонить значения в экземпляре 2 и использовать значения из экземпляра 1 приложения. 

	Экземпляр 1 приложения	
	![][4]

	Экземпляр 2 приложения	
	![][6]



<h2><a name="scriptsexample"></a>Автоматическое разрешение конфликтов в серверных скриптах</h2>

Можно обнаруживать и разрешать конфликты записи в скриптах сервера. Это хорошая идея - использовать записанную логику вместо взаимодействия с пользователем для разрешения конфликта. В этом разделе вы добавите скрипт со стороны сервера в таблицу TodoItem для приложения. Логика, которую будет использовать этот скрипт для разрешения конфликтов, выглядит следующим образом.

+  Если для поля ` complete` таблицы TodoItem установлено значение true, таблица считается завершенной и свойство `text` больше не может быть изменено.
+  Если поле ` complete` таблицы TodoItem по-прежнему имеет значение false, попытки обновить свойство `text` будут продолжаться.

Дальнейшие действия описывают добавление серверного скрипта обновления и его тестирование.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

   	![][7]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][8]

3. Щелкните **Скрипт** и выберите операцию **Обновить**.

   	![][9]

4. Замените существующий скрипт следующей функцией, затем нажмите кнопку **Сохранить**.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. Запустите приложение **todolist** на обоих компьютерах. Измените поле `text` таблицы TodoItem для последнего элемента в экземпляре 2 и нажмите **Ввод**, чтобы приложение обновило базу данных.

	Экземпляр 1 приложения	
	![][4]

	Экземпляр 2 приложения	
	![][5]

6. В экземпляре 1 приложения введите отличающееся значение для последнего текстового свойства и нажмите **ВВОД**. Приложение попытается обновить базу данных неправильным свойством `__version`.

	Экземпляр 1 приложения	
	![][13]

	Экземпляр 2 приложения	
	![][14]

7. Обратите внимание, что в приложении не было обнаружено исключение, поскольку серверный скрипт разрешил конфликт, позволив обновление, поскольку элемент не помечен как завершенный. Чтобы увидеть, что обновление действительно выполнено успешно, нажмите кнопку **Обновить** в экземпляре 2 для отправки повторного запроса к базе данных.

	Экземпляр 1 приложения	
	![][15]

	Экземпляр 2 приложения	
	![][15]

8. В экземпляре 1 установите флажок для завершения последнего элемента TodoItem.

	Экземпляр 1 приложения	
	![][16]

	Экземпляр 2 приложения	
	![][15]

9. В примере 2 попытайтесь обновить текст последнего элемента и нажмите клавишу **ВВОД**. Возникнет конфликт, так как элемент был обновлен, а в поле complete задано значение true. В ответ на конфликт сценарий разрешил его, отказавшись от обновления, потому что элемент уже завершен. Скрипт предоставляет сообщение в ответе.  

	Экземпляр 1 приложения	
	![][17]

	Экземпляр 2 приложения	
	![][18]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показано, как сделать так, чтобы приложение для Магазина Windows обрабатывало конфликты записи при работе с данными в мобильных службах. Далее проработайте один из следующих учебников в серии, посвященной работе с данными:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Использование скриптов сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

После завершения серии, посвященной работе с данными, можно также пройти один из следующих учебников по Магазину Windows:

* [Приступая к работе с аутентификацией] 
  <br/>Проверка подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение с помощью мобильных служб.
 
<!-- Anchors. -->
[Обновите приложение, чтобы разрешить обновления]: #uiupdate
[Включите в приложении обнаружение конфликтов]: #enableOC
[Протестируйте в приложении конфликты записи базы данных]: #test-app
[Автоматическое разрешение конфликтов в серверных скриптах]: #scriptsexample
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Управление оптимистичным параллелизмом]: http://go.microsoft.com/fwlink/?LinkId=330935
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Учетная запись Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/
[Проверка и изменение данных с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Свойства системы]: http://go.microsoft.com/fwlink/?LinkId=331143
[Начало работы с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
