
1. В **обозревателе проектов** в Android Studio откройте файл ToDoActivity.java и добавьте следующие инструкции import.

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


	При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с использованием имени входа в Google. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    > [AZURE.NOTE] Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод **login** выше, на одно из следующих: _MicrosoftAccount_, _Facebook_, _Twitter_ или _windowsazureactivedirectory_.

3. Добавьте в метод **onCreate** следующую строку после кода, который формирует экземпляр объекта  `MobileServiceClient`.

		authenticate();

	Этот вызов запускает процесс проверки подлинности.

4. Переместите оставшийся код после authenticate(); в методе **onCreate** в новый метод **createTable**, который выглядит следующим образом:

		private void createTable() {
	
			// Получить экземпляр таблицы мобильной службы для использования
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Создать адаптер для связи элементов с представлением
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Загрузить элементы из мобильной службы
			refreshItemsFromTable();
		}

9. В меню **Запуск** щелкните **Запуск приложения**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

<!--HONumber=49-->