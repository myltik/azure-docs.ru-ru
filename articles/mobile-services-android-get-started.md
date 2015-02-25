<properties pageTitle="Начало работы с мобильными службами Azure для приложений Android" description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений Android." services="" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="10/16/2014" ms.author="ricksal,glenga"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение <em>To do list</em>, хранящее данные приложения в новой мобильной службе.</p>
<p>Снимок экрана завершенного приложения приведен ниже:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">7:26</span></div>
</div>

![][0]

Для прохождения этого учебника требуются [Средства разработчика Android][Android SDK], которые включают в себя интегрированную среду разработки (IDE) Eclipse, подключаемый модуль средств разработчика Android (ADT) и новейшую платформу Android. Требуется Android 4.2 или более поздняя версия. 

Загруженный проект быстрого запуска содержит пакет SDK мобильных служб для платформы Android. Хотя для этого проекта требуется Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

> [AZURE.IMPORTANT] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

>[AZURE.NOTE] Если требуется просмотреть исходный код завершенного приложения, см. <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">здесь</a>.

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Создание нового приложения для Android</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Android** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение на Android**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на платформе Android, подключенного к вашей мобильной службе.

  	![][7]

3. Если это еще не было сделано, загрузите и установите [Средства разработчика Android][Android SDK] на своем локальном компьютере или виртуальной машине.

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.


5. Загрузите приложение:
	- В новейшей версии приложения используется пакет Android SDK для мобильных служб версии 2.0. Эту версию можно загрузить <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">здесь</a>. Нажмите кнопку **Download Zip** (Загрузить ZIP-файл), распакуйте архив. Проект находится в папке GettingStarted внутри папки Android.
	 
	- В более ранней версии используется предыдущая версия пакета SDK. Для ее использования в разделе **Загрузка и запуск приложения** щелкните **Загрузить**. При этом будут загружены файлы проекта для примера по созданию приложения _To do list_, подключаемого к вашей мобильной службе. Файлы проекта будут сжаты, поэтому перейдите к их расположению и распакуйте файлы на вашем компьютере.


## Запуск приложения для Android

Последний раздел учебника - построение и выполнение нового приложения.

2. В Eclipse щелкните **Файл**, затем щелкните **Импорт**, разверните элемент **Android**, щелкните **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее**. 

 	![][14]

3. Нажмите кнопку **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК**, убедитесь, что отмечен проект TodoActivity, а затем нажмите кнопку **Готово**. 

 	![][15]

	При этом файлы проекта импортируются в текущую рабочую область.

   	![][8]

4. Если вы загрузили пакет SDK версии 2.0, необходимо обновить код URL-адресом и ключом вашей мобильной службы:
	- 	Найдите метод **OnCreate** в **TodoActivity.java** и найдите код, который создает экземпляр клиента мобильных служб. Код отображается на предыдущем изображении.
	- 	Замените "MobileServiceUrl" фактическим URL-адресом вашей мобильной службы.
	- 	Замените "AppKey" ключом вашей мобильной службы.
	- 	Для получения дополнительных сведений обратитесь к учебнику <a href="http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-android-get-started-data/">Добавление мобильных служб к существующему приложению</a>. 



4. Для запуска проекта в эмуляторе Android в меню **Запуск** щелкните **Выполнить**.

	> [AZURE.IMPORTANT] Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

5. Введите содержательный текст в приложении, например _Complete the tutorial_, и нажмите кнопку **Добавить**.

   	![][10]

   	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [AZURE.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.

6. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   	![][11]

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   	![][12]

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним с помощью мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

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


<!--HONumber=42-->
