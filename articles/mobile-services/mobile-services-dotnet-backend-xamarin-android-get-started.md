<properties
	pageTitle="Приступая к работе с мобильными службами в приложениях Xamarin для Android | Microsoft Azure"
	description="Этот учебник поможет приступить к использованию мобильных служб Azure для разработки приложений Xamarin для Android"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="01/14/2016"
	ms.author="donnam"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Создание приложения Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).

В этом учебнике показано, как добавить облачную серверную службу в приложение Xamarin Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

>[AZURE.NOTE]В этом разделе показано, как создать новый проект мобильной службы с помощью классического портала Azure. Используя Visual Studio 2013 с обновлением 2, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. в разделе [Быстрый запуск: добавление мобильной службы (серверная версия .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям Xamarin Android.

>[AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. 
>Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started). Для работы с этим руководством требуется [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Доступна бесплатная пробная версия.

## Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения Xamarin Android

Создав мобильную службу, вы можете использовать функцию быстрого запуска на классическом портале Azure, чтобы создать или изменить приложение, а затем подключить его к своей мобильной службе.

В этом разделе предстоит скачать новое приложение Xamarin Android и проект службы для мобильной службы.

1. Если вы еще не сделали это, установите Visual Studio с расширением Xamarin. Инструкции см. в [руководстве по установке и настройке Visual Studio и Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). О том, как использовать Xamarin Studio на компьютере Mac OS X, см. в [руководстве по установке, настройке и проверке для пользователей Mac](https://msdn.microsoft.com/library/mt488770.aspx).  

2. На [классическом портале] щелкните раздел **Мобильные службы** и выберите только что созданную мобильную службу.

3. На вкладке быстрого запуска нажмите кнопку **Xamarin** в разделе **Выбор платформы**, а затем разверните раздел **Создание нового приложения Xamarin**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на Xamarin Android, подключенного к вашей мобильной службе.

  	![][7]

4. В разделе **Скачивание и публикация службы в облаке** выберите **Android** и щелкните кнопку **Скачать**.

  	Будет загружено решение, содержащее проекты для мобильной службы и примера приложения _To do list_, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

5. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Запуск приложения Xamarin Android

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении мобильной службы в Visual Studio или Xamarin Studio.

2. Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство.

	> [AZURE.NOTE] Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

3. В приложении введите осмысленный текст, например _Работа с учебником_, и щелкните значок «плюс» (**+**).

	![][10]

	Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [AZURE.NOTE]
   	Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# ToDoActivity.cs.

## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с синхронизацией автономных данных] <br/>Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

* [Приступая к работе с проверкой подлинности ] <br/>Дополнительные сведения о проверке подлинности учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Устранение неполадок серверной части в мобильных службах .NET] <br/>Узнайте, как определять и исправлять неполадки, которые могут возникнуть в серверной части мобильных служб для .NET.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Приступая к работе с синхронизацией автономных данных]: mobile-services-xamarin-android-get-started-offline-data.md
[Приступая к работе с проверкой подлинности ]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Приступая к работе с push-уведомлениями]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Azure classic portal]: https://manage.windowsazure.com/
[классическом портале]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: mobile-services-android-get-started.md
[Устранение неполадок серверной части в мобильных службах .NET]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!---HONumber=AcomDC_0413_2016-->