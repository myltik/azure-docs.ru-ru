<properties 
	pageTitle="Добавление мобильных служб в существующее универсальное приложение Windows | Мобильные службы Azure" 
	description="Узнайте, как подключать существующие универсальные приложения Windows к мобильным службам Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Обзор

В этом разделе показано, как применять мобильные службы Azure для эффективного использования данных в универсальном приложении Windows. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений Windows для Windows и Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для универсального приложения Windows, которое хранит данные в памяти, создать мобильную службу, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

>[AZURE.NOTE]В этом разделе поясняется, как использовать средства Visual Studio Professional 2013 с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Эти же инструкции можно использовать для подключения мобильной службы к приложению Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как подключить мобильную службу к приложению Windows Phone 8.0 или Windows Phone Silverlight 8.1, см. в разделе [Приступая к работе с данными для Windows Phone](mobile-services-windows-phone-get-started-data.md).

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F).
* [Visual Studio Express 2013 для Windows](https://go.microsoft.com/fwLink/p/?LinkID=257546) (с обновлением 2 или более поздней версии). 

##<a name="download-app"></a>Скачивание проекта GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Создание мобильной службы в Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. В обозревателе решений откройте файл кода App.xaml.cs в папке проекта GetStartedWithData.Shared и обратите внимание на новое статическое поле, добавленное в класс **App** в блоке условной компиляции приложения Магазина Windows, которое выглядит как в следующем примере:

	public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
	    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
	        "https://todolist.azure-mobile.net/",
	        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

&nbsp;&nbsp;Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью экземпляра [класса MobileServiceClient]. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Это статическое поле доступно для всех страниц в приложении.

&nbsp;&nbsp;8. Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт **Добавить**, щелкните **Подключенная служба...**, выберите только что созданную мобильную службу и нажмите кнопку **ОК**. Этот же код будет добавлен в общий файл App.xaml.cs, но на этот раз — в блок условной компиляции приложения Windows Phone.

Теперь приложение Магазина Windows и приложение Магазина Windows Phone подключены к новой мобильной службе. Следующий шаг — создание таблицы TodoItem в мобильной службе.

##<a name="add-table"></a>Добавление новой таблицы в мобильную службу

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##<a name="update-app"></a>Обновление приложения для использования мобильной службы

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

&nbsp;&nbsp;4. Войдите на [портал управления Azure] и выберите пункт **Мобильные службы**, а затем свою мобильную службу.

&nbsp;&nbsp;5. Последовательно выберите параметры **Данные** > **Обзор** и обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.
     
Это заключительный шаг учебника.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в универсальном приложении Windows возможности работы с данными в мобильных службах. Рекомендуем прочитать один из следующих разделов.

* [Приступая к работе с проверкой подлинности] <br/>Узнайте, как проверять подлинность пользователей приложения.

* [Приступая к работе с push-уведомлениями] <br/>Узнайте, как отправлять в приложение простейшие push-уведомления.

* [Справочник принципов использования мобильных служб C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Узнайте, как использовать мобильные службы с .NET.
  
<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use the mobile service]: #update-app
[Test the app against Mobile Services]: #test-app
[View uploaded data in the Azure Management Portal]: #view-data
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Validate and modify data with scripts]: ../mobile-services-windows-store-dotnet-validate-modify-data-server-scripts.md
[Refine queries with paging]: ../mobile-services-windows-store-dotnet-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Приступая к работе с проверкой подлинности]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Приступая к работе с push-уведомлениями]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[портал управления Azure]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[веб-сайта с примерами кода для разработчиков]: http://go.microsoft.com/fwlink/p/?LinkID=510826

[класса MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=August15_HO6-->