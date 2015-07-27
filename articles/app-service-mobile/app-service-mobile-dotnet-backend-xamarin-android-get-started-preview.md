<properties
	pageTitle="Начало работы с мобильными приложениями Azure для Xamarin Android — мобильные приложения Azure"
	description="Этот учебник поможет приступить к использованию мобильных приложений Azure для разработки приложений Xamarin Android"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrisanderson"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="05/01/2015"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Создание нового приложения Xamarin Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

В этом учебнике рассказывается, как добавить облачную серверную службу в приложение Xamarin для Android с помощью мобильных приложений Azure. В этом учебнике будет создана новая служба .NET и простое приложение _Список заданий_, которое хранит данные приложения в серверной службе .NET

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными мобильным приложениям Azure для приложений Xamarin Android.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Если вы хотите ознакомиться со службой приложений Azure до создания учетной записи, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где вы можете быстро создать краткосрочное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## Создание серверной части мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового приложения Xamarin Android

После создания серверной части мобильного приложения вы можете следовать простым шагам быстрого запуска на [портале Azure], чтобы создать новое приложение или изменить существующее приложение и подключить его к серверной части мобильного приложения.

В этом разделе предстоит скачать новое приложение Xamarin Android и проект серверной службы .NET для мобильного приложения.

1. На портале Azure щелкните **обзор**, затем **Мобильные приложения**, а затем щелкните только что созданное мобильное приложение.

2. В верхней части колонки нажмите кнопку **Добавить клиент** и разверните **Xamarin.Android**.

    ![][6]

    После этого будут показаны три простых шага для создания приложения Xamarin Android, подключенного к серверной части вашего мобильного приложения.


3. Если вы еще не сделали этого, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. Если вы еще не сделали этого, загрузите и установите [Xamarin Studio]. Вы также можете использовать Xamarin для Visual Studio.

5. В разделе **Скачивание и публикация службы в облаке** щелкните элемент **Скачать**.

  	Загрузится решение, содержащее проекты для кода серверной части мобильной службы и примера клиентского приложения _Список заданий_, которое подключается к серверной части вашей мобильной службы. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование серверной части мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация серверной части мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения Xamarin Android

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении мобильной службы в Visual Studio или Xamarin Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта, и после этого запустите приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство.

	> [AZURE.NOTE]Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

3. В приложении введите осмысленный текст, например _Работа с учебником_, и щелкните значок "плюс" (**+**).

	![][10]

	Отправится запрос POST к серверной части новой мобильной службы, размещенной в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются внутренним сервером мобильного приложения, а данные отображаются в списке.

	> [AZURE.NOTE]Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных, которые находятся в файле C# ToDoActivity.cs.



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[портале Azure]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=July15_HO3-->