<properties urlDisplayName="Get Started with Mobile Services for Xamarin Android apps" pageTitle="Приступая к работе с мобильными службами в приложениях Xamarin для Android - мобильные службы Azure" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin Android development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin Android apps" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение Xamarin Android с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Для создания мобильной службы, которая позволяет писать серверную бизнес-логику на языке JavaScript, ознакомьтесь с разделом [Серверная версия базы данных JavaScript] этого раздела.

>[WACOM.NOTE]В этом разделе показано, как создать новый проект мобильной службы с помощью портала управления Azure. Используя Visual Studio 2013 с обновлением 2, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. на странице [Краткое руководство: Добавление мобильной службы (внутренний сервер .NET)](http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx)

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям Xamarin Android. 

>[WACOM.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started" target="_blank">Бесплатная пробная версия Azure</a>.<br />Для работы с этим учебником требуется <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения Xamarin Android

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе предстоит скачать новое приложение Xamarin Android и проект службы для мобильной службы.

1. На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.
   
2. На вкладке быстрого запуска нажмите кнопку **Xamarin** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение Xamarin**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на Xamarin Android, подключенного к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, загрузите и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.  

4. Если вы еще не сделали этого, скачайте и установите [Xamarin Studio] или Xamarin для Visual Studio.

5. В разделе **Скачивание и публикация службы в облаке** выберите **Android** и нажмите кнопку **Загрузить**. 

  	При этом загружается решение, содержащее проекты для мобильной службы и примера приложения "To do list", которое подключается к вашей мобильной службе. Сохраните упакованный файл проекта на своем локальном компьютере и зафиксируйте, в каком месте он был сохранен.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Запуск приложения Xamarin Android

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении мобильной службы в Visual Studio или Xamarin Studio.

	![][8]

	![][9]

2.  Нажмите клавишу **Запуск** для построения проекта, после чего запустите приложение. Вам будет предложено выбрать эмулятор или подключенное USB-устройство. 

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

3.  В приложении введите содержательный текст, например "Завершить работу с учебником", и щелкните значок "плюс" (**+**).

	![][10]

	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [WACOM.NOTE] 
   	> Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# ToDoActivity.cs.
    
## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Начало работы с автономной синхронизацией данных]
  <br/>Использование автономной синхронизации данных для повышения быстроты отклика и надежности приложения.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Устранение неполадок сервера базы данных мобильных служб .NET]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET. 

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Дальнейшие действия]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Начало работы с автономной синхронизацией данных]: /ru-ru/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript и HTML ]: mobile-services-win8-javascript/
[Портал управления]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started
[Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Устранение неполадок сервера базы данных мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin для Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
