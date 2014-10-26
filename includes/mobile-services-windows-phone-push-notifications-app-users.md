Затем вам необходимо изменить способ регистрации push-уведомлений, чтобы аутентификация пользователя проводилась до попытки регистрации.

1.  В Visual Studio в обозревателе решений откройте файл проекта app.xaml.cs и в обработчике событий **Application\_Launching** закомментируйте или удалите вызов в метод **AcquirePushChannel**.

2.  Измените доступность метода **AcquirePushChannel** с `private` на `public` и добавьте модификатор `static`.

3.  Откройте файл проекта MainPage.xaml.cs и переопределите метод **OnNavigatedTo** с помощью следующего кода:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }


