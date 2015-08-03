В проекте сервера, скачанном с портала, уже включена отправка.

В проекте ASP.NET вы увидите следующее:

* Пакет NuGet `Microsoft.Azure.Mobile.Server.Notifications` установлен.

* В WebApiConfig.cs метод `UseDefaultConfiguration()` вызывается для объекта MobileAppConfiguration. Таким образом вызывается метод расширения `AddPushNotifications()`, предоставленный пакетом NuGet, упомянутым выше.

<!---HONumber=July15_HO4-->