<properties pageTitle="Начало работы с мобильными службами в приложениях Xamarin iOS" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]



В этом учебнике будет показано, как добавить облачную серверную службу в приложение Xamarin.iOS с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе.

Кроме того, можно посмотреть видеоклип (см. ниже), где приведены те же действия, что и в учебнике.

Видео: "Приступая к работе с мобильными службами Xamarin и Azure" с участием Крейга Данна (Craig Dunn), ИТ-пропагандиста продукции Xamarin (длительность: 10:05 мин)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



Снимок экрана завершенного приложения приведен ниже:

![][0]

Для выполнения заданий этого учебника потребуется XCode 4.5 и IOS 5.0 или более поздняя версия, а также [Xamarin Studio] для OS X или подключаемый модуль Xamarin Visual Studio для Visual Studio на Windows.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Создание нового приложения Xamarin.iOS</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение Xamarin.iOS, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.

2. На вкладке быстрого старта выберите **Xamarin.iOS** в списке **Выбора платформы** и раскройте узел **Создать новое приложение Xamarin.iOS**.

	![][6]

	После этого будут показаны три простых шага для создания приложения Xamarin.iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите [Xcode] V4.4 или более поздней версии и [Xamarin Studio].

4. Щелкните **Создание таблицы TodoItems**, чтобы создать таблицу для хранения данных приложения.

5.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

	После этого загрузится проект примера приложения ToDoList, которое подключено к вашей мобильной службе и обращается к компоненту Azure Mobile Services для Xamarin.iOS. Сохраните запакованный файл проекта на локальном компьютере и запомните место сохранения.

<h2>Запустите ваше новое приложение Xamarin.iOS</h2>

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите в папку, где вы сохранили сжатые файлы проекта, распакуйте файлы на свой компьютер и откройте файл решения **XamarinTodoQuickStart.iOS.sln** с помощью Xamarin Studio или Visual Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

3.  В приложении введите содержательный текст, например "Завершить работу с учебником", и щелкните значок "плюс" (**+**).

	![][10]

Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	<div class="dev-callout"> 
	<b>Примечание</b> 
   	<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# TodoService.cs.</p> 
 	</div>

4. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

	![][11]

	Это позволяет просматривать данные, добавленные в таблицу приложением.

	![][12]

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Начало работы с автономной синхронизацией данных]
  <br/>Использование автономной синхронизации данных для повышения быстроты отклика и надежности приложения.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.


<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Начало работы с автономной синхронизацией данных]: /ru-ru/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/
