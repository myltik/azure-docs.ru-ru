В проекте сервера, скачанном с портала, уже включены push-уведомления.

В проекте ASP.NET можно проверить следующее:

* Пакет NuGet `Microsoft.Azure.Mobile.Server.Notifications` установлен.

* В WebApiConfig.cs метод `UseDefaultConfiguration()` вызывается для объекта MobileAppConfiguration. Таким образом вызывается метод расширения `AddPushNotifications()`, предоставленный пакетом NuGet, упомянутым выше.

<!---HONumber=August15_HO8-->