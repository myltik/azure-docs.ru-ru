<properties urlDisplayName="Optimistic concurrency" pageTitle="Обрабатывайте конфликты записи базы данных с поддержкой оптимистичного параллелизма (магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Обработка конфликтов записи базы данных

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#" class="current">C# в Магазине Windows</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">JavaScript в Магазине Windows</a>
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	


Этот учебник поможет вам лучше понять, как обрабатывать конфликты, возникающие в приложениях Магазина Windows, когда несколько клиентов пытаются выполнить запись в один и тот же элемент базы данных. В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. Мобильные службы Azure предоставляют поддержку для обнаружения и разрешения таких конфликтов. В этом разделе описываются шаги, которые позволят обрабатывать конфликты записи базы данных как на сервере, так и в приложении.

В этом учебнике вы добавите функции в приложение быстрого запуска для обработки конфликтов, возникающих при обновлении базы данных TodoItem. В этом учебнике рассматриваются следующие основные действия:

1. [Обновите приложение, чтобы разрешить обновления]
2. [Включите в приложении обнаружение конфликтов]
3. [Протестируйте в приложении конфликты записи базы данных]
4. [Автоматическое разрешение конфликтов в серверных скриптах]


Для работы с данным учебником требуется следующее

+ Microsoft Visual Studio 2012 Express для Windows или более поздней версии.
+ Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 
+ [Учетная запись Azure]
+ Azure Mobile Services NuGet Package 1.1.0 или более поздняя версия. Для получения последней версии выполните следующие действия.
	1. Откройте проект в Visual Studio и щелкните правой кнопкой мыши проект в обозревателе решений, а затем щелкните  **Управление пакетами NuGet**. 

		![][19]

	2. Разверните **В сети** и нажмите **Microsoft и .NET**. В поле "Поиск" введите  **Azure Mobile Services**. Нажмите **Установка** в пакете NuGet **мобильных служб Azure**.

		![][20]


 

<h2><a name="uiupdate"></a>Обновите приложение, чтобы разрешить обновления</h2>

В этом разделе вы обновите пользовательский интерфейс TodoList, что позволит обновлять текст для каждого элемента в элементе управления "поле со списком". Поле со списком будет содержать поле флажка и текстовое поле для каждого элемента в таблице базы данных. Можно будет обновить текстовое поле TodoItem. Приложение будет обрабатывать событие `LostFocus` из этого текстового поля для обновления элемента в базе данных.


1. Откройте в Visual Studio проект, который вы загрузили в учебнике [Приступая к работе с мобильными службами].
2. В обозревателе решений Visual Studio откройте MainPage.xaml и замените определение `ListView` на `ListView`, приведенный ниже, после чего сохраните изменения.

		<ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
			<ListView.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListView.ItemTemplate>
		</ListView>


3. В MainPage.xaml.cs добавьте следующую директиву `using` в верхнюю часть страницы.

		using System.Threading.Tasks;


4. В обозревателе решений Visual Studio откройте MainPage.xaml.cs. Добавьте обработчик событий в MainPage для события текстового поля `LostFocus`, как показано ниже.


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. В MainPage.xaml.cs добавьте определение для метода MainPage `UpdateToDoItem()`, на который ссылается обработчик событий, как показано ниже.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

Теперь, когда текстовое окно теряет фокус, приложение снова записывает изменения текста для каждого элемента в базе данных.

<h2><a name="enableOC"></a>Включите в приложении обнаружение конфликтов</h2>

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. [При управлении оптимистичным параллелизмом] предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается. Мобильные службы Azure поддерживают управление оптимистичным параллелизмом путем отслеживания изменений каждого элемента с помощью столбца системного свойства `__version`, который добавляется к каждой таблице. В этом разделе мы сделаем так, чтобы приложение определяло эти конфликты записи с помощью системного свойства `__version`. Приложение будет получать уведомления от `MobileServicePreconditionFailedException` во время попытки обновления, если запись была изменена с момента последнего запроса. Затем оно сможет выбрать, зафиксировать свои изменения в базе данных или оставить без изменений последнее изменение в базе данных. Дополнительные сведения о системных свойствах мобильных служб см. в [Системные свойства].

1. В MainPage.xaml.cs обновите определение класса **TodoItem** с помощью следующего кода, чтобы включить в него системное свойство __version, обеспечивающее обнаружение конфликтов записи.

		public class TodoItem
		{
			public string Id { get; set; }			
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }			
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }			
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	<div class="dev-callout"><strong>Примечание.</strong>
	<p>При использовании нетипизированных таблиц включите оптимистичный параллелизм, добавив флаг "Версия" в системные свойства таблицы.</p>
	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. После добавления свойства `Версия` к классу `TodoItem` приложение во время обновления будет получать уведомления об исключении `MobileServicePreconditionFailedException`, если запись была изменена с момента последнего запроса. Это исключение включает последнюю версию элемента с сервера. В MainPage.xaml.cs добавьте следующий код, чтобы обрабатывать исключение в методе `UpdateToDoItem()`.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }


3. В MainPage.xaml.cs добавьте определение для метода `ResolveConflict()`, на который ссылается `UpdateToDoItem()`. Обратите внимание, что для разрешения конфликта перед принятием пользователем решения необходимо в качестве версии локального элемента задать обновленную версию с сервера. В противном случае конфликт будет возникать постоянно.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);			
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;				
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };			
            ServerBtn.Invoked = async (IUICommand command) =>
            {
				RefreshTodoItems();
            };			
            await msgDialog.ShowAsync();
        }



<h2><a name="test-app"></a>Протестируйте в приложении конфликты записи базы данных</h2>

В этом разделе вы построите пакет приложения для Магазина Windows, чтобы устанавливать приложение на втором компьютере или виртуальной машине. Затем вы выполните приложение на обоих компьютерах, создавая конфликт записи, чтобы проверить код. Оба экземпляра приложения будут пытаться обновить свойство `text` одного и того же элемента, что потребует от пользователя разрешения конфликта.


1. Создайте пакет приложения для Магазина Windows, чтобы устанавливать приложение на втором компьютере или виртуальной машине. Чтобы сделать это, нажмите в Visual Studio **Проект**->**Магазин**->**Создать пакеты приложения**.

	![][0]

2. На экране "Создать свои пакеты" щелкните **Нет**, поскольку этот пакет не будет загружен в Магазин Windows. Затем нажмите **Далее**.

	![][1]

3. На экране "Выбор и настройка пакетов" подтвердите значения по умолчанию и нажмите **Создать**.

	![][10]

4. На экране "Выбор и настройка пакетов" примите значения по умолчанию и нажмите **Расположение вывода**, чтобы открыть место расположения пакета.

   	![][11]

5. Скопируйте папку пакета, "todolist_1.0.0.0_AnyCPU_Debug_Test", на второй компьютер. На этом компьютере откройте папку пакета и щелкните правой кнопкой мыши скрипт PowerShell **Add-AppDevPackage.ps1** и нажмите кнопку **Запуск с помощью PowerShell**, как показано ниже. Следуйте инструкциям на экране для установки приложения.

	![][12]
  
5. Запустите экземпляр 1 приложения в Visual Studio, нажав **Отладка**->**Начать отладку**. На экране запуска второго компьютера щелкните стрелку вниз, чтобы увидеть "Приложения по имени". Затем щелкните приложение **todolist** для запуска экземпляра 2 приложения. 

	Экземпляр 1 приложения	
	![][2]

	Экземпляр 2 приложения	
	![][2]


6. В экземпляре 1 приложения обновите текст последнего элемента до **Тест записи 1**, затем нажмите другое текстовое поле так, чтобы обработчик событий "LostFocus" обновил базу данных. На следующем снимке экрана показан пример.
	
	Экземпляр 1 приложения	
	![][3]

	Экземпляр 2 приложения	
	![][2]

7. На этом этапе последний элемент в экземпляре 2 приложения содержит старую версию элемента. В этом экземпляре приложения введите **Тест записи 2** для свойства `текст`. Затем выберите другое текстовое поле, чтобы обработчик событий `LostFocus` попытался обновить базу данных со старым свойством `_version`.

	Экземпляр 1 приложения	
	![][4]

	Экземпляр 2 приложения	
	![][5]

8. Так как значение `__version`, использованное в попытке обновления, не соответствует значению `__version` сервера, пакет SDK для мобильных служб высылает уведомление `MobileServicePreconditionFailedException`, которое позволит приложению разрешить этот конфликт. Чтобы разрешить конфликт, нажмите **Принять локальный текст** для сохранения значений из экземпляра 2. Или же нажмите **Оставить текст сервера** для отклонения значений в экземпляре 2 и использования значений из экземпляра 1 приложения. 

	Экземпляр 1 приложения	
	![][4]

	Экземпляр 2 приложения	
	![][6]



<h2><a name="scriptsexample"></a>Автоматическое разрешение конфликтов в серверных скриптах</h2>

Можно обнаруживать и разрешать конфликты записи в скриптах сервера. Это хорошая идея - использовать записанную логику вместо взаимодействия с пользователем для разрешения конфликта. В этом разделе вы добавите скрипт со стороны сервера в таблицу TodoItem для приложения. Логика, которую будет использовать этот скрипт для разрешения конфликтов, выглядит следующим образом.

+  Если для поля ` Завершено` таблицы TodoItem установлено значение true, таблица считается завершенной и свойство `text` больше не может быть изменено.
+  Если поле ` Завершено` таблицы TodoItem по-прежнему имеет значение false, то попытки обновить свойство `text` будут продолжаться.

Дальнейшие действия описывают добавление серверного скрипта обновления и его тестирование.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

   	![][7]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][8]

3. Нажмите **Скрипт** и выберите операцию **Обновить**.

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
5. Запустите приложение **todolist** на обоих компьютерах. Измените TodoItem `text` для последнего элемента в экземпляре 2. Затем нажмите другое текстовое поле, чтобы обработчик событий `LostFocus` обновил базу данных.

	Экземпляр 1 приложения	
	![][4]

	Экземпляр 2 приложения	
	![][5]

6. В экземпляре 1 приложения введите отличающееся значение для последнего текстового свойства. Затем выберите другое текстовое поле, чтобы обработчик событий `LostFocus` попытался обновить базу данных неправильным свойством `__version`.

	Экземпляр 1 приложения	
	![][13]

	Экземпляр 2 приложения	
	![][14]

7. Обратите внимание, что в приложении не было обнаружено исключение, поскольку серверный скрипт разрешил конфликт, позволив обновление, поскольку элемент не помечен как завершенный. Чтобы увидеть, что обновление действительно выполнено успешно, нажмите **Обновить** в экземпляре 2 для повторного запроса к базе данных.

	Экземпляр 1 приложения	
	![][15]

	Экземпляр 2 приложения	
	![][15]

8. В экземпляре 1 установите флажок для завершения последнего элемента TodoItem.

	Экземпляр 1 приложения	
	![][16]

	Экземпляр 2 приложения	
	![][15]

9. В экземпляре 2 попытайтесь обновить текст последнего элемента TodoItem и вызвать событие `LostFocus`. В ответ на конфликт сценарий разрешил его, отказавшись от обновления, потому что элемент уже завершен. 

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
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Управление оптимистичным параллелизмом]: http://go.microsoft.com/fwlink/?LinkId=330935
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Учетная запись Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Свойства системы]: http://go.microsoft.com/fwlink/?LinkId=331143
