<properties
	pageTitle="Начало работы с мобильными службами Azure для приложений Android (серверная часть JavaScript)"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений Android (серверная часть JavaScript)."
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
	ms.topic="hero-article"
	ms.date="10/05/2015"
	ms.author="ricksal"/>

# Начало работы с мобильными службами для Android (серверная часть JavaScript)

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение **To do list**, хранящее данные приложения в новой мобильной службе.

> [AZURE.VIDEO mobile-get-started-android]

Снимок экрана завершенного приложения приведен ниже: ![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Предварительные требования

Для прохождения этого учебника требуются [Средства разработчика Android][Android Studio], которые включают в себя интегрированную среду разработки Android Studio и новейшую платформу Android. Требуется Android 4.2 или более поздняя версия.

Загруженный проект быстрого запуска содержит пакет SDK мобильных служб Azure для платформы Android.

> [AZURE.IMPORTANT]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения для Android

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Android** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение на Android**.

   	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	Здесь показаны три простых шага для создания приложения на платформе Android, подключенного к вашей мобильной службе.

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Если вы еще не сделали этого, загрузите и установите [Средств разработчика Android][Android SDK] на ваш локальный компьютер или виртуальную машину.

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.


5. Загрузите приложение:
	- В новейшей версии приложения используется пакет Android SDK для мобильных служб версии 2.0. Эту версию можно скачать <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">здесь</a>. Нажмите кнопку **Загрузить ZIP-файл**, распакуйте архив. Проект находится в папке GettingStarted внутри папки Android.

	- В более ранней версии используется предыдущая версия пакета SDK. Для ее использования в разделе **Загрузка и запуск приложения** щелкните **Загрузить**. При этом будут загружены файлы проекта для примера по созданию приложения _To do list_, подключаемого к вашей мобильной службе. Файлы проекта будут сжаты, поэтому перейдите к их расположению и распакуйте файлы на вашем компьютере.


## Запуск приложения для Android

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

### Просмотр кода (необязательно)

Просмотреть исходный код завершенного приложения можно [здесь](https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio).


Чтобы просмотреть версию этого учебника для Eclipse, перейдите к следующей статье: [Приступая к работе (Eclipse)](mobile-services-android-get-started-ec.md).

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с данными ]<br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности ] <br/>Дополнительные сведения о проверке подлинности учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] <br/>Сведения об отправке в приложение простейших push-уведомлений.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-android-get-started-ec.md
[Приступая к работе с данными ]: mobile-services-android-get-started-data.md
[Приступая к работе с проверкой подлинности ]: mobile-services-android-get-started-users.md
[Приступая к работе с push-уведомлениями]: mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO2-->