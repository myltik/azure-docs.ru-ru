<properties
	pageTitle="Включение автономной синхронизации для мобильного приложения Azure (Android)"
	description="Узнайте, как использовать мобильные приложения службы приложений для кэширования и синхронизации автономных данных в приложении Android."
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Включение автономной синхронизации для мобильного приложения Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Обзор

В этом учебнике рассматривается функция автономной синхронизации мобильных приложений Azure для Android. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением (просматривать, добавлять или изменять данные) даже при отсутствии подключения к сети. Изменения хранятся в локальной базе данных; после подключения устройства к сети эти изменения синхронизируются с удаленным внутренним сервером.

Если вы впервые работаете с мобильными приложениями Azure, сначала изучите учебник [Создание приложения Android]. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для доступа к данным. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## Обновление приложения для поддержки синхронизации автономных данных

При включенной синхронизации автономных данных операции чтения и записи осуществляются с помощью *таблицы синхронизации* (с помощью интерфейса *IMobileServiceSyncTable*), которая является частью базы данных **SQLight** на вашем устройстве.

Для принудительной отправки изменений на устройстве в мобильные службы Azure и получения изменений из мобильных служб используйте *контекст синхронизации* (*MobileServiceClient.SyncContext*), который инициализируется с помощью локальной базы данных.

1. В `TodoActivity.java` закомментируйте существующее определение `mToDoTable` и раскомментируйте версию таблицы синхронизации.

	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. В методе `onCreate` закомментируйте существующую инициализацию `mToDoTable` и раскомментируйте следующее определение.

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. В `refreshItemsFromTable` закомментируйте определение `results` и раскомментируйте следующее определение.

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Закомментируйте определение `refreshItemsFromMobileServiceTable`.

5. Раскомментируйте определение `refreshItemsFromMobileServiceTableSyncTable`.

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. Раскомментируйте определение `sync`.

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## Тестирование приложения

В этом разделе мы протестируем поведение приложения при доступной сети Wi-Fi, а затем отключим Wi-Fi и рассмотрим автономный сценарий.

Добавляемые элементы данных будут находиться в локальном хранилище SQLight, но не будут синхронизированы с мобильной службой, пока вы не нажмете кнопку **Обновить**. У разных приложений могут быть разные требования к синхронизации данных, но для демонстрационных целей этого учебника мы будем считать, что пользователь явно запрашивает ее.

При нажатии этой кнопки запускается новая фоновая задача, которая сначала принудительно отправляет все изменения в локальном хранилище, используя контекст синхронизации, а затем извлекает все измененные данные из Azure в локальную таблицу.

### Тестирование в автономном режиме

1. Переведите устройство или эмулятор в режим *В самолете*. Это позволит смоделировать автономный сценарий.

2. Добавьте несколько элементов *ToDo* или отметьте некоторые элементы как завершенные. Выйдите из эмулятора (или принудительно закройте приложение) и перезапустите его. Убедитесь, что изменения на устройстве по прежнему отображаются (так как они хранятся в локальном хранилище SQLight).

3. Просмотрите содержимое таблицы Azure *TodoItem* либо с помощью инструмента SQL, например *SQL Server Management Studio*, или с помощью клиента REST, например *Fiddler* или *Postman*. Убедитесь, что новые элементы _не_ были синхронизированы с сервером.

   	+ Для серверной части Node.js перейдите на [портал Azure](https://portal.azure.com/), затем на странице серверной части своего мобильного приложения щелкните **Easy Tables** > **TodoItem**, чтобы просмотреть содержимое таблицы `TodoItem`.
   	+ Для серверной части .NET просмотрите содержимое таблицы либо с помощью средства SQL, например *SQL Server Management Studio*, или с помощью клиента REST, например *Fiddler* или *Postman*.

4. Включите Wi-Fi в эмуляторе или на устройстве. Затем нажмите кнопку **Обновить**.

5. Снова просмотрите данные TodoItem на портале Azure. Должны отображаться как новые, так и измененные TodoItem.

## Дополнительные ресурсы

* [Автономная синхронизация данных в мобильных приложениях Azure]

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure] \(примечание: видео рассказывает о мобильных службах, однако точно так же автономная синхронизация работает в мобильных приложениях Azure)


<!-- URLs. -->

[Автономная синхронизация данных в мобильных приложениях Azure]: ../app-service-mobile-offline-data-sync.md

[Создание приложения Android]: ../app-service-mobile-android-get-started.md

[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0727_2016-->