

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши папку Controllers для проекта мобильной службы, выберите пункт **Добавить**, а затем щелкните **Новый элемент скаффолдинга**.

	Откроется диалоговое окно добавления элемента скаффолдинга.

2. Разверните **Мобильные службы Azure** и выберите элемент **Настраиваемый контроллер мобильных служб Azure**, нажмите кнопку **Добавить**, введите в поле **Имя контроллера** значение  `CompleteAllController`, а затем еще раз нажмите кнопку **Добавить**.

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	При этом создается новый пустой класс контроллера с именем **CompleteAllController**.

>[AZURE.NOTE]Если диалоговое окно не содержит элементов скаффолдинга, характерных для мобильных служб, вместо этого создайте новый пустой **контроллер веб-API**. Добавьте в этот новый класс контроллера открытое свойство **Services**, которое возвращает тип **ApiServices**. Это свойство используется для доступа к параметрам, характерным для сервера, непосредственно из вашего контроллера.

3. В новом файле проекта CompleteAllController.cs добавьте следующие операторы **using**:

		using System.Threading.Tasks;
		using todolistService.Models;

	В приведенном выше коде замените  `todolistService` пространством имен своего проекта мобильных служб, которое должно быть именем мобильной службы с добавлением  `Service`. 

4. В файле CompleteAllController.cs добавьте в пространство имен следующее определение класса. Этот класс служит оболочкой для ответа, отправляемого клиенту.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. Добавьте в новый контроллер следующий код:

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

	В приведенном выше коде замените  `todolistContext` именем DbContext для своей модели данных, которое должно быть именем мобильной службы с добавлением  `Context`. Также замените имя схемы в инструкции UPDATE именем своей мобильной службы. 

	Этот код использует [класс Database](http://msdn.microsoft.com/ru-ru/library/system.data.entity.database.aspx) для непосредственного доступа к таблице **TodoItems**, чтобы задать флаг завершения для всех элементов. Этот метод поддерживает запрос POST, при этом число измененных строк возвращается клиенту как целое значение.

	> [AZURE.NOTE] Устанавливаются разрешения по умолчанию. Это означает, что любой пользователь приложения может вызвать настраиваемый API. Однако код приложения не распространяется или не хранится безопасно и не может считаться безопасным. Поэтому следует ограничивать доступ к операциям, которые изменяют данные или влияют на мобильную службу, только для прошедших проверку пользователей. 

Далее предстоит изменить приложение быстрого запуска, чтобы добавить новую кнопку и код, который асинхронно вызывает новый пользовательский интерфейс API.


<!--HONumber=42-->
