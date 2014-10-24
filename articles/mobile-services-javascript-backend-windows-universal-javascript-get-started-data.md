<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

В этом разделе показано, как применять мобильные службы Azure для эффективного использования данных в универсальном приложении Windows. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений Windows для Windows и Windows Phone][Создание универсальных приложений Windows для Windows и Windows Phone].

В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для универсального приложения Windows, которое хранит данные в памяти, создать мобильную службу, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

> [WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio Express 2013 для Windows с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Эти же инструкции можно использовать для подключения мобильной службы к приложению Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как подключить мобильную службу к приложению Windows Phone 8.0 или Windows Phone Silverlight 8.1, см. в разделе [Приступая к работе с данными для Windows Phone][Приступая к работе с данными для Windows Phone].

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Добавление таблицы для хранения данных][Добавление таблицы для хранения данных]
4.  [Обновление приложения для использования мобильных служб][Обновление приложения для использования мобильных служб]
5.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

Для работы с этим учебником требуется:

-   Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
-   [Visual Studio Express 2013 для Windows][Visual Studio Express 2013 для Windows] (с обновлением 2 или более поздней версии).

## <a name="download-app"></a>Загрузка проекта GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][mobile-services-windows-universal-javascript-download-project]]

## <a name="create-service"></a>Создание мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][mobile-services-create-new-service-vs2013]]

1.  В обозревателе решений перейдите во вложенную папку **services\\mobileService\\scripts**, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот скрипт была добавлена в файл default.html, эта переменная доступна для всех файлов скриптов, на которые также указывает ссылка на этой странице.

2.  Откройте файл проекта default.html, найдите ссылку на новый файл скрипта service.js и убедитесь в том, что путь ссылки выглядит следующим образом:

        <script src="/services/mobileServices/scripts/todolist.js">

    В настоящее время в Visual Studio есть ошибка, из-за которой создается путь с неправильным именем папки.

3.  Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт **Добавить**, щелкните **Подключенная служба...**, выберите только что созданную мобильную службу и нажмите кнопку **ОК**.

    Тот же новый файл с кодом добавляется в проект приложения Магазина Windows Phone. Не забудьте также исправить путь ссылки, добавленный в файл default.html.

## <a name="add-table"></a>Добавление новой таблицы в мобильную службу

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][mobile-services-create-new-table-vs2013]]

> [WACOM.NOTE]Новые таблицы создаются со столбцами Id, \_\_createdAt, \_\_updatedAt и \_\_version. Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема][Динамическая схема].

# <a name="update-app"></a>Обновление приложения для использования мобильной службы

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][mobile-services-windows-javascript-update-data-app]]

## <a name="test-azure-hosted"></a>Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][mobile-services-windows-universal-test-app]]

1.  На [Портале управления][Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

    <p>
2.  Откройте вкладку **Данные**, а затем щелкните **Обзор**.

    Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

![][]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в универсальном приложении Windows возможности работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
    Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
    Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]
    Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.

 	

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Создание универсальных приложений Windows для Windows и Windows Phone]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx
  [Приступая к работе с данными для Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Загрузка проекта приложения для Магазина Windows]: #download-app
  [Создание мобильной службы]: #create-service
  [Добавление таблицы для хранения данных]: #add-table
  [Обновление приложения для использования мобильных служб]: #update-app
  [Тестирование работы приложения с мобильными службами]: #test-app
  [Бесплатная пробная версия Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Express 2013 для Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Динамическая схема]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [Портале управления]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/
