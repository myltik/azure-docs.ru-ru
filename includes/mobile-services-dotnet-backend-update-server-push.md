1. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте TodoItemController.cs и обновите определение метода `PostTodoItem` с помощью следующего кода:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            WindowsPushMessage message = new WindowsPushMessage();
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Этот код отправит push-уведомление (с текстом вставленного элемента) после вставки элемента списка дел. В случае возникновения ошибки код добавит запись в журнал ошибок, которую можно просмотреть на вкладке **Журналы** мобильной службы на портале управления.




