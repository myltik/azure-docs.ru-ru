После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1.  Если у вас еще не установлен пакет [Android SDK для мобильных служб][], загрузите его и распакуйте сжатые файлы.

2.  Скопируйте файлы `.jar` из папки `mobileservices` пакета SDK в папку `libs` проекта GetStartedWithData.

3.  В обозревателе пакетов Eclipse щелкните правой кнопкой мыши папку `libs`, нажмите кнопку **Обновить**, после чего появятся скопированные JAR-файлы.

    Это приведет к добавлению в рабочую область ссылки на пакет SDK для мобильных служб.

4.  Откройте файл AndroidManifest.xml и добавьте в него следующую строку, которая позволяет приложению получить доступ к мобильным службам в Azure.

        <uses-permission android:name="android.permission.INTERNET" />

5.  В обозревателе пакетов откройте файл TodoActivity.java, расположенный в пакете com.example.getstartedwithdata, и раскомментируйте следующие строки кода:

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Мы удалим расположенный в памяти список, используемый приложением в данный момент, чтобы заменить его мобильной службой. В классе **ToDoActivity** раскомментируйте следующую строку кода, которая определяет существующий список **toDoItemList**.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  Сохраните файл, и проект укажет ошибки сборки. Найдите три оставшихся места, где используется переменная `toDoItemList`, и раскомментируйте указанные разделы. Также удалите `import java.util.ArrayList`. Это полностью удалит список, расположенный в памяти.

8.  Теперь следует добавить мобильную службу. Раскомментируйте следующие строки кода:

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  Найдите класс ProgressFilter в нижней части файла и раскомментируйте его. Для этого класса отображается индикатор "Загрузка" при выполнении сетевых операций на MobileServiceClient.

10. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

11. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

    ![][]

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

    Код создает экземпляр MobileServiceClient, используемый для доступа к мобильной службе. Также будет создан экземпляр MobileServiceTable, который используется для хранения данных прокси-сервера в мобильной службе.

13. В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес и ключ приложения из мобильной службы в этом порядке.

14. Раскомментируйте следующие строки метода **checkItem**:

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    В мобильную службу будет отправлено обновление элемента. Элементы, отмеченные флажками, будут удалены из адаптера.

15. Раскомментируйте следующие строки метода **addItem**:

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    Этот код создает новый элемент и вставляет его в таблицу в удаленной мобильной службе.

16. Раскомментируйте следующие строки метода **refreshItemsFromTable**:

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    При этом будет создан запрос к мобильной службе и будут возвращены все элементы, которые не помечены как завершенные. Элементы добавляются к адаптеру для привязки.

<!-- URLs. -->

  [Android SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  []: ./media/download-android-sample-code/mobile-dashboard-tab.png
