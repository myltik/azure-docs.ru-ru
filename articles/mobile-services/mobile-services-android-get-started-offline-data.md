<properties
	pageTitle="Добавление синхронизации автономных данных в приложение мобильных служб для Android | Microsoft Azure"
	description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в приложении на Android."
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="ricksal"/>

# Добавление синхронизации автономных данных в приложение мобильных служб Android

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Включение автономной синхронизации для мобильного приложения Android](../app-service-mobile/app-service-mobile-android-get-started-offline-data.md).

## Сводка

Мобильные приложения могут терять подключение к сети при нахождении вне зоны покрытия или в случае проблем на стороне оператора. При работе с мобильными приложениями, например с приложением для строительной отрасли на удаленной стройплощадке, может возникнуть необходимость вводить данные в автономном режиме и синхронизировать их с Azure позже. Синхронизация автономных данных в мобильных службах Azure позволяет продолжать работу даже при потере подключения к сети, что очень важно для многих сценариев использования мобильных приложений. При включенной синхронизации автономных данных вы работаете с локальной копией таблицы Azure SQL Server и периодически синхронизируете ее с серверной.

Используя этот учебник, вы добавите поддержку синхронизации автономных данных в приложение, созданное с помощью учебника [Быстрый запуск мобильных служб], а затем протестируете его, добавив автономные данные, синхронизировав их с оперативной базой данных и проверив изменения на классическом портале Azure.

Независимо от того, работаете вы в автономном режиме или онлайн, при одновременном внесении изменений в данные могут возникать конфликты. Конфликты синхронизации и выбор версии изменений, которую нужно зафиксировать, мы рассмотрим в следующий раз. А пока будем считать, что никаких конфликтов синхронизации нет и изменения, которые вы вносите в данные, будут применяться непосредственно на сервере Azure SQL Server.


## Что необходимо для начала работы

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Обновление приложения для поддержки синхронизации автономных данных

При включенной синхронизации автономных данных операции чтения и записи осуществляются с помощью *таблицы синхронизации* (с помощью интерфейса *IMobileServiceSyncTable*), которая является частью базы данных **SQLight** на вашем устройстве.

Для принудительной отправки изменений на устройстве в мобильные службы Azure и получения изменений из мобильных служб используйте *контекст синхронизации* (*MobileServiceClient.SyncContext*), который инициализируется с помощью локальной базы данных.

1. Предоставьте разрешение на проверку подключения к сети, добавив следующий код в файл *AndroidManifest.xml*:

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. Добавьте следующие операторы **import** в файл *ToDoActivity.java*.

		import java.util.Map;

		import android.widget.Toast;

		import com.microsoft.windowsazure.mobileservices.table.query.Query;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. В верхней части класса `ToDoActivity` замените в объявлении переменной `mToDoTable` класс `MobileServiceTable<ToDoItem>` на класс `MobileServiceSyncTable<ToDoItem>`.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	Здесь вы определяете таблицу синхронизации.

4. Чтобы инициализировать локальное хранилище, добавьте в метод `onCreate` следующие строки после создания экземпляра `MobileServiceClient`:

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. После приведенного выше кода, который находится внутри блока `try`, добавьте дополнительный блок `catch` после блока `MalformedURLException`:

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. Добавьте следующий метод для проверки подключения к сети:

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. Добавьте следующий метод для синхронизации между локальным хранилищем *SQL Light* и Azure SQL Server:

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {

					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. Добавьте следующий код в метод `onCreate` предпоследней строкой, непосредственно перед вызовом `refreshItemsFromTable`:

			syncAsync();

	В результате при запуске устройства будет выполняться синхронизация с таблицей Azure. В противном случае будет отображаться содержимое, добавленное ранее в локальное хранилище в автономном режиме.



9. Измените метод `refreshItemsFromTable`, чтобы он использовал этот запрос (первая строка в блоке `try`):

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. Замените содержимое блока `if` в методе `onOptionsItemSelected` следующим кодом:

			syncAsync();
			refreshItemsFromTable();

	Данный код выполняется при нажатии кнопки **Обновить** в правом верхнем углу. Это основной способ синхронизации локального хранилища с Azure, который дополняет синхронизацию во время запуска. Учитывая, что извлечение данных из Azure — это относительно затратная операция, данный способ является эффективным, поскольку способствует применению пакетной обработки изменений при синхронизации. При необходимости вы можете предусмотреть в приложении синхронизацию при каждом изменении, добавив вызов `syncAsync` в методы `addItem` и `checkItem`.


## Тестирование приложения

В этом разделе мы протестируем поведение приложения при доступной сети Wi-Fi, а затем отключим Wi-Fi и рассмотрим автономный сценарий.

Добавляемые элементы данных будут находиться в локальном хранилище SQL Light, но не будут синхронизированы с мобильной службой, пока вы не нажмете кнопку **Обновить**. У разных приложений могут быть разные требования к синхронизации данных, но для демонстрационных целей этого учебника мы будем считать, что пользователь явно запрашивает ее.

При нажатии этой кнопки запускается новая фоновая задача, которая сначала принудительно отправляет все изменения в локальном хранилище, используя контекст синхронизации, а затем извлекает все измененные данные из Azure в локальную таблицу.


### Тестирование в режиме онлайн

В рамках этого сценария выполните следующие действия.

1. Добавьте несколько новых элементов на устройстве.
2. Убедитесь, что элементы не отображаются на портале.
3. Нажмите кнопку **Обновить** и убедитесь, что они появились.
4. Измените или добавьте элемент на портале, затем нажмите кнопку **Обновить** и убедитесь, что изменения отображаются на устройстве.

### Тестирование в автономном режиме

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. Переведите устройство или эмулятор в режим *В самолете*. Это позволит смоделировать автономный сценарий.

2. Добавьте несколько элементов *ToDo* или отметьте некоторые элементы как завершенные. Выйдите из эмулятора (или принудительно закройте приложение) и перезапустите его. Убедитесь, что изменения на устройстве по прежнему отображаются (так как они хранятся в локальном хранилище SQL Light).

3. Просмотрите содержимое таблицы *TodoItem* в Azure. Убедитесь, что новые элементы _не_ были синхронизированы с сервером.

   - Для серверной части JavaScript перейдите на классический портал Azure и откройте вкладку «Данные», чтобы просмотреть содержимое таблицы `TodoItem`.
   - Для серверной части .NET просмотрите содержимое таблицы либо с помощью инструмента SQL, например *SQL Server Management Studio*, или с помощью клиента REST, например *Fiddler* или *Postman*.

4. Включите Wi-Fi в эмуляторе или на устройстве. Затем нажмите кнопку **Обновить**.

5. Снова просмотрите данные TodoItem на классическом портале Azure. Должны отображаться как новые, так и измененные TodoItem.


## Дальнейшие действия

* [Использование обратимого удаления в мобильных службах][Soft Delete]

## Дополнительные ресурсы

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure]

* [Azure Friday: приложения с поддержкой автономной работы в мобильных службах Azure]. (Примечание. Демонстрации предназначены для операционной системы Windows, однако обсуждение функций справедливо для всех платформ.)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: приложения с поддержкой автономной работы в мобильных службах Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Быстрый запуск мобильных служб]: mobile-services-android-get-started.md

<!---HONumber=AcomDC_0309_2016-->