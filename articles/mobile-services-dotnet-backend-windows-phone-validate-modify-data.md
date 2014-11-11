<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-wp8" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Phone 8) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Phone app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Проверка и изменение данных в мобильных службах с помощью серверной части .NET

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone" class="current">Windows Phone</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Сервер .NET" class="current">Сервер .NET</a> | 
    <a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8"  title="Сервер JavaScript">Сервер JavaScript</a>
</div>

В этом разделе показано, как использовать код в мобильных службах Azure на основе .NET для проверки и изменения данных. Серверная служба .NET — это HTTP-служба, созданная с помощью платформы Web API. Если вам знаком класс `ApiController`, определенный с помощью платформы веб-API, класс `TableController`, предоставляемый мобильными службами, будет вам интуитивно понятен. `TableController` является производным от класса `ApiController` и предоставляет дополнительные возможности для взаимодействия с таблицей базы данных. Его можно использовать для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных, что показано в этом учебнике.

В этом учебнике рассматриваются следующие основные действия:

1.  [Добавление проверки длины строки][Добавление проверки длины строки]
2.  [Обновление клиента для поддержки проверки][Обновление клиента для поддержки проверки]
3.  [Проверка длины теста][Проверка длины теста]
4.  [Добавление метки времени для CompleteDate][Добавление метки времени для CompleteDate]
5.  [Обновление клиента для отображения CompleteDate][Обновление клиента для отображения CompleteDate]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе][Приступая к работе] или [Приступая к работе с данными][Приступая к работе с данными]. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе][Приступая к работе] или [Приступая к работе с данными][Приступая к работе с данными].

## <a name="string-length-validation"></a>Добавление проверки

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба настроена для проверки данных и отправки сообщений об ошибках при недопустимой длине текста, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки. Ошибка будет перехвачена как `MobileServiceInvalidOperationException` из вызова клиентского приложения в `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  В обозревателе решений в Visual Studio перейдите в проект клиентского приложения и откройте файл MainPage.xaml.cs. Добавьте в файл следующую инструкцию using.

        using Newtonsoft.Json.Linq;

2.  В файле MainPage.xaml.cs замените существующий метод **InsertTodoItem** следующим кодом:

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
            catch (MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageBox.Show(string.Format("{0} (HTTP {1})",
                    (string)responseContent["message"], (int)invalidOpException.Response.StatusCode),
                    invalidOpException.Message, MessageBoxButton.OK);
            }
        }

    Эта версия метода включает обработку ошибок для исключения **MobileServiceInvalidOperationException**, которое отображает десериализованное сообщение об ошибке из содержимого ответа в поле сообщения.

## <a name="test-length-validation"></a>Проверка длины теста

1.  В Visual Studio настройте цель развертывания Windows Phone. Затем в обозревателе решений щелкните правой кнопкой мыши проект клиентского приложения, щелкните **Отладка** и **Запустить новый экземпляр**.

2.  Введите текст для нового элемента списка дел длиной более 10 символов и нажмите кнопку **Сохранить**.

    ![][0]

3.  В ответ на ввод недопустимого текста появится следующее диалоговое окно.

    ![][1]

## <a name="add-timestamp"></a>Добавление поля метки времени для CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Обновление клиента для отображения CompleteDate

Последним шагом является обновление клиента для отображения новых данных **CompleteDate**.

1.  В обозревателе решений в Visual Studio откройте проект клиентского приложения, откройте файл MainPage.xaml и замените элемент **StackPanel** определением, показанным ниже. Затем сохраните файл. При этом обработчик событий **CheckBoxComplete** изменится, чтобы мы могли обработать событие `click`. Кроме того, мы добавляем блок текста рядом с флажком и привязываем его к временной метке даты завершения.

        <StackPanel Orientation="Horizontal">
          <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
            Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
            VerticalAlignment="Center"/>
          <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
            VerticalAlignment="Center" />
        </StackPanel>

2.  В обозревателе решений в Visual Studio в проекте клиентского приложения откройте файл MainPage.xaml.cs, замените обработчик событий `CheckBoxComplete_Checked` следующим обработчиком событий `CheckBoxComplete_Clicked`. Так мы сможем увидеть дату завершения после обработки элемента.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  В файле MainPage.xaml.cs замените существующий класс **TodoItem** следующим определением, которое включает в себя новое свойство **CompleteDate** как тип, допускающий значение NULL.

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

    > [WACOM.NOTE] `DataMemberAttribute` оповещает клиент для сопоставления нового свойства `CompleteDate` в приложении со столбцом `CompleteDate`, определенным в таблице TodoItem. С помощью этого атрибута ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL.

4.  В файле MainPage.xaml.cs удалите или закомментируйте функцию `.Where` в существующем методе **RefreshTodoItems**, чтобы включить завершенные элементы todoitem в результаты.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  В файле MainPage.xaml.cs обновите метод **UpdateCheckedTodoItem**, как показано ниже, чтобы элементы обновлялись после изменения, а завершенные элементы не удалялись из списка. Затем сохраните файл.

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  В окне обозревателя решений в Visual Studio щелкните правой кнопкой мыши **Решение** и выберите пункт **Перестроить решение**, чтобы повторить сборку клиента и серверной службы .NET. Убедитесь, что построение выполнено без ошибок.

7.  Нажмите клавишу **F5**, чтобы локально запустить клиентское приложение и службу. Добавьте новые элементы и отметьте некоторые элементы как завершенные, чтобы убедиться, что временная метка **CompleteDate** обновляется.

8.  В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект службы todolist и выберите **Опубликовать**. Опубликуйте службу .NET в Microsoft Azure с помощью файла настроек публикации, загруженного с портала Azure.

9.  Обновите файл App.xaml.cs для проекта клиентского приложения, раскомментировав подключение к адресу мобильной службы. Проверьте приложения со службой .NET, размещенной в учетной записи Azure.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов c разбиением по страницам][Уточнение запросов c разбиением по страницам].

Серверные скрипты используются также при авторизации пользователей для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

-   [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы]

    Узнайте, как фильтровать данные на основании идентификатора аутентифицированного пользователя.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Дополнительные сведения об использовании мобильных служб в .NET.



  [Добавление проверки длины строки]: #string-length-validation
  [Обновление клиента для поддержки проверки]: #update-client-validation
  [Проверка длины теста]: #test-length-validation
  [Добавление метки времени для CompleteDate]: #add-timestamp
  [Обновление клиента для отображения CompleteDate]: #update-client-timestamp
  [Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
