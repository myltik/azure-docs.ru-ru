---
title: Включение автономной синхронизации для приложения универсальной платформы Windows (UWP) с помощью мобильных приложений | Документация Майкрософт
description: Узнайте, как использовать мобильное приложение Azure для кэширования и синхронизации автономных данных в приложении универсальной платформы Windows (UWP).
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a16de4cef82c29f9b6becfae1901662ee1936934
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594485"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Включение автономной синхронизации для приложения для Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Обзор
В этом учебнике показано, как добавить поддержку автономных функций в приложение универсальной платформы Windows (UWP) с помощью серверной части мобильного приложения Azure. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением — просматривать, добавлять или изменять данные — даже при отсутствии подключения к сети. Изменения сохраняются в локальной базе данных. Как только устройство возвращается в режим подключения к сети, эти изменения синхронизируются с удаленной внутренним сервером.

Из этого руководства вы узнаете, как обновить проект приложения UWP, описанного в руководстве [Создание приложения Windows], чтобы обеспечить поддержку автономных функций мобильных приложений Azure. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений доступа к данным. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## <a name="requirements"></a>Требования
Для работы с этим руководством требуется следующее:

* Visual Studio 2013 в Windows 8.1 или более поздней версии;
* Выполнение заданий на странице [Создание приложения Windows][Создание приложения Windows].
* [Хранилище SQLite для мобильных служб Azure][sqlite store nuget]
* [SQLite для разработки универсальной платформы Windows](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Обновление клиентского приложения для поддержки автономных функций
Автономные функции мобильных приложений Azure позволяют взаимодействовать с локальной базой данных в случае автономной работы. Чтобы использовать эти функции в приложении, необходимо инициализировать [SyncContext][synccontext] в локальном хранилище. Затем необходимо сослаться на таблицу с помощью интерфейса [IMobileServiceSyncTable][IMobileServiceSyncTable] . SQLite используется как локальное хранилище на устройстве.

1. Установите [среду выполнения SQLite для универсальной платформы Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. В Visual Studio откройте диспетчер пакетов NuGet для проекта приложения UWP, созданного в руководстве [Создание приложения Windows].
    Найдите и установите пакет NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
3. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** > **Добавить ссылку...** > **Универсальная платформа Windows** > **Расширения**, а затем включите **SQLite для универсальной платформы Windows** и **среду выполнения Visual C++ 2015 для приложений универсальной платформы Windows**.

    ![Добавить ссылку на SQLite UWP][1]
4. Откройте файл MainPage.xaml.cs и раскомментируйте определение `#define OFFLINE_SYNC_ENABLED`.
5. В Visual Studio нажмите клавишу **F5** , чтобы выполнить сборку и запустить клиентское приложение. Приложение работает так же, как и до включения автономной синхронизации. Тем не менее, локальная база данных теперь заполняется данными, которые можно использовать в автономном сценарии.

## <a name="update-sync"></a>Обновление приложения для отключения от серверной части
В этом разделе вы разорвете связь с серверной частью мобильного приложения для имитации автономного режима. При добавлении элементов данных обработчик исключений сообщает, что приложение работает в автономном режиме. В этом состоянии новые элементы добавляются в локальное хранилище и будут синхронизированы с серверной частью мобильного приложения при следующей отправке данных в подключенном состоянии.

1. Измените файл App.xaml.cs в общем проекте. Закомментируйте инициализацию **MobileServiceClient** и добавьте следующую строку, использующую недопустимый URL-адрес мобильного приложения:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Чтобы продемонстрировать поведение в автономном режиме, отключите на устройстве Wi-Fi и мобильную связь и включите режим "в самолете".
2. Нажмите клавишу **F5** , чтобы выполнить сборку и запустить приложение. Обратите внимание, синхронизацию не удалось выполнить при обновлении во время запуска приложения.
3. Введите новые элементы. Учтите, что при каждом нажатии кнопки **Сохранить** отправка завершается сбоем с состоянием [CancelledByNetworkError]. Однако новые элементы списка существуют в локальном хранилище, пока они не будут перемещены в серверную часть мобильного приложения.  В рабочем приложении при подавлении этих исключений клиентское приложение ведет себя так, как если бы оно по-прежнему было подключено к серверной части мобильного приложения.
4. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.
5. В Visual Studio в откройте **обозреватель сервера**. Перейдите к своей базе данных в **Azure**->**Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**. Теперь можно перейти к таблице базы данных SQL и ее содержимому. Убедитесь, что данные в серверной базе данных не изменились.
6. (Необязательно.) Воспользуйтесь инструментом REST, например Fiddler или Postman, чтобы выполнить запрос к мобильной серверной части с помощью запроса GET вида `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения к серверной части мобильного приложения
В этом разделе вы повторно подключите приложение к серверной части мобильного приложения. Эти изменения моделируют повторное подключение сети в приложении.

При первом запуске приложения обработчик событий `OnNavigatedTo` вызывает `InitLocalStoreAsync`. Этот метод, в свою очередь, вызывает `SyncAsync` для синхронизации локального хранилища с серверной базой данных. Приложение пытается выполнить синхронизацию при запуске.

1. Откройте файл App.xaml.cs в общем проекте и раскомментируйте предыдущую инициализацию `MobileServiceClient` , чтобы использовать правильный URL-адрес мобильного приложения.
2. Нажмите клавишу **F5** , чтобы перестроить и запустить приложение. Приложение синхронизирует локальные изменения с серверной частью мобильного приложения Azure, применяя операции отправки и извлечения после выполнения обработчика событий `OnNavigatedTo`.
3. (Необязательно.) Просмотрите обновленные данные, используя обозреватель объектов SQL Server или инструмент REST, например Fiddler. Обратите внимание, что данные синхронизированы между базой данных серверной части мобильного приложения Azure и локальным хранилищем.
4. В приложении установите флажки рядом с некоторыми элементами, отметив их в локальном хранилище как выполненные.

   `UpdateCheckedTodoItem` вызывает `SyncAsync` для синхронизации каждого выполненного элемента с серверной частью мобильного приложения. `SyncAsync` вызывает операцию отправки и извлечения данных. Тем не менее **при каждом выполнении операции извлечения данных в отношении таблицы, в которую клиент внес изменения, операция отправки данных всегда выполняется автоматически**. Эта особенность позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними. Такое поведение может привести к непредвиденной отправке данных.  Дополнительные сведения об этом поведении см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## <a name="api-summary"></a>Сводные данные API
Для поддержки автономных функций мобильных служб мы использовали интерфейс [IMobileServiceSyncTable] и инициализировали [MobileServiceClient.SyncContext][synccontext] с использованием локальной базы данных SQLite. В автономном режиме обычные операции CRUD для мобильных приложений работают так, как если бы приложение по-прежнему было подключено, но операции выполнялись в локальном хранилище. Для синхронизации локального хранилища с сервером используются следующие методы.

* **[PushAsync.]** Так как этот метод является элементом объекта [IMobileServicesSyncContext], изменения во всех таблицах будут отправляться в серверную часть приложения. Только записи с локальными изменениями будут отправляться на сервер.
* **[PullAsync]**. Извлечение запускается из объекта [IMobileServiceSyncTable]. При наличии отслеживаемых изменений в таблице выполняется неявная отправка, чтобы убедиться в том, что все таблицы в локальном хранилище и их взаимосвязи остаются согласованными. Параметр *pushOtherTables* определяет, будет ли выполнена для других таблиц в контексте неявная отправка. Параметр *query* использует [IMobileServiceTableQuery<T>][IMobileServiceTableQuery] или строку запроса OData для фильтрации возвращаемых данных. Параметр *queryId* используется для определения добавочной синхронизации. Дополнительные сведения см. в разделе [Как работает автономная синхронизация](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync.]** Приложение должно периодически вызывать этот метод, чтобы удалить устаревшие данные из локального хранилища. Используйте параметр *force* , чтобы удалить все изменения, которые не были синхронизированы.

Дополнительные сведения об этих понятиях см. в разделе [Как работает автономная синхронизация](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Подробнее
В следующих статьях приводятся дополнительные сведения о функции автономной синхронизации мобильных приложений.

* [Автономная синхронизация данных в мобильных приложениях Azure]
* [Использование пакета SDK .NET для мобильных приложений Azure][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md
[Создание приложения Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync.]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync.]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
