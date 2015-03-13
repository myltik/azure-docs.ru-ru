<properties 
	pageTitle="Начало работы с мобильными службами Azure для приложений Android" 
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений Android." 
	services="mobile-services" 
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
	ms.date="02/13/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение Android с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Информацию о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия базы данных JavaScript](/ru-ru/documentation/articles/mobile-services-android-get-started-EC/) этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для прохождения этого учебника требуются [Средства разработчика Android][SDK Android], которые включают в себя интегрированную среду разработки (IDE) Eclipse, подключаемый модуль средств разработчика Android (ADT) и новейшую платформу Android. Требуется Android 4.2 или более поздняя версия. 

Загруженный проект быстрого запуска содержит пакет SDK мобильных служб для платформы Android. Хотя для этого проекта требуется Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

> [AZURE.IMPORTANT] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Скачивание мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1. Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска выберите **Android** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения Android**.

	![][1]  

2. Если вы еще не сделали этого, скачайте и установите [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) или более поздней версии.

3. Щелкните **Загрузить** в разделе **Загрузка и публикация службы в облаке**.

	При этом загружается проект Visual Studio, реализующий мобильную службу. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

4. Кроме того, скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запишите путь к нему.

## Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Создание нового приложения для Android

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1. На [портале управления] щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Android** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение на Android**. 
 
	![][2]  

3. Если это еще не было сделано, загрузите и установите [Средства разработчика Android][SDK Android] на своем локальном компьютере или виртуальной машине.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

  	При этом будут загружены файлы проекта для примера по созданию приложения _Список задач_, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запуск приложения для Android

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2. В Eclipse нажмите кнопку **Файл**, затем щелкните **Импорт**, разверните **Android**, щелкните пункт **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее.** 

 	![][14]

3. Нажмите кнопку **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК**, убедитесь, что отмечен проект TodoActivity, а затем нажмите кнопку **Готово**. 

 	![][15]

	При этом файлы проекта импортируются в текущую рабочую область.

   	![][8]

4. Для запуска проекта в эмуляторе Android в меню **Запуск** щелкните **Выполнить**.

	> [AZURE.IMPORTANT] Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

5. Введите содержательный текст в приложении, например _Завершить работу с учебником_, и нажмите кнопку **Добавить**.

   	![][10]

   	При этом в новую мобильную службу, размещенную в Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [AZURE.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Дополнительная информация о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Устранение неполадок серверной части .NET мобильных служб]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET. 

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-EC.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-EC.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[SDK Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Пакет SDK для мобильных служб для Android]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Устранение неполадок серверной части .NET мобильных служб]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Портал управления]: https://manage.windowsazure.com/

<!--HONumber=45--> 
