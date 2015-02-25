<properties pageTitle="Приступая к работе с мобильными службами в приложениях Xamarin для iOS - мобильные службы Azure" description="Этот учебник поможет приступить к использованию мобильных служб Azure для разработки приложений Xamarin для iOS" services="" documentationCenter="xamarin" authors="lindydonna" manager="dwrede" editor="mollybos"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В данном учебнике описывается добавление облачных служб внутреннего сервера в приложение Xamarin iOS с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

>[AZURE.NOTE]В этом разделе показано, как создать новый проект мобильной службы с помощью портала управления Azure. Используя Visual Studio 2013 с обновлением 2, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. на странице [Краткое руководство: добавление мобильной службы (серверная часть .NET)](http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx).

Снимок экрана завершенного приложения приведен ниже:

![][0]


Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям Xamarin iOS. 

>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Бесплатная пробная версия Azure</a>.<br />Для работы с этим учебником требуется <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## Создание мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения Xamarin iOS

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе предстоит скачать новое приложение Xamarin iOS и проект службы для мобильной службы.

1. В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.
   
2. На вкладке быстрого запуска щелкните **Xamarin** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения Xamarin**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на Xamarin iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если это еще не было сделано, загрузите и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.  

4. Загрузите и установите [Xcode] 4.4 или более поздней версии, а также [Xamarin Studio]. Вы также можете использовать Xamarin для Visual Studio.

5. В разделе **Загрузка и публикация службы в облаке** выберите **iOS** и нажмите кнопку **Загрузить**. 

  	При этом загружается решение, содержащее проекты для мобильной службы и примера приложения _To do list_, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Запуск приложения Xamarin iOS

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении мобильной службы в Visual Studio или Xamarin Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запустить**, чтобы создать клиентский проект и запустить приложение в эмуляторе iPhone.

3. В приложении введите содержательный текст, например _Complete the tutorial_, и щелкните значок "плюс" (**+**).

	![][10]

	Запрос POST отправляется в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

>[AZURE.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# QSTodoService.cs.

    
## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с автономной синхронизацией данных]
  <br/>Узнайте, как функция быстрого запуска обеспечивает высокую скорость реагирования и надежность приложений с помощью автономной синхронизации данных.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Устранение неполадок серверной части .NET мобильных служб]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET. 

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Дальнейшие действия]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Приступая к работе с автономной синхронизацией данных]: /ru-ru/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript и HTML]: mobile-services-win8-javascript/
[Портал управления]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started
[Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Устранение неполадок серверной части .NET мобильных служб]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin для Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=42-->
