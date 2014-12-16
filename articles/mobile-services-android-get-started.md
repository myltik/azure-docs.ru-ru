<properties pageTitle="Начало работы с мобильными службами Azure для приложений Android" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Этот учебник показывает, как добавить облачную серверную службу к приложению Android с использованием мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение <em>Список заданий</em>, которое хранит данные приложения в новой мобильной службе.</p>
<p>Снимок экрана завершенного приложения приведен ниже:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">07:26:00</span></div>
</div>

![][0]

Для прохождения этого учебника требуются [Средства разработчика Android][SDK Android], которые включают интегрированную среду разработки (IDE) Eclipse, сменный модуль средств разработчика Android (ADT) и новейшую платформу Android. Требуется Android 4.2 или более поздняя версия. 

Загружаемый проект краткого руководства содержит SDK мобильных служб для Android. Безусловно, для этого проекта требуется Android 4.2 или более поздняя версия, но для мобильных служб достаточно иметь только SDK Android 2.2 или более позднюю версию.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

>[AZURE.NOTE] Если требуется просмотреть исходный код завершенного приложения, см. <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">здесь</a>.

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Создание нового приложения для Android</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.

2. На вкладке краткого руководства щелкните **Android** в разделе **Выбор платформы** и разверните **Создать новое приложение Android**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на платформе Android, подключенного к вашей мобильной службе.

  	![][7]

3. Если это еще не было сделано, загрузите и установите [Средства разработчика Android][SDK Android] на своем локальном компьютере или виртуальной машине.

4. Щелкните **Создание таблицы TodoItem**, чтобы создать таблицу для хранения данных приложения.


5. Загрузите приложение:
	- В новейшей версии приложения используется пакет Android SDK для мобильных служб версии 2.0. Эту версию можно загрузить <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">здесь</a>. Нажмите кнопку **Загрузить Zip**, распакуйте архив. Проект находится в папке GettingStarted внутри папки Android.
	 
	- В более ранней версии используется предыдущая версия пакета SDK. Для ее использования в разделе **Загрузка и запуск приложения** щелкните **Загрузить**. При этом будут загружены файлы проекта для примера по созданию приложения _Список задач_, подключаемого к вашей мобильной службе. Файлы проекта будут сжаты, поэтому перейдите к их расположению и распакуйте файлы на вашем компьютере.


## Запуск приложения для Android

Последний раздел учебника - построение и выполнение нового приложения.

2. В Eclipse щелкните **Файл**, затем щелкните **Импорт**, разверните элемент **Android**, щелкните **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее** .

 	![][14]

3. Щелкните **Обзор**, перейдите к местоположению развернутых файлов проекта, щелкните **ОК**, обязательно выберите проект TodoActivity, затем щелкните **Готово**. 

 	![][15]

	При этом файлы проекта импортируются в текущую рабочую область.

   	![][8]

4. Если вы загрузили пакет SDK версии 2.0, необходимо обновить код URL-адресом и ключом вашей мобильной службы:
	- 	Найдите метод **OnCreate** в **TodoActivity.java** и найдите код, который создает экземпляр клиента мобильных служб. Код отображается на предыдущем изображении.
	- 	Замените "MobileServiceUrl" фактическим URL-адресом вашей мобильной службы.
	- 	Замените "AppKey" ключом вашей мобильной службы.
	- 	Для получения дополнительных сведений обратитесь к учебнику <a href="http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-android-get-started-data/">Добавление мобильных служб к существующему приложению</a>. 



4. Откройте меню **Выполнить** и щелкните ** Выполнить**, чтобы запустить проект в эмуляторе Android.

	<div class="dev-callout"><strong>Примечание</strong>. <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство на платформе Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

5. В приложении введите значимый текст, например, _Пройти учебник_, затем щелкните **Добавить**.

   	![][10]

   	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	<div class="dev-callout"><strong>Примечание.</strong> 
   	<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.</p>
 	</div>

6. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   	![][11]

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   	![][12]

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

<!-- Anchors. -->
[Начало работы с мобильными службами]:#getting-started
[Создание новой мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-android-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/
