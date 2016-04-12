<properties
	pageTitle="Начало работы с мобильными службами Azure для приложений Android (серверная часть JavaScript)"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений Android (серверная часть JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="reikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="03/05/2016"
	ms.author="ricksal"/>

# Начало работы с мобильными службами для Android (серверная часть JavaScript)

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Создание приложения Android в мобильных приложениях Azure](../app-service-mobile/app-service-mobile-android-get-started.md).

В этом учебнике показано, как добавить облачную серверную службу в приложение Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение **To do list**, хранящее данные приложения в новой мобильной службе.

> [AZURE.VIDEO mobile-get-started-android]

Снимок экрана завершенного приложения приведен ниже:

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Предварительные требования

Для работы с этим руководством требуются [средства разработчика Android](https://developer.android.com/sdk/index.html), которые включают в себя интегрированную среду разработки Android Studio и новейшую платформу Android. Требуется Android 4.2 или более поздняя версия.

Загруженный проект быстрого запуска содержит пакет SDK мобильных служб Azure для платформы Android.

> [AZURE.IMPORTANT] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения для Android

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на классическом портале Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к своей мобильной службе.

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1.  На классическом портале Azure щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Android** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение на Android**.

   	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	Здесь показаны три простых шага для создания приложения на платформе Android, подключенного к вашей мобильной службе.

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Если вы еще не сделали этого, загрузите и установите [Средств разработчика Android](https://go.microsoft.com/fwLink/p/?LinkID=280125) на ваш локальный компьютер или виртуальную машину.

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.


5. Загрузите приложение, нажав кнопку **Загрузить**.

## Запуск приложения для Android

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с данными ]<br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности ] <br/>Дополнительные сведения о проверке подлинности учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] <br/>Сведения об отправке в приложение простейших push-уведомлений.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-android-get-started-ec.md
[Приступая к работе с данными ]: mobile-services-android-get-started-data.md
[Приступая к работе с проверкой подлинности ]: mobile-services-android-get-started-users.md
[Приступая к работе с push-уведомлениями]: mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

<!-----HONumber=AcomDC_0309_2016-->