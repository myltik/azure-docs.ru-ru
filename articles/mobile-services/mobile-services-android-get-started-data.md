<properties
	pageTitle="Приступая к работе с данными в Android (серверная часть JavaScript) | Microsoft Azure"
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении Android (серверная часть JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Добавление мобильных служб в существующее приложение Android (серверная часть JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Использование клиентской библиотеки Android для мобильных приложений](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).

## Сводка

В этом разделе показано, как использовать мобильные службы Azure для добавления постоянных данных в приложение Android. В этом учебнике вам предстоит скачать приложение, которое хранит данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением, чтобы оно хранило и обновляло данные в мобильных службах Azure, а не на локальном компьютере, а затем использовать классический портал Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

Этот учебник поможет лучше понять, как мобильные службы Azure могут сохранять данные приложения Android и извлекать их из него. В этом разделе рассматриваются многие действия, которые уже выполнялись в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами](mobile-services-android-get-started.md).

## Предварительные требования

Для работы с этим учебником требуется:

- учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357).


- [пакет SDK для Android для мобильных служб Azure];
- [интегрированная среда разработки Android Studio](https://developer.android.com/sdk/index.html), которая включает в себя пакет SDK для Android, а также Android 4.2 или более поздней версии. Для загружаемого проекта GetStartedWithData требуется Android 4.2 или более поздней версии. Однако для пакета SDK для мобильных служб требуется только Android 2.2 или более поздней версии.

## Пример кода

Просмотреть полный исходный код можно [здесь](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio).

## Загрузка пакета GetStartedWithData

###Получение кода примера

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Проверка и запуск примера кода

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Создание мобильной службы на классическом портале Azure

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Добавление новой таблицы в мобильную службу

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Обновление приложения для доступа к данным с помощью мобильной службы

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Тестирование работы приложения с новой мобильной службой

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1. В меню **Запуск** выберите **Запуск приложения**, чтобы запустить проект.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. Перейдите на [классический портал Azure], выберите пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]

   	Обратите внимание, что таблица **TodoItem** теперь содержит данные с рядом значений, созданных мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Android.

## Устранение неполадок

### Проверка версии Android SDK

[AZURE.INCLUDE [Проверка пакета SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android. Обратитесь к одному из следующих учебников по Android:

* [Приступая к работе с проверкой подлинности](mobile-services-android-get-started-users.md)<br/> Дополнительные сведения о проверке подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями](mobile-services-javascript-backend-android-get-started-push.md) <br/>Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->

[классический портал Azure]: https://manage.windowsazure.com/
[пакет SDK для Android для мобильных служб Azure]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0727_2016-->