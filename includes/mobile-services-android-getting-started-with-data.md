После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1. Убедитесь, что в теге **dependencies** в файле *build.gradle (приложение Module)* присутствуют следующие строки, а если их нет — добавьте их. Этот код добавляет ссылки на пакет SDK клиента Android мобильных служб.

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2+'


2. Теперь перестройте проект, щелкнув **Синхронизировать проект с файлами Gradle**.

3. Откройте файл AndroidManifest.xml и добавьте в него следующую строку, которая позволяет приложению получить доступ к мобильным службам в Azure.

		<uses-permission android:name="android.permission.INTERNET" />


4. В обозревателе проектов откройте файл TodoActivity.java, расположенный в папке **GetStartedWithData => app => src => java**, и раскомментируйте следующие строки кода:



		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
5. Закомментируйте следующие строки:

		import java.util.ArrayList;
		import java.util.List;

6. Мы удалим расположенный в памяти список, используемый приложением в данный момент, чтобы заменить его мобильной службой. В классе **ToDoActivity** раскомментируйте следующую строку кода, которая определяет существующий список **toDoItemList**.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Сохраните файл, и проект укажет ошибки сборки. Найдите три оставшихся места, где используется переменная `toDoItemList`, и раскомментируйте указанные разделы. Это полностью удалит список, расположенный в памяти.

8. Теперь следует добавить мобильную службу. Раскомментируйте следующие строки кода:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Найдите класс *ProgressFilter* в нижней части файла и раскомментируйте его. Для этого класса отображается индикатор "Загрузка" при выполнении сетевых операций на *MobileServiceClient*.


10. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

11. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

12. В методе **onCreate** раскомментируйте следующие строки кода, в которых определяется переменная **MobileServiceClient**:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Код создает экземпляр *MobileServiceClient*, используемый для доступа к мобильной службе. Также будет создан экземпляр *MobileServiceTable*, который используется для хранения данных прокси-сервера в мобильной службе.

13. В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес и ключ приложения из мобильной службы в указанном порядке.



14. Раскомментируйте следующие строки метода **checkItem**:

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	В мобильную службу будет отправлено обновление элемента. Элементы, отмеченные флажками, будут удалены из адаптера.
    
15. Раскомментируйте следующие строки метода **addItem**:
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	Этот код создает новый элемент и вставляет его в таблицу в удаленной мобильной службе.

16. Раскомментируйте следующие строки метода **refreshItemsFromTable**:

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	При этом будет создан запрос к мобильной службе и будут возвращены все элементы, которые не помечены как завершенные. Элементы добавляются к адаптеру для привязки.
		

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO2-->