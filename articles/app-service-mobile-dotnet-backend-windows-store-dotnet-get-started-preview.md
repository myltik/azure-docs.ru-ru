<properties
	pageTitle="Начало работы с внутренними серверами мобильных приложений для приложений Магазина Windows | Центр разработки мобильных приложений"
	description="Пройдите этот учебник, чтобы начать использовать внутренние серверы мобильных приложений Azure для разработки приложений Магазина Windows на C#, VB или JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Создание приложения Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

В этом учебнике показано, как добавить облачную службу внутреннего сервера к универсальному приложению Windows, используя внутренний сервер мобильного приложения Azure. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure до регистрации учетной записи Azure, перейдите в раздел [Пробная версия службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где можно сразу создать кратковременное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## <a name="create-new-service"> </a>Создание внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового универсального приложения для Windows

После создания внутреннего сервера мобильного приложения вы можете следовать простым шагам краткого руководства на портале Azure, чтобы создать новое приложение или изменить существующее и подключить его к внутреннему серверу мобильного приложения.

В этом разделе будет создано новое универсальное приложение Windows, подключаемое к внутреннему серверу мобильного приложения.

1. На портале Azure щелкните **Мобильное приложение**, а затем выберите созданное мобильное приложение.

2. В верхней части колонки щелкните **Добавить клиент** и разверните **Windows (C#)**.

   ![Mobile App quickstart steps](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   Отобразятся три простых шага для создания приложения Магазина Windows, подключенного к внутреннему серверу мобильного приложения.

3. Если вы этого еще не сделали, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальный компьютер или виртуальную машину.

4. В разделе **Загрузите и локально запустите ваше приложение и службу** выберите язык для приложения Магазина Windows, затем щелкните **Загрузить**.

   При этом скачивается решение, содержащее проекты для внутреннего сервера мобильного приложения и примера приложения _Список задач_, которое подключается к внутреннему серверу мобильного приложения. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Тестирование мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения Windows

После публикации внутреннего сервера мобильного приложения и подключения клиента к удаленному внутреннему серверу мобильных приложений, размещенному в Azure, можно запустить приложения, используя Azure для хранилища экземпляров.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Начало работы с внутренними серверами мобильных приложений]:#getting-started
[Создание внутреннего сервера мобильного приложения]:#create-new-service
[Определение экземпляра внутреннего сервера мобильного приложения]:#define-mobile-app-backend-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Начало работы с проверкой подлинности]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Пакет SDK для мобильных приложений]: http://go.microsoft.com/fwlink/?LinkId=257545
[Портал Azure]: https://portal.azure.com/

<!--HONumber=49-->