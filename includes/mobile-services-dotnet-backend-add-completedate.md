В этом разделе мы будем изменять модель нашей базы данных посредством добавления нового поля timestamp с именем **CompleteDate**. В этом поле будет записано последнее время, когда был завершен элемент todo. Entity Framework будет обновлять базу данных на основании изменений нашей модели, используя класс инициализатора базы данных по умолчанию, производный от [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621). 

1. В обозревателе решений для Visual Studio разверните папку **App_Start** в проекте службы todolist. Откройте файл WebApiConfig.cs.

2. В файле WebApiConfig.cs обратите внимание, что класс инициализатора базы данных по умолчанию является производным от класса  `DropCreateDatabaseIfModelChanges`. Это означает, что любое изменение модели приведет в таблице к его удалению и повторному созданию в соответствии с новой моделью. Таким образом данные в таблице будут потеряны, а таблица будет повторно заполнена. Измените метод Seed инициализатора базы данных таким образом, чтобы заполняемые данные были такими же, как сохраненные в файле WebApiConfig.cs.

    >[AZURE.NOTE] В случае использования инициализатора базы данных по умолчанию Entity Framework будет удалять и заново создавать базу данных при каждом обнаружении изменения модели данных в определении модели Code First. Чтобы сделать это изменение модели данных и сохранить существующие данные в базе данных, необходимо использовать Code First Migrations. Дополнительную информацию см. в разделе [Как использовать Code First Migrations для обновления модели данных](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. В обозревателе решений для Visual Studio разверните папку **DataObjects** в проекте службы todolist. Откройте файл TodoItem.cs и обновите класс TodoItem для включения поля CompleteDate следующим образом. Затем сохраните файл TodoItem.cs.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. В обозревателе решений для Visual Studio разверните папку **Controllers** в проекте службы todolist. Откройте файл TodoItemController.cs и обновите метод  `PatchTodoItem` таким образом, чтобы он устанавливал значение **CompleteDate**, когда свойство **Complete** меняет значение с false на true. Затем сохраните файл TodoItemController.cs.

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. Повторите сборку проекта серверной службы .NET списка дел и убедитесь в отсутствии ошибок сборки. 

Затем вы обновите клиентское приложение для отображения новых данных **CompleteDate**.
\<!--HONumber=42-->
