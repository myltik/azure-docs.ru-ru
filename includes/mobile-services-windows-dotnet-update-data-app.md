
1. В файле MainPage.xaml.cs добавьте или раскомментируйте следующие операторы "using": 

		using Microsoft.WindowsAzure.MobileServices;

2. Замените определение класса TodoItem следующим кодом:

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	Атрибут **JsonPropertyAttribute** используется для определения сопоставления между именами свойств в типе клиента и именами столбцов в базовой таблице данных.

	>[AZURE.NOTE]В проекте универсального приложения для Windows класс TodoItem определяется в отдельном файле кода в общей папке DataModel.

3. В файле MainPage.xaml.cs закомментируйте или удалите строку, которая определяет коллекцию существующих элементов, а затем раскомментируйте или добавьте следующие строки, заменив _&lt;yourClient&gt;_ полем `MobileServiceClient`, добавленным в файл App.xaml.cs при подключении проекта к мобильной службе:

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Этот код создает коллекцию привязок (элементы) и прокси-класс для таблицы базы данных (todoTable), в которых включены мобильные службы.

4. В методе **InsertTodoItem** удалите строку кода, которая устанавливает значение свойства **TodoItem.Id**, добавьте в метод модификатор **async** и раскомментируйте следующую строку кода:

		await todoTable.InsertAsync(todoItem);


	Этот код вставляет новый элемент в таблицу.

5. Замените метод **RefreshTodoItems** следующим кодом:

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	Это позволяет установить привязку к коллекции элементов в таблице `todoTable`, которая содержит все объекты **TodoItem**, возвращенные из мобильной службы. Если при выполнении запроса возникает проблема, открывается окно с сообщением об ошибке.

6. В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

		await todoTable.UpdateAsync(item);

	В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<!---HONumber=July15_HO4-->