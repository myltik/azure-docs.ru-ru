
##<a name="update-app"></a>Обновление приложения для вызова настраиваемого интерфейса API

1. В Visual Studio откройте файл MainPage.xaml в проекте быстрого запуска, найдите элемент **Button** с именем `ButtonRefresh` и замените его следующим кодом XAML: 

		<StackPanel Orientation="Horizontal">
	        <Button Margin="72,0,0,0" Name="ButtonRefresh" 
	                Click="ButtonRefresh_Click">Refresh</Button>
	        <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
	                Click="ButtonCompleteAll_Click">Complete All</Button>
	    </StackPanel>

	Это приведет к добавлению на страницу новой кнопки.

2. Откройте файл кода MainPage.xaml.cs и добавьте следующий код определения класса:

	    public class MarkAllResult
	    {
	        public int Count { get; set; }
	    }

	Этот класс используется для хранения значения счетчика строк, возвращаемого настраиваемым API.

3. Найдите метод **RefreshTodoItems** в классе **MainPage** и убедитесь, что `query` определен с помощью следующего метода **Where**:

        .Where(todoItem => todoItem.Complete == false)

	Это приводит к фильтрации элементов, так что завершенные элементы не возвращаются в результатах запроса.

3. Добавьте в класс **MainPage** следующий метод:

		private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
		{
		    string message;
		    try
		    {
		        // Asynchronously call the custom API using the POST method. 
		        var result = await App.MobileService
		            .InvokeApiAsync<MarkAllResult>("completeAll", 
		            System.Net.Http.HttpMethod.Post, null);
		        message =  result.Count + " item(s) marked as complete.";
		        RefreshTodoItems();
		    }
		    catch (MobileServiceInvalidOperationException ex)
		    {
		        message = ex.Message;                
		    }
		
		    var dialog = new MessageDialog(message);
		    dialog.Commands.Add(new UICommand("OK"));
		    await dialog.ShowAsync();
		}

	Этот метод обрабатывает событие **Click** для новой кнопки. Метод [InvokeApiAsync](http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx) вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому интерфейсу API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки.

## <a name="test-app"></a>Тестирование приложения

1. В Visual Studio нажмите клавишу **F5**, чтобы повторить сборку проекта, после чего запустите приложение.

2. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

3. Повторяйте предыдущий шаг, пока в список не будут добавлены несколько элементов списка дел.

4. Нажмите кнопку **Выполнить все**.

  	![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)

	Отображается диалоговое окно сообщения, в котором указывается количество элементов, помеченных как завершенные; снова выполняется отфильтрованный запрос, что приводит к удалению из списка всех элементов.

<!--HONumber=54-->