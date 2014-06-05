

Теперь, когда проверка подлинности необходима для доступа к данным в таблице TodoItem, можно использовать значение userID, назначенное мобильными службами для фильтрации возвращаемых данных.

>[WACOM.NOTE]Методы ниже должны иметь атрибут **RequiresAuthorizationAttribute**, применяемый для **пользователя** **Authorizationlevel** Это ограничивает доступ к таблице только пользователями, прошедшими проверку подлинности.

1. В Visual Studio 2013 откройте проект мобильной службы, разверните папку DataObjects, а затем откройте файл проекта TodoItem.cs.

	Класс TodoItem определяет объект данных, и вам необходимо добавить свойство UserId для использования фильтрации.

2. Добавьте следующее новое свойство UserId в класс **TodoItem**:

		public string UserId { get; set; }

	>[WACOM.NOTE] Когда используется инициализатор базы данных по умолчанию, Entity Framework будет удалять и заново создавать базу данных всякий раз при обнаружении изменения модели данных в определении модели Code First. Чтобы сделать это изменение модели данных и сохранить существующие данные в базе данных, необходимо использовать Code First Migrations. Нельзя использовать инициализатор по умолчанию для базы данных SQL в Azure. Дополнительные сведения см. в разделе [Как использовать Code First Migrations для обновления модели данных](/ru-ru/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).

3. В обозревателе решений разверните папку Controllers, откройте файл проекта TodoItemController.cs и добавьте следующую инструкцию **using**:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	Класс **TodoItemController** реализует доступ к данным для таблицы TodoItem. 
 
4. Найдите метод **PostTodoItem** и добавьте следующий код в конец перед инструкцией **return**:

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    Этот код добавляет в элемент значение UserId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem. 
	

5. В методе **GetAllTodoItems** найдите и замените имеющуюся инструкцию **return** на следующую строку кода:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	Этот запрос фильтрует возвращаемые объекты TodoItem таким образом, чтобы каждый пользователь получил только элементы, которые он вставил. Вы можете при необходимости 

6. Повторно опубликовать проект мобильной службы в Azure.

