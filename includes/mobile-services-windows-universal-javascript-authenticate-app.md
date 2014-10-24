1.  Откройте файл проекта default.js, а затем в перегрузке метода **app.OnActivated** замените последний вызов метода **refreshTodoItems** следующим кодом:

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

    Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод **login** выше, на одно из следующих: *microsoftaccount*, *twitter*, *google* или *windowsazureactivedirectory*.

    > [WACOM.NOTE]При регистрации данных пакета приложения Магазина Windows с помощью мобильных служб необходимо вызвать метод [login][login], указав значение **true** для параметра *useSingleSignOn*. Если этого не сделать, пользователям потребуется осуществлять вход в систему при каждом вызове метода для входа в систему.

2.  В проекте приложения Магазина Windows откройте файл проекта default.html и добавьте следующий элемент **button** непосредственно перед элементом, определяющим кнопку **Сохранить**:

        <button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3.  Нажмите клавишу F5 для запуска приложения и войдите в приложение с выбранным вами поставщиком удостоверений.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

  [login]: http://go.microsoft.com/fwlink/p/?LinkId=322050
