
##<a name="update-app"></a>Обновление приложения для вызова пользовательского API

1. Мы добавим кнопку с подписью "Выполнить все" рядом с существующей кнопкой и переместим обе кнопки на одну строку вниз. Откройте в Android Studio файл *res\\layout\\activity\_to\_do.xml* проекта быстрого запуска, найдите элемент **LinearLayout**, содержащий элемент **Button** с именем `buttonAddToDo`. Скопируйте элемент **LinearLayout** и вставьте его непосредственно рядом с оригиналом. Удалить элемент **Button** из первого элемента **LinearLayout**.

2. Во втором элементе **LinearLayout** удалите элемент **EditText** и добавьте следующий код сразу после существующего элемента **Button**:

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

	В результате на страницу будет добавлена новая кнопка, на отдельной строке, рядом с существующей кнопкой.

3. Второй элемент **LinearLayout** теперь выглядит следующим образом:

	     <LinearLayout
	        android:layout_width="match_parent" 
	        android:layout_height="wrap_content" 
	        android:background="#71BCFA"
	        android:padding="6dip"  >
	        <Button
	            android:id="@+id/buttonAddToDo"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="addItem"
	            android:text="@string/add_button_text" />
	        <Button
	            android:id="@+id/buttonCompleteItem"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="completeItem"
	            android:text="@string/complete_button_text" />
	    </LinearLayout>
	

4. Откройте файл res\\values\\string.xml и добавьте следующую строку кода:

    	<string name="complete_button_text">Complete All</string>



5. В обозревателе проектов щелкните правой кнопкой мыши имя проекта в папке *src* (`com.example.{your projects name}`), выберите пункт **Создать**, а затем **Классы**. В диалоговом окне введите **MarkAllResult** в поле имя класса, нажмите кнопку "ОК" и замените результирующее определение класса на следующий код:

		import com.google.gson.annotations.SerializedName;
		
		public class MarkAllResult {
		    @SerializedName("count")
		    public int mCount;
		    
		    public int getCount() {
		        return mCount;
			}
		
			public void setCount(int count) {
			        this.mCount = count;
			}
		}

	Этот класс используется для хранения значения счетчика строк, возвращаемого настраиваемым API.

6. В файле **ToDoActivity.java** найдите метод **refreshItemsFromTable** и убедитесь в том, что первая строка кода в блоке `try` выглядит следующим образом:

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

	Это приводит к фильтрации элементов, так что завершенные элементы не возвращаются в результатах запроса.

7. Убедитесь, что **ToDoActivity.java** содержит следующие импорты в начале файла:

		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;

8. В файле **ToDoActivity.java** добавьте следующий метод:

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
	    	
	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}
	    		
	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();	
	    		}
	    	});
	    }
	
	Этот метод обрабатывает событие **Click** для новой кнопки. Метод **invokeApi** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки.

## Тестирование приложения

1. В меню **Запуск** щелкните элемент **Запуск приложения**, чтобы запустить проект в эмуляторе Android или на подключенном устройстве Android.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.


2. В приложении введите какой-либо текст в поле **Insert a TodoItem** (Вставка TodoItem), а затем нажмите кнопку **Добавить**.

3. Повторяйте предыдущий шаг, пока в список не будут добавлены несколько элементов списка дел.

4. Нажмите кнопку **Выполнить все**.

  	![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

	Отображается диалоговое окно сообщения, в котором указывается количество элементов, помеченных как завершенные; снова выполняется отфильтрованный запрос, что приводит к удалению из списка всех элементов.

<!---HONumber=August15_HO8-->