<properties urlDisplayName="Optimistic concurrency" pageTitle="Обработка конфликтов записи базы данных с поддержкой оптимистичного параллелизма (Магазин Windows) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как обрабатывать конфликты записи в базу данных как на сервере, так и в приложении магазина Windows." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Обработка конфликтов записи базы данных

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

This tutorial is intended to help you better understand how to handle conflicts that occur when two or more clients write to the same database record in a Windows Phone 8 app. Two or more clients may write changes to the same item, at the same time, in some scenarios. Without any conflict detection, the last write would overwrite any previous updates even if this was not the desired result. Mobile Services provides support for detecting and resolving these conflicts. This topic walks you through the steps that allow you to handle database write conflicts on both the server and in your application.

In this tutorial you will add functionality to the quickstart app to handle contentions that occur when updating the TodoItem database. This tutorial walks you through these basic steps:

1. [Update the application to allow updates]
2. [Enable Conflict Detection in your application]
3. [Test database write conflicts in the application]
4. [Automatically handling conflict resolution in server scripts]


This tutorial requires the following

+ Microsoft Visual Studio 2012 Express for Windows Phone 8 or later.
+ [Windows Phone 8 SDK] running on Windows 8. 
+ [Azure Account]
+ This tutorial is based on the Mobile Services quickstart. Before you start this tutorial, you must first complete [Get started with Mobile Services].
+ Azure Mobile Services NuGet Package 1.1.0 or later. To get the latest version, follow these steps below:
	1. In Visual Studio, open the project and right-click the project in Solution Explorer then click **Manage Nuget Packages**. 

		![][13]

	2. Expand **Online** and click **Microsoft and .NET**. In the search text box enter **Azure Mobile Services**. Click **Install** on the **Azure Mobile Services** NuGet Package.

		![][14]


 

<h2><a name="uiupdate"></a>Update the application to allow updates</h2>

In this section you will update the TodoList user interface to allow updating the text of each item in a ListBox control. The ListBox will contain a CheckBox and TextBox control for each item in the database table. You will be able to update the text field of the TodoItem. The application will handle the `LostFocus` event from that TextBox to update the item in the database.


1. In Visual Studio, open the TodoList project you downloaded in the [Get started with Mobile Services] tutorial.
2. In the Visual Studio Solution Explorer, open MainPage.xaml and replace the `phone:LongListSelector` definition with the ListBox shown below and save the change.

		<ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
			<ListBox.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListBox.ItemTemplate>
		</ListBox>


2. В обозревателе решений Visual Studio откройте файл MainPage.xaml.cs и добавьте следующую директиву using:

		using System.Threading.Tasks;


3. В обозревателе решений Visual Studio откройте файл MainPage.xaml.cs. Добавьте обработчик событий MainPage для текстового поля события LostFocus, как показано ниже.


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

4. В файле MainPage.xaml.cs добавьте определение для метода UpdateToDoItem(), на который ссылается обработчик событий, как показано ниже.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

Теперь, когда текстовое окно теряет фокус, приложение снова записывает изменения текста для каждого элемента в базе данных.

<h2><a name="enableOC"></a>Включите в приложении обнаружение конфликтов</h2>

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. [Управление оптимистичным параллелизмом] предполагает, что каждая транзакция может фиксироваться и поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается. Мобильные службы Azure поддерживают управление оптимистичным параллелизмом за счет отслеживания изменений каждого элемента с помощью столбца системного свойства __version, добавляющегося в каждую таблицу. В этом разделе описано, как сделать так, чтобы приложение определяло конфликты записи с помощью системного свойства __version. При попытке обновления приложение будет получать уведомления от MobileServicePreconditionFailedException, если запись изменилась с момента последнего запроса. Затем оно сможет выбрать, зафиксировать свои изменения в базе данных или оставить без изменений последнее изменение в базе данных. Дополнительные сведения о системных свойствах мобильных служб см. в [Системные свойства]

1. В файле MainPage.xaml.cs обновите определение класса **TodoItem** с помощью приведенного ниже кода, чтобы добавить системное свойство **__version**. Благодаря этому будет включена поддержка обнаружения конфликтов записи.

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
	<pre><code>//Включить оптимистичный параллелизм извлечением __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. После добавления свойства Version к классу TodoItem приложение во время обновления будет получать уведомления об исключении MobileServicePreconditionFailedException, если запись изменилась с момента последнего запроса. Это исключение включает последнюю версию элемента с сервера. В файле MainPage.xaml.cs добавьте указанный ниже код, чтобы обрабатывать исключение в методе UpdateToDoItem().

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
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. В файле MainPage.xaml.cs добавьте определение для метода ResolveConflict(), на который ссылается UpdateToDoItem(). Обратите внимание, что для разрешения конфликта перед принятием пользователем решения необходимо в качестве версии локального элемента задать обновленную версию с сервера. В противном случае конфликт будет возникать постоянно.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>Протестируйте в приложении конфликты записи базы данных</h2>

В этом разделе будет протестирован код, обрабатывающий конфликты записи путем запуска приложения в двух различных эмуляторах Windows Phone 8 (WVGA и WVGA 512 МБ). Оба клиентских приложения будут пытаться обновить свойство text одного и того же элемента, что потребует разрешения конфликта пользователем.


1. В Visual Studio убедитесь, что в раскрывающемся списке **Эмулятор WVGA 512 МБ** выбран в качестве целевого объекта развертывания, как показано на снимке экрана ниже.

	![][0]

2. В Visual Studio выберите в меню команду **СБОРКА**, а затем - **Развернуть решение**. Если эмулятор ранее не выполнялся, эмулятору потребуется несколько минут для загрузки операционной системы Windows Phone 8. Проверьте в окне вывода в нижней части, что сборка и развертывание в эмуляторе Windows Phone 8 выполнены успешно.

	![][2]

3. В Visual Studio измените объект развертывания в поле раскрывающегося списка на **Эмулятор WVGA**.

	![][1]

4. В Visual Studio выберите в меню команду **СБОРКА**, а затем - **Развернуть решение**. Проверьте в окне вывода в нижней части, что сборка и развертывание в эмуляторе Windows Phone 8 выполнены успешно.

€
  
5. Поместите оба эмулятора таким образом, чтобы они отображались рядом друг с другом. Мы можем имитировать конфликты параллельной записи, осуществляемой клиентскими приложениями, работающими в этих эмуляторах. Проведите пальцем справа налево в обоих эмуляторах, чтобы просмотреть список установленных приложений. Перейдите к нижней части каждого списка и щелкните приложение **todolist**.

	![][3]

6. В левом эмуляторе измените значение свойства текста последнего элемента таблицы TodoItem на **Тест записи 1**, а затем щелкните другое текстовое поле, чтобы обработчик событий LostFocus обновил базу данных. На следующем снимке экрана показан пример. 

	![][4]

7. На этом этапе соответствующий элемент в правом эмуляторе имеет старую версию и старое значение текста. В правом эмуляторе введите **Тест записи 2** для свойства текста. Затем выберите другое текстовое поле, чтобы обработчик событий LostFocus в правом эмуляторе попытался обновить базу данных со старой версией.

	![][5]

8. Так как версия, использованная при попытке обновления, не соответствует версии сервера, пакет SDK для мобильных служб высылает уведомление MobileServicePreconditionFailedException, позволяющее приложению разрешить этот конфликт. Для разрешения конфликта можно нажать **ok**, чтобы зафиксировать значения из правого приложения. Можно также щелкнуть **отмена**, чтобы отменить значения в правом приложении, оставляя зафиксированными значения из левого приложения. 

	![][6]



<h2><a name="scriptsexample"></a>Автоматическое разрешение конфликтов в серверных скриптах</h2>

Можно обнаруживать и разрешать конфликты записи в скриптах сервера. Это хорошая идея - использовать записанную логику вместо взаимодействия с пользователем для разрешения конфликта. В этом разделе вы добавите скрипт со стороны сервера в таблицу TodoItem для приложения. Логика, которую будет использовать этот скрипт для разрешения конфликтов, выглядит следующим образом:

+  Если в поле complete таблицы TodoItem указано значение true, таблица считается завершенной и свойство текста больше не меняется.
+  Если в поле complete таблицы TodoItem указано значение false, будут продолжаться попытки обновить это свойство.

Дальнейшие действия описывают добавление серверного скрипта обновления и его тестирование.

1. Войдите на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение. 

   	![][7]

2. На вкладке **Данные** щелкните таблицу **TodoItem**.

   	![][8]

3. Щелкните элемент **Скрипт** и выберите операцию **Обновить**.

   	![][9]

4. Замените существующий скрипт указанной ниже функцией и нажмите кнопку **Сохранить**.

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
5. Измените текст TodoItem для последнего элемента в приложении в левом эмуляторе. Затем выберите второе текстовое поле, чтобы обработчик событий LostFocus обновил базу данных.

	![][4]

6. В правом эмуляторе введите другое значение для текстового свойства последнего элемента таблицы TodoItem. Затем выберите второе текстовое поле, чтобы обработчик событий LostFocus в правом эмуляторе попытался обновить базу данных со старой версией.

	![][5]

7. Обратите внимание, что, поскольку серверный сценарий разрешил конфликт, позволив обновление, так как соответствующий элемент не был помечен как завершенный, в приложении не было обнаружено исключение. Чтобы увидеть, что обновление выполнено действительно успешно, нажмите кнопку **Обновить** в приложении в левом эмуляторе для повторного запроса к базе данных.

	![][10]

8. В приложении в левом эмуляторе установите флажок для завершения последнего элемента TodoItem.

	![][11]

9. В приложении в правом эмуляторе попытайтесь обновить тот же текст TodoItem и вызвать событие LostFocus. В ответ на конфликт сценарий разрешил его, отказавшись от обновления, потому что элемент уже завершен. 

	![][12]


## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показано, как сделать, чтобы приложение для Windows Phone 8 обрабатывало конфликты записи при работе с данными в мобильных службах. Далее проработайте один из следующих учебников в серии, посвященной работе с данными:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения ряда данных можно попробовать выполнить один из следующих учебников по Windows Phone 8.

* [Приступая к работе с проверкой подлинности] 
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.
 
<!-- Anchors. -->
[Обновление приложения, чтобы разрешить обновления]: #uiupdate
[Включение в приложении функции обнаружения конфликтов]: #enableOC
[Тестирование конфликтов записи базы данных в приложении]: #test-app
[Автоматическое разрешение конфликтов в серверных скриптах]: #scriptsexample
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[Управление оптимистичным параллелизмом]: http://go.microsoft.com/fwlink/?LinkId=330935
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Учетная запись Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
[Приступая к работе с данными]: ./mobile-services-get-started-with-data-wp8.md
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Свойства системы]: http://go.microsoft.com/fwlink/?LinkId=331143

<!--HONumber=35.2-->
