

Теперь, когда проверка подлинности необходима для доступа к данным в таблице TodoItem, можно использовать значение userID, назначенное мобильными службами для фильтрации возвращаемых данных.

>[AZURE.NOTE]В приведенных ниже методах атрибут **AuthorizeLevel** должен быть применен к **Authorizationlevel** для **User**. Это ограничивает доступ к таблице только пользователями, прошедшими проверку подлинности.

1. В Visual Studio 2013 откройте проект мобильной службы, разверните папку DataObjects, а затем откройте файл проекта TodoItem.cs.

	Класс TodoItem определяет объект данных, и вам необходимо добавить свойство UserId для использования фильтрации.

2. Добавьте следующее новое свойство UserId в класс **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] В случае использования инициализатора базы данных по умолчанию Entity Framework будет удалять и заново создавать базу данных при каждом обнаружении изменения модели данных в определении модели Code First. Чтобы сделать это изменение модели данных и сохранить существующие данные в базе данных, необходимо использовать Code First Migrations. Нельзя использовать инициализатор по умолчанию для базы данных SQL в Azure. Дополнительную информацию см. в разделе [Как использовать Code First Migrations для обновления модели данных](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

3. В обозревателе решений разверните папку Controllers, откройте файл проекта TodoItemController.cs и добавьте следующий оператор **using**:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	Класс **TodoItemController** реализует доступ к данным для таблицы TodoItem. 
 
4. Найдите метод **PostTodoItem** и добавьте следующий код в его начало:

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    Этот код добавляет в элемент значение UserId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem. 
	

5. Найдите метод **GetAllTodoItems** и замените в нем оператор **return** следующей строкой кода:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	Этот запрос фильтрует возвращаемые объекты TodoItem таким образом, чтобы каждый пользователь получил только элементы, которые он вставил. 

6. повторно опубликовать проект мобильной службы в Azure.

<!--HONumber=42-->
