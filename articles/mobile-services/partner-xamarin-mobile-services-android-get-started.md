<properties
	pageTitle="Приступая к работе с мобильными службами в приложениях Xamarin для Android | Microsoft Azure"
	writer="craigd"
	description="Узнайте, как использовать мобильные службы Azure с приложением Xamarin.Android."
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="donnam"/>

# <a name="getting-started"></a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Создание приложения Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).

В данном учебнике описывается добавление облачных служб внутреннего сервера в приложение Xamarin.Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе.

Кроме того, можно посмотреть видеоклип (см. ниже), где показаны те же действия, что и в руководстве.

Видео: «Приступая к работе с мобильными службами Xamarin и Azure» с участием Крейга Данна (Craig Dunn), ИТ-пропагандиста продукции Xamarin (длительность: 10:05 мин)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника требуется компонент [Xamarin.Android], который устанавливает Xamarin Studio и подключаемый модуль Visual Studio (в Windows), а также последнюю версию платформы Android. Требуется пакет SDK Android 4.2 или более поздней версии.

Скачанный проект быстрого запуска содержит компонент мобильных служб Azure для Xamarin.Android. Хотя этот проект предназначен для Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

> [AZURE.IMPORTANT] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения Xamarin.Android

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на классическом портале Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к своей мобильной службе.

В этом разделе будет создано новое приложение Xamarin.Android, которое будет подключено к вашей мобильной службе.

1.  На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска в меню **Выбор платформы** нажмите кнопку **Xamarin.Android**, а затем разверните элемент **Создание нового приложения Android**.

	![][6]

	После этого будут показаны три простых шага для создания приложения Xamarin.Android, подключенного к вашей мобильной службе.

	![][7]

3. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

	При этом будут загружены файлы проекта для примера по созданию приложения _To do list_, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запуск приложения для Android

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2. В Xamarin Studio или Visual Studio последовательно щелкните **Файл** и **Открыть**, перейдите к несжатым файлам примеров и выберите для открытия файл **XamarinTodoQuickStart.Android.sln**.

3. Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта, и после этого запустите приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство.

	> [AZURE.NOTE] Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

4. В приложении введите содержательный текст, например _Завершение работы с учебником_, и нажмите кнопку **Добавить**.

	![][10]

	Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [AZURE.NOTE]
   	Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# ToDoActivity.cs.

6. На [классическом портале Azure] щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

	![][11]

	Это позволяет просматривать данные, добавленные в таблицу приложением.

	![][12]

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с синхронизацией автономных данных] Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

* [Добавление проверки подлинности к приложению мобильных служб]. Узнайте, как проверять подлинность пользователей приложения с помощью поставщика удостоверений.

* [Добавление push-уведомлений к приложению мобильных служб]. Узнайте, как отправлять в приложение простейшие push-уведомления.



[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Приступая к работе с синхронизацией автономных данных]: mobile-services-xamarin-android-get-started-offline-data.md
[Добавление проверки подлинности к приложению мобильных служб]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Добавление push-уведомлений к приложению мобильных служб]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[классическом портале Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0323_2016-->