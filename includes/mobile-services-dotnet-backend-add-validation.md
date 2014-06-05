
Рекомендуется всегда проверять длину данных, предоставляемых пользователями. В этом разделе вы добавите код в мобильную службу, проверяющий длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1. Запустите Visual Studio с параметром **Запуск от имени администратора**, а также откройте решение, содержащее проект мобильной службы, с которым вы работали в учебниках [Приступая к работе] или [Приступая к работе с данными].

2. В окне обозревателя решений разверните проект службы списка дел и разверните элемент **Contollers** Откройте файл TodoItemController.cs, который является частью проекта мобильной службы.  

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)

3. Замените метод `PostTodoItem` следующим методом, который будет проверять, что текстовая строка не превышает 10 символов. Для элементов, которые имеют длину более 10 символов текста, этот метод возвращает код 400 ошибочного запроса состояния HTTP с описательным сообщением, включенным в качестве содержимого.


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



4. Щелкните правой кнопкой мыши проект службы и нажмите кнопку **Создать** для построения проекта мобильной службы. Убедитесь, что ошибок нет.

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)

5. Щелкните правой кнопкой проект службы и выберите **Опубликовать**. Публикация мобильной службы для учетной записи Microsoft Azure с помощью параметров публикации, использованных ранее в учебниках [Приступая к работе] или [Приступая к работе с данными].
 
     >[WACOM.NOTE] Кроме того, можно выполнить тестирование с помощью службы, размещенной локально в IIS Express. Дополнительные сведения см. в учебнике [Приступая к работе с данными].

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)





<!-- URLs. -->
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-

