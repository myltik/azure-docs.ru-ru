<properties
	pageTitle="Приступая к работе с серверной частью мобильных приложений для приложений из Магазина Windows | Центр разработчиков для мобильных устройств"
	description="Следуйте указаниям этой статьи, чтобы начать работу с серверной частью мобильных приложений Azure для разработки приложений Магазина Windows на C#, VB или JavaScript."
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

В этой статье показано, как добавить облачную серверную службу в универсальное приложение Windows с помощью серверной части мобильного приложения Azure. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Если вы хотите ознакомиться со службой приложений Azure до создания учетной записи, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где вы можете быстро создать краткосрочное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## <a name="create-new-service"> </a>Создание серверной части мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового универсального приложения для Windows

Создав серверную часть мобильного приложения, с помощью простых инструкций на портале Azure создайте новое приложение \(или измените существующее\) и подключите его к серверной части своего мобильного приложения.

В этом разделе вы создадите новое универсальное приложение для Windows, подключаемое к серверной части вашего мобильного приложения.

1. На портале Azure щелкните **Мобильное приложение**, а затем щелкните только что созданное мобильное приложение.

2. В верхней части колонки нажмите кнопку **Добавить клиент** и разверните **Windows \(C\#\)**.

   ![Шаги для быстрого запуска мобильного приложения](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   Здесь показаны три простых шага по созданию приложения для Магазина Windows, подключаемого к серверной части вашего мобильного приложения.

3. Если вы еще не сделали этого, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. В разделе **Загрузите и локально запустите ваше приложение и службу** выберите язык приложения для Магазина Windows, затем щелкните **Загрузить**.

   Загрузится решение, содержащее проекты для серверной части мобильной службы и примера приложения _Список заданий_, которое подключается к серверной части вашей мобильной службы. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Тестирование мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация серверной части мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения Windows

Теперь, когда серверная часть мобильного приложения опубликована, а клиент подключен к серверной части удаленного мобильного приложения, размещенного в Azure, мы можем запустить это приложение с использованием Azure для хранения элементов.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Define the mobile app backend instance]: #define-mobile-app-backend-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/

<!--HONumber=52-->
