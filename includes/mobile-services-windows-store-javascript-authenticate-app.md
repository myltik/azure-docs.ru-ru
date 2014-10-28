1.  Откройте файл проекта default.js и в переопределении метода **app.OnActivated** замените последний вызов метода **refreshTodoItems** следующим кодом:

        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    Это создает переменную-член для хранения текущего пользователя и метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод **login** выше, на одно из следующих: *microsoftaccount*, *twitter*, *google* или *windowsazureactivedirectory*.

    > [WACOM.NOTE]При регистрации данных пакета приложения Магазина Windows с помощью мобильных служб необходимо вызвать метод [login][login], указав значение **true** для параметра *useSingleSignOn*. Если этого не сделать, пользователям потребуется осуществлять вход в систему при каждом вызове метода для входа в систему.

2.  Нажмите клавишу F5 для запуска приложения и войдите в приложение с выбранным вами поставщиком удостоверений.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

  [login]: http://go.microsoft.com/fwlink/p/?LinkId=322050
