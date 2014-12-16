
Всегда рекомендуется проверять длину данных, отправляемых пользователям. В этом разделе в мобильную службу будет добавлен код, который проверяет длину строки данных, отправленной в мобильную службу, и отклоняет слишком длинные строки; в этом случае строки длиной более 10 знаков.

1. Запустите Visual Studio через пункт **Запуск от имени администратора**, а затем откройте решение, содержащее проект мобильной службы, с которым вы работали в учебниках [Приступая к работе] или [Приступая к работе с данными](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).

2. В окне обозревателя решений разверните проект службы списка дел и разверните элемент **Contollers** Откройте файл TodoItemController.cs, который является частью проекта мобильной службы.  

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)

3. Замените метод "PostTodoItem" следующим методом, который будет определять, что текстовая строка не превышает 10 символов. Для элементов, которые имеют длину более 10 символов текста, этот метод возвращает код 400 ("Ошибка запроса состояния HTTP") с описательным сообщением, включенным в качестве содержимого.


        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }



4. Щелкните правой кнопкой мыши проект службы и нажмите кнопку **Создать** для построения проекта мобильной службы. Убедитесь в отсутствии ошибок.

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)

5. Щелкните правой кнопкой проект службы и выберите **Опубликовать**. Выполните публикацию мобильной службы для учетной записи Microsoft Azure с параметрами публикации, использованными ранее в учебниках [Приступая к работе] или [Приступая к работе с данными](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).
 
     >[WACOM.NOTE]  Кроме того, можно выполнить тестирование с помощью службы, размещенной локально в IIS Express. Дополнительные сведения см. в учебнике [Приступая к работе с данными](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)





<!-- URLs. -->
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
