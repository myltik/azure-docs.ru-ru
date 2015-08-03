В проекте сервера, скачанном с портала, уже включены функции обработки данных.

В проекте ASP.NET вы увидите следующее:

* Пакеты NuGet `Microsoft.Azure.Mobile.Server.Tables` и `Microsoft.Azure.Mobile.Server.Entity` установлены.

* В WebApiConfig.cs метод `UseDefaultConfiguration()` вызывается для объекта MobileAppConfiguration. Таким образом вызывается метод расширения `AddTablesWithEntityFramework()`, предоставленный пакетом NuGet, упомянутым выше.

<!---HONumber=July15_HO4-->