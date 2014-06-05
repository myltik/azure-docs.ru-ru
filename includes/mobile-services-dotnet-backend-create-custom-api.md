

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши папку Controllers для проекта мобильных служб, выберите **Добавить**, а затем щелкните **Новый элемент скаффолдинга**.

	Откроется диалоговое окно добавления элемента скаффолдинга.

2. Разверните **Мобильные службы Azure** и выберите **Особый контроллер мобильных служб Azure**, нажмите кнопку **Добавить**, введите **имя контроллера** для `CompleteAllController`, а затем нажмите кнопку **Добавить** еще раз.

	![](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	При этом создается новый пустой класс контроллера с именем **CompleteAllController**.

>[WACOM.NOTE]Если диалоговое окно не содержит элементов скаффолдинга, характерных для мобильных служб, вместо этого создается новый пустой **контроллер Web API - Empty**. Добавьте в этот новый класс контроллера открытое свойство **Services**, которое возвращает тип **ApiServices**. Это свойство используется для доступа к параметрам, характерным для сервера, непосредственно из вашего контроллера.

3. В новом файле проекта CompleteAllController.cs добавьте следующие операторы **using**:

		using System.Threading.Tasks;
		using todolistService.Models;

	В приведенном выше коде замените `todolistService` пространством имен вашего проекта мобильных служб, которое должно быть именем мобильной службы с добавлением `Service`. 

4. Добавьте в новый контроллер следующий код:

	    // POST api/completeall        
        public Task<int> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";
                var result = database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.ToString()));
                
                return result;
            }
        }

	В приведенном выше коде замените `todolistContext` именем DbContext для вашей модели данных, которое должно быть именем мобильной службы с добавлением `Context`. Этот код использует [класс Database](http://msdn.microsoft.com/ru-ru/library/system.data.entity.database(v=vs.113).aspx) для непосредственного доступа к таблице **TodoItems**, чтобы задать флаг завершения для всех элементов. Этот метод поддерживает запрос POST, при этом число измененных строк возвращается клиенту как целое значение.

	> [WACOM.NOTE] Устанавливаются разрешения по умолчанию, что означает, что любой пользователь приложения может вызвать настраиваемый API. Однако код приложения не распространяется или не хранится безопасно и не может считаться безопасным. Поэтому следует ограничивать доступ к операциям, которые изменяют данные или влияют на мобильную службу, только для прошедших проверку пользователей. 

Далее предстоит изменить приложение быстрого запуска, чтобы добавить новую кнопку и код, который асинхронно вызывает новый пользовательский интерфейс API.


