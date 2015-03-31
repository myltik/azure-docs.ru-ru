<properties 
	pageTitle="Приступая к работе с push-уведомлениями (Android) | Центр разработчиков мобильных устройств" 
	description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение .NET для Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="ricksal"/>

# Добавление push-уведомлений в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи. 




Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Таким образом, для работы с этим учебником требуется Visual Studio 2013. 

>[AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Бесплатное пробное использование Azure</a>. 

<!-- -->

>[AZURE.NOTE] Для просмотра версии этого учебника для Eclipse перейдите к пункту: [Приступая к работе с push-уведомлениями (Eclipse)].
 
##<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Настройка мобильных служб для отправки push-запросов

1. Войдя на [портал управления Azure], щелкните элемент **Мобильные службы** и выберите нужное приложение.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Перейдите на вкладку **Push-уведомления**, введите значение **Ключ API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

> [AZURE.IMPORTANT] При задании учетных данных WNS для улучшенных push-уведомлений на вкладке "Push-уведомления" портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.


Ваша мобильная служба теперь настроена для работы с GCM и концентраторами уведомлений.


<h2><a name="download-the-service"></a>Загрузка службы на локальный компьютер</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Тестирование мобильной службы</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Обновление сервера для отправки push-уведомлений

1. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs. Добавьте следующие операторы `using` в начало файла:


		using System;
		using System.Collections.Generic;

2. Обновите определение метода`PostTodoItem` с помощью следующего кода:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Этот код отправит push-уведомление (с текстом вставленного элемента) после вставки элемента списка дел. В случае возникновения ошибки код добавит запись в журнал ошибок, которую можно просмотреть на вкладке **Журналы** мобильной службы на портале управления.


<h2><a name="publish-the-service"></a>Публикация мобильной службы в Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Добавление push-уведомлений в приложение

###Проверка версии Android SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте. 

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

<h2><a name="test-app"></a>Тестирование приложения с помощью опубликованной мобильной службы</h2>

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###При использовании эмулятора для тестирования...

Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. В разделе **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство, выберите **Изменить** (или **Создать**, если пока никаких устройств нет).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Выберите **API-интерфейсы Google** (или **API-интерфейсы Google x86**) в поле **Цель**, а затем нажмите кнопку "ОК".

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google. Если имеется несколько установленных версий пакета Android SDK, убедитесь, что уровень API соответствует установленному ранее в свойствах проекта.

###<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Выполнение теста

1. В меню **Запуск** в Eclipse щелкните **Запуск**, чтобы запустить приложение.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_) и нажмите кнопку **Добавить**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.


Вы успешно завершили ознакомление с данным учебником.


## <a name="next-steps"> </a>Дальнейшие действия

<!---В этом учебнике содержатся базовые сведения о том, как включить в приложении Android возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Рассылка push-уведомлений проверенным пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только тем пользователям, которые прошли проверку подлинности.


+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Дополнительная информация о том, как использовать теги для отправки push-уведомлений из мобильной службы только прошедшим проверку пользователям.

+ [Рассылка широковещательных уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений по шаблону подписчикам]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.
-->
Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним с помощью мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительная информация об аутентификации учетных данных пользователей приложения с другими типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Дополнительные сведения о работе центров уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Руководство по устранению неполадок и решения по отладке центров уведомлений. 

* [Использование клиентской библиотеки Android для мобильных служб]
  <br/>Дополнительные сведения об использовании мобильных служб с Android.  
  
<!-- Anchors. -->

[Создание мобильной службы]: #create-service
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Тестирование мобильной службы]: #test-the-service
[Загрузка проекта GetStartedWithData]: #download-app
[Обновление приложения для доступа к данным с помощью мобильной службы]: #update-app
[Тестирование приложения Android с помощью службы, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения Android с помощью службы, размещенной в Azure]: #test-azure-hosted
[Тестирование приложения с помощью опубликованной мобильной службы]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Приступая к работе с push-уведомлениями (Eclipse)]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push-EC
[Проверка и изменение данных с помощью скриптов]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов c разбиением по страницам]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе с мобильными службами]: /documentation/articles/mobile-services-dotnet-backend-android-get-started
[Приступая к работе с данными]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Приступая к работе с проверкой подлинности]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript и HTML]: /develop/mobile/tutorials/get-started-with-data-js
[Серверная версия JavaScript]: /develop/mobile/tutorials/get-started-with-data-android
[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник по принципам использования мобильных служб .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[Использование клиентской библиотеки Android для мобильных служб]: /documentation/articles/mobile-services-android-how-to-use-client-library

[Рассылка push-уведомлений проверенным пользователям]: /documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /documentation/articles/notification-hubs-overview/
[Рассылка широковещательных уведомлений подписчикам]: /documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Рассылка уведомлений по шаблону подписчикам]: /documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
