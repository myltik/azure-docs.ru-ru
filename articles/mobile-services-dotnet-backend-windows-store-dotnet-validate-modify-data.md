<properties urlDisplayName="Validate and Modify Data" pageTitle="Использование серверной части .Net для проверки и изменения данных (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как проверять, изменять и улучшать данные для приложения Магазина Windows с помощью мобильных служб Microsoft Azure с серверной частью .NET." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Проверка и изменение данных в мобильных службах с помощью серверной части .NET

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать код в мобильных службах Azure на основе .NET для проверки и изменения данных. Серверная служба .NET - это HTTP-служба, созданная с помощью платформы Web API и Entity Framework. Если вы знакомы с классом `ApiController`, определенным с помощью платформы Web API, класс `TableController`, предоставляемый мобильными службами, будет вам понятен. `TableController` является производным от класса `ApiController` и предоставляет дополнительные возможности для взаимодействия с таблицей базы данных. Ее можно использовать для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных, что показано в этом учебнике. 

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Проверка длины теста]
4. [Добавление метки времени для CompleteDate]
5. [Обновление клиента для отображения CompleteDate]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника [Приступая к работе] или [Приступая к работе с данными]. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе] или [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Add validation

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Update the client

Теперь, когда мобильная служба настроена для проверки данных и отправки сообщений об ошибках при недопустимой длине текста, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки. Ошибка будет перехвачена как исключение MobileServiceInvalidOperationException из вызова метода IMobileServiceTable<TodoItem].InsertAsync() в клиентском приложении.

1. В обозревателе решений в Visual Studio перейдите в проект клиентского приложения и откройте файл MainPage.xaml.cs. Добавьте следующие инструкции **using** в этот файл:

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2. В файле MainPage.xaml.cs замените существующий метод **InsertTodoItem** следующим кодом.

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

   	Эта версия метода включает обработку ошибок для исключения **MobileServiceInvalidOperationException**, которое отображает десериализованное сообщение об ошибке из содержимого ответа в поле сообщения.

## <a name="test-length-validation"></a>Проверка длины теста

1. В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект клиентского приложения и выберите пункт **Назначить запускаемым проектом**. Затем нажмите клавишу **F5**, чтобы запустить приложение, в котором служба размещается локально в IIS Express.

2. Введите текст нового элемента todo длиной более 10 знаков, затем нажмите кнопку **Сохранить**.

    ![][1]

3. В ответ на ввод недопустимого текста появится следующее диалоговое окно.

    ![][2]

## <a name="add-timestamp"></a>Add a timestamp field for CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]




## <a name="update-client-timestamp"></a>Update the client to display the CompleteDate

Последний шаг - обновление клиента с целью отображения новых данных **CompleteDate**. 


1. В обозревателе решений в Visual Studio откройте проект клиентского приложения ToDoList, откройте файл MainPage.xaml и замените в нем элемент **CheckBoxComplete** показанным ниже определением. Затем сохраните файл. При этом обработчик событий элемента **CheckBoxComplete** изменится так, чтобы можно было обработать событие "click". Кроме того, мы добавляем блок текста рядом с флажком и привязываем его к временной метке даты завершения.
	      
        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>


2. В обозревателе решений Visual Studio в проекте клиентского приложения TodoList откройте файл MainPage.xaml.cs, замените обработчик событий `CheckBoxComplete_Checked` следующим обработчиком `CheckBoxComplete_Clicked`. Так мы сможем увидеть дату выполнения элемента списка.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


2. Затем в файле MainPage.xaml.cs замените существующий класс **TodoItem** на следующее определение, которое включает в себя новое свойство **CompleteDate** как тип, допускающий значение NULL.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }
	
    >[WACOM.NOTE]  Атрибут <code>DataMemberAttribute</code> указывает клиенту, что нужно сопоставить новое свойство <code>CompleteDate</code> в приложении со столбцом <code>CompleteDate</code>, определенным в таблице TodoItem. С помощью этого атрибута ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL.
    

	


4. В файле MainPage.xaml.cs удалите или закомментируйте функцию предложения `.Where` в существующем методе **RefreshTodoItems**, чтобы включить завершенные элементы todoitem в результаты.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. В файле MainPage.xaml.cs обновите метод **UpdateCheckedTodoItem**, как показано ниже, чтобы элементы обновлялись после изменения, а завершенные элементы не удалялись из списка. Затем сохраните файл.	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }


6. В окне обозревателя решений в Visual Studio щелкните правой кнопкой мыши **Решение** и выберите команду **Перестроить решение** для перестройки клиента и серверной службы .NET. Убедитесь, что оба проекта были построены без ошибок.

    ![][3]
	
7. Нажмите клавишу **F5**, чтобы запустить клиентское приложение и службу локально. Добавьте новые элементы и отметьте некоторые как завершенные, чтобы убедиться, что метка времени **CompleteDate** обновляется.

    ![][4]

8. В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект службы todolist и выберите **Опубликовать**. Опубликуйте службу .NET в Microsoft Azure с помощью файла настроек публикации, загруженного с портала Azure.

9. Обновите файл App.xaml.cs для проекта клиентского приложения, раскомментировав подключение к адресу мобильной службы. Проверьте приложение на серверной части .NET, размещенной в вашей учетной записи Azure.




## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром].

Серверный скрипт также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей на стороне службы]
  <br/>Фильтрация данных с учетом идентификатора пользователя, прошедшего проверку подлинности.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Проверка длины теста]: #test-length-validation
[Добавление метки времени для CompleteDate]: #add-timestamp
[Обновление клиента для отображения CompleteDate]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Авторизация пользователей на стороне службы]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
