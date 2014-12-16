
1. В обозревателе пакетов в Eclipse откройте файл ToDoActivity.java и добавьте следующие инструкции импорта.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Добавьте в класс **TodoActivity** следующий метод: 
	
	private void authenticate() {
	    // Login using the Google provider.
	    
		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}   		
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();	
    		}
    	});   	
	}


	При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с использованием имени входа в Google. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Не получив положительные результаты проверки подлинности, нельзя продолжить работу.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик идентификации, отличный от Google, измените значение, передаваемое выше методу <strong>login</strong>, на одно из следующих: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> или <em>windowsazureactivedirectory</em>.</p>
    </div>

3. В методе **OnCreate** добавьте следующую строку после кода, который создает объект MobileServiceClient.

		authenticate();

	Этот вызов запускает процесс проверки подлинности.

4. Переместите оставшийся код после `authenticate();` в методе **onCreate** в новый метод **createTable**, который выглядит следующим образом:

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.
