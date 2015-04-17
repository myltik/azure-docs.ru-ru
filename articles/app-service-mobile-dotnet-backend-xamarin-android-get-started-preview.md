<properties
	pageTitle="Начало работы с мобильными приложениями Azure для приложений Xamarin для Android - мобильное приложение Azure"
	description="Этот учебник поможет приступить к использованию мобильных приложений Azure для разработки приложений Xamarin для Android"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrande"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Создание приложения Xamarin для Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

В этом учебнике показано, как добавить облачную внутреннюю службу в приложение Xamarin для Android с мобильным приложением Azure. В этом учебнике вы создадите новую службу .NET и простое приложение _Список задач_, в котором хранятся данные приложения на внутреннем сервере .NET.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является обязательным для всех остальных учебников по мобильным приложениям Azure Xamarin для Android.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure до регистрации учетной записи Azure, перейдите в раздел [Пробная версия службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где можно сразу создать кратковременное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## Создание внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового приложения Xamarin Android

После создания внутреннего сервера мобильного приложения вы можете следовать простым шагам краткого руководства на [Портал Azure], чтобы создать новое приложение или изменить существующее и подключить его к внутреннему серверу мобильного приложения.

В этом учебнике вы скачаете новое приложение Xamarin для Android и проект внутренней службы .NET для мобильного приложения.

1. На портале Azure щелкните **Обзор**, затем **Мобильные приложения** и выберите только что созданное мобильное приложение.

2. В верхней части колонки щелкните **Добавить клиент** и разверните **Xamarin.Android**.

    ![][6]

    Отобразятся три простых шага для создания приложения Xamarin для Android, подключенного к внутреннему серверу мобильного приложения.


3. Если вы еще этого не сделали, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.  

4. Если вы этого еще не сделали, скачайте и установите [Xamarin Studio]. Вы также можете использовать Xamarin для Visual Studio.

5. В разделе **Загрузка и публикация службы в облаке** щелкните **Скачать**.

  	При этом скачивается решение, которое содержит проекты для кода внутреннего сервера мобильного приложения и для примера клиентского приложения _Список задач_, подключенного к внутреннему серверу мобильного приложения. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения Xamarin Android

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении мобильного приложения в Visual Studio или Xamarin Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запустить**, чтобы выполнить сборку проекта и запустить приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство.

	> [AZURE.NOTE] Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

3. В приложении введите содержательный текст, например _Завершение работы с учебником_, и щелкните значок "плюс" (**+**).

	![][10]

	При этом на новый внутренний сервер мобильного приложения, размещенный в Azure, отправляется запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются внутренним сервером мобильного приложения, а данные отображаются в списке.

	> [AZURE.NOTE]
   	> Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных, которые находятся в файле C# ToDoActivity.cs.



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Портал Azure]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin для Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=49-->