<properties
	pageTitle="Приступая к работе с мобильными службами для приложений Xamarin для iOS | Microsoft Azure"
	description="Этот учебник поможет приступить к использованию мобильных служб Azure для разработки приложений Xamarin для iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/05/2015"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

В этом учебнике показано, как с помощью мобильных служб Azure добавить облачную серверную службу в приложение Xamarin.iOS. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе.

Кроме того, можно посмотреть видеоклип (см. ниже), где приведены те же действия, что и в учебнике.

Видео: «Приступая к работе с мобильными службами Xamarin и Azure» с участием Крейга Данна (Craig Dunn), ИТ-пропагандиста продукции Xamarin (длительность: 10:05 мин)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для работы с этим учебником необходимы XCode и [Xamarin Studio] для OS X или подключаемый модуль Xamarin Visual Studio для Visual Studio в ОС Windows. Образец будет работать в iOS 5.0 и более поздней версии.

> [AZURE.IMPORTANT] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения Xamarin.iOS

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на классическом портале Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к своей мобильной службе.

В этом разделе будет создано новое приложение Xamarin.iOS, которое будет подключено к вашей мобильной службе.

1.  На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Xamarin.iOS** в меню **Выбор платформы**, а затем разверните элемент **Создание нового приложения Xamarin.iOS**.

	![][6]

	После этого будут показаны три простых шага для создания приложения Xamarin.iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если вы еще этого не сделали, скачайте и установите Xcode (рекомендуем использовать самую новую версию – Xcode 6.0 или более позднюю) и [Xamarin Studio].

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.

5. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

	При этом будут скачаны файлы проекта для примера по созданию приложения _To do list_, подключенного к вашей мобильной службе, и добавлена ссылка на компонент мобильных служб Azure для Xamarin.iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения Xamarin.iOS

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите к месту хранения сжатых файлов проекта, извлеките файлы на компьютер и откройте файл решения **XamarinTodoQuickStart.iOS.sln** в Xamarin Studio или Visual Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

3. В приложении введите осмысленный текст, например _Работа с учебником_, и щелкните значок «плюс» (**+**).

	![][10]

	Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [AZURE.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# TodoService.cs.

4. На [классическом портале Azure] щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

	![][11]

	Это позволяет просматривать данные, добавленные в таблицу приложением.

	![][12]


## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с синхронизацией автономных данных] Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

* [Добавление проверки подлинности к приложению мобильных служб]. Узнайте, как проверять подлинность пользователей приложения с помощью поставщика удостоверений.

* [Добавление push-уведомлений к приложению мобильных служб]. Узнайте, как отправлять в приложение простейшие push-уведомления.

* [Использование управляемой клиентской библиотеки для мобильных служб Azure](partner-xamarin-mobile-services-how-to-use-client-library.md). Узнайте, как отправлять запросы в мобильную службу, работать с данными и обращаться к настраиваемым API.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

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
[Приступая к работе с синхронизацией автономных данных]: mobile-services-xamarin-ios-get-started-offline-data.md
[Добавление проверки подлинности к приложению мобильных служб]: partner-xamarin-mobile-services-ios-get-started-users.md
[Добавление push-уведомлений к приложению мобильных служб]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[классическом портале Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0128_2016-->