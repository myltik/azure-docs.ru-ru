<properties linkid="mobile-services-javascript-backend-windows-universal-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Universal) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your universal Windows app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>


# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как применять мобильные службы Azure для эффективного использования данных в универсальном приложении Windows. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений Windows для Windows и Windows Phone][Создание универсальных приложений Windows для Windows и Windows Phone].

В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для универсального приложения Windows, которое хранит данные в памяти, создать мобильную службу, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

>[WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio Professional 2013 с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Эти же инструкции можно использовать для подключения мобильной службы к приложению Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как подключить мобильную службу к приложению Windows Phone 8.0 или Windows Phone Silverlight 8.1, см. в разделе [Приступая к работе с данными для Windows Phone][Приступая к работе с данными для Windows Phone].

> Если вы не можете выполнить обновление до Visual Studio Professional 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение Магазина Windows, см. [эту версию][эту версию] раздела.

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2. [Создание мобильной службы в Visual Studio][Создание мобильной службы в Visual Studio]
3. [Добавление таблицы для хранения данных][Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильной службы][Обновление приложения для использования мобильной службы]
5. [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]
6. [Просмотр переданных данных на портале управления Azure][Просмотр переданных данных на портале управления Azure]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
* [Visual Studio Express 2013 для Windows][Visual Studio Express 2013 для Windows] (с обновлением 2 или более поздней версии).


## <a name="download-app"></a> Загрузка проекта GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]


## <a name="create-service"></a> Создание мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>В обозревателе решений откройте файл кода App.xaml.cs в папке проекта GetStartedWithData.Shared и обратите внимание на новое статическое поле, добавленное в класс <strong>App</strong> в блоке условной компиляции приложения Магазина Windows, которое выглядит как в следующем примере:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью экземпляра [класса MobileServiceClient][класса MobileServiceClient]. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Это статическое поле доступно для всех страниц в приложении.</p>
</li>
<li><p>Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт <strong>Добавить</strong>, щелкните <strong>Подключенная служба...</strong>, выберите только что созданную мобильную службу и нажмите кнопку <strong>ОК</strong>. </p>
<p>Этот же код будет добавлен в общий файл App.xaml.cs, но на этот раз — в блок условной компиляции приложения Windows Phone.</p></li>
</ol>

Теперь приложение Магазина Windows и приложение Магазина Windows Phone подключены к новой мобильной службе. Следующий шаг — создание таблицы TodoItem в мобильной службе.

## <a name="add-table"></a> Добавление новой таблицы в мобильную службу

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]Новые таблицы создаются со столбцами Id, __createdAt, __updatedAt и __version. Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема][Динамическая схема].

# <a name="update-app"></a> Обновление приложения для использования мобильной службы

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

## <a name="test-azure-hosted"></a> Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>На [Портале управления][Портале управления] щелкните <strong>Мобильные службы</strong>, затем щелкните свою мобильную службу.<p></li>
<li><p>Откройте вкладку <strong>Данные</strong>, а затем щелкните <strong>Обзор</strong>.</p>
<p>Обратите внимание, что таблица <strong>TodoItem</strong> теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.</p></li>
</ol>

![][0]

Это заключительный шаг учебника <strong>Приступая к работе с данными</strong>.

## <a name="next-steps"> </a> Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в универсальном приложении Windows возможности работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб в .NET.



<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->


[mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
[Создание универсальных приложений Windows для Windows и Windows Phone]: http://msdn.microsoft.com/ru-ru/library/windows/apps/xaml/dn609832.aspx
[Приступая к работе с данными для Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data
[эту версию]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Загрузка проекта приложения для Магазина Windows]: #download-app
[Создание мобильной службы в Visual Studio]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильной службы]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Просмотр переданных данных на портале управления Azure]: #view-data
[Бесплатная пробная версия Azure]: http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F
[Visual Studio Express 2013 для Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[mobile-services-windows-universal-dotnet-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
[mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
[класса MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
[Динамическая схема]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx
[mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
[mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
[Портале управления]: https://manage.windowsazure.com/
[0]: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
[Проверка и изменение данных с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
