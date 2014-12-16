<properties pageTitle="Начало работы с мобильными службами Azure для приложений Android" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Этот учебник показывает, как добавить облачную серверную службу к приложению Android с использованием мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Для создания мобильной службы, которая позволяет писать серверную бизнес-логику на языке JavaScript, ознакомьтесь с разделом [Серверная версия базы данных JavaScript](/ru-ru/documentation/articles/mobile-services-android-get-started/) этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для прохождения этого учебника требуются [Средства разработчика Android][SDK Android], которые включают интегрированную среду разработки (IDE) Eclipse, сменный модуль средств разработчика Android (ADT) и новейшую платформу Android. Требуется Android 4.2 или более поздняя версия. 

Загружаемый проект краткого руководства содержит SDK мобильных служб для Android. Безусловно, для этого проекта требуется Android 4.2 или более поздняя версия, но для мобильных служб достаточно иметь только SDK Android 2.2 или более позднюю версию.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Скачивание мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1. Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска выберите **Android** в разделе **Выбор платформы** и разверните элемент **Создание нового приложения Android**.

	![][1]  

2. Если вы еще не сделали этого, загрузите и установите [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) или более поздней версии.

3. Щелкните **Загрузка** в разделе **Загрузить и опубликовать свою службу в облаке**.

	Это приведет к загрузке проекта Visual Studio, реализующего мобильную службу. Сохраните упакованный файл проекта на локальном компьютере и запомните, в каком месте он был сохранен.

4. Кроме того, скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запишите путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Создание нового приложения для Android

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1. На [портале управления] щелкните **Мобильные службы**, затем выберите только что созданную мобильную службу.

2. На вкладке краткого руководства щелкните **Android** в разделе **Выбор платформы** и разверните **Создать новое приложение Android**. 
 
	![][2]  

3. Если это еще не было сделано, загрузите и установите [Средства разработчика Android][SDK Android] на своем локальном компьютере или виртуальной машине.

4. В разделе **Загрузка и выполнение приложения** щелкните **Загрузка**. 

  	Это приведет к загрузке проекта для примера приложения _To do list_, которое подключено к мобильной службе. Сохраните упакованный файл проекта на своем локальном компьютере и зафиксируйте, в каком месте он был сохранен.

## Запуск приложения для Android

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2. В Eclipse щелкните **Файл**, затем щелкните **Импорт**, разверните элемент **Android**, щелкните **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее** .

 	![][14]

3. Щелкните **Обзор**, перейдите к местоположению развернутых файлов проекта, щелкните **ОК**, обязательно выберите проект TodoActivity, затем щелкните **Готово**. 

 	![][15]

	При этом файлы проекта импортируются в текущую рабочую область.

   	![][8]

4. Откройте меню **Выполнить** и щелкните ** Выполнить**, чтобы запустить проект в эмуляторе Android.

	<div class="dev-callout"><strong>Примечание</strong>. <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство на платформе Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

5. В приложении введите значимый текст, например, _Пройти учебник_, затем щелкните **Добавить**.

   	![][10]

   	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	<div class="dev-callout"><strong>Примечание.</strong> 
   	<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.</p>
 	</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Устранение неполадок сервера базы данных мобильных служб .NET]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET. 

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png


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
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Устранение неполадок сервера базы данных мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Портал управления]: https://manage.windowsazure.com/
