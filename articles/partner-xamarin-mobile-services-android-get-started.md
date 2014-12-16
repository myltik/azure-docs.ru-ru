<properties pageTitle="Приступая к работе с мобильными службами для Xamarin.Android" metaKeywords="Azure Приложение Xamarin.Android, мобильная служба XamarinAndroid, приступая к работе с Azure Xamarin.Android" writer="craigd" description="Learn how to use Azure Mobile Services with your Xamarin.Android app." metaCanonical="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/11/2014" ms.author="donnam" />

# <a name="getting-started"></a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В данном учебнике описывается добавление облачных служб внутреннего сервера в приложение Xamarin.Android с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе.</p>
<p>Снимок экрана завершенного приложения приведен ниже:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:05</span></div>
</div>

![][0]

Для прохождения этого учебника требуется: [Xamarin.Android], который устанавливает Xamarin Studio и подключаемый модуль Visual Studio (на Windows), а также новейшую платформу Android. Требуется пакет SDK Android 4.2 или более поздней версии. 

Скачанный проект быстрого запуска содержит компонент мобильных служб Azure для Xamarin.Android. Несмотря на то что этот проект предназначен для Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Создание нового приложения Xamarin.Android</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение Xamarin.Android, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Xamarin** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение Android**.

	![][6]

	После этого будут показаны три простых шага для создания приложения Xamarin.Android, подключенного к вашей мобильной службе.

	![][7]

3. Щелкните **Создание таблицы TodoItem**, чтобы создать таблицу для хранения данных приложения.

4.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. 

	Это приведет к загрузке проекта для примера приложения _To do list_, которое подключено к мобильной службе. Сохраните упакованный файл проекта на своем локальном компьютере и зафиксируйте, в каком месте он был сохранен.

## Запуск приложения для Android

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2. В Xamarin Studio или Visual Studio щелкните **Файл**, затем **Открыть**, перейдите к распакованным файлам примера и выберите **XamarinTodoQuickStart.Android.sln**, чтобы его открыть.

 	![][8]

	![][9]

3.  Нажмите клавишу **Запуск** для построения проекта, после чего запустите приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство. 

	<div class="dev-callout"><strong>Примечание</strong>. <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство на платформе Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

4. В приложении введите значимый текст, например, _Пройти учебник_, затем щелкните **Добавить**.

	![][10]

	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [WACOM.NOTE] 
   	> Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# ToDoActivity.cs.

6. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

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
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Начало работы с автономной синхронизацией данных]: /ru-ru/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[WindowsAzure.com]: http://www.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
