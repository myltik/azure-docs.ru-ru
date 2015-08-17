В проекте сервера, скачанном с портала, уже включена аутентификация.

В проекте ASP.NET вы увидите следующее:

* Пакет NuGet `Microsoft.Azure.Mobile.Server.Authentication` установлен.

* В WebApiConfig.cs метод `UseDefaultConfiguration()` вызывается для объекта MobileAppConfiguration. Таким образом вызывается метод расширения `AddAppServiceAuthentication()`, предоставленный пакетом NuGet, упомянутым выше. Он также регистрирует ПО промежуточного слоя OWIN, необходимое для аутентификации путем вызова `app.UseAppServiceAuthentication()` при запуске OWIN.

<!---HONumber=August15_HO6-->