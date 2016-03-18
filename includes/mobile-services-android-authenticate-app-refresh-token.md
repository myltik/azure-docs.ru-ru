Кэш маркера должен работать в самом простом случае, однако что произойдет, если срок действия маркера истечет или он будет отозван? Срок действия маркера может истечь в момент, когда приложение не запущено. Это будет означать, что кэш маркера будет являться недопустимым. Срок действия маркера также может истечь во время работы приложения при непосредственном вызове из приложения или при вызове из библиотеки мобильных служб. В результате будет возвращен код состояния HTTP 401 "Не авторизован".

Необходимо иметь возможность обнаруживать просроченный токен и обновить его. Для этого воспользуемся [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) из [клиентской библиотеки Android](http://dl.windowsazure.com/androiddocs/).

В данном разделе вы определите ServiceFilter, который будет обнаруживать код состояния HTTP 401 в ответе и запускать обновление маркера и кэша маркера. Кроме того, этот ServiceFilter будет блокировать иные исходящие запросы при проверке подлинности, чтобы они использовали обновленный маркер.

1. Откройте файл ToDoActivity.java и добавьте следующую инструкцию import:
 
        import java.util.concurrent.atomic.AtomicBoolean;
		import java.util.concurrent.ExecutionException;

		import com.microsoft.windowsazure.mobileservices.MobileServiceException;
 
2. Добавьте в класс `ToDoActivity` следующие методы.

    	public boolean bAuthenticating = false;
	    public final Object mAuthenticationLock = new Object();

    Они будут использоваться для синхронизации проверки подлинности пользователя. Мы хотим сделать единственную проверку подлинности. Все вызовы в момент проверки подлинности должны ожидать и использовать новый маркер в процессе проверки подлинности.

3. В файле ToDoActivity.java добавьте следующий метод в класс ToDoActivity, который будет использоваться для блокирования исходящих запросов в других потоках при проверке подлинности.

	    /**
    	 * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
    	 */
    	public boolean detectAndWaitForAuthentication()
    	{
    		boolean detected = false;
    		synchronized(mAuthenticationLock)
    		{
    			do
    			{
    				if (bAuthenticating == true)
    					detected = true;
    				try
    				{
    					mAuthenticationLock.wait(1000);
    				}
    				catch(InterruptedException e)
    				{}
    			}
    			while(bAuthenticating == true);
    		}
    		if (bAuthenticating == true)
    			return true;
    		
    		return detected;
    	}
    	

4. В файле ToDoActivity.java добавьте следующий метод в класс ToDoActivity. Этот метод будет собственно запускать ожидание и обновлять маркер при исходящих запросах при завершении проверки подлинности.

    	
    	/**
    	 * Waits for authentication to complete then adds or updates the token 
    	 * in the X-ZUMO-AUTH request header.
    	 * 
    	 * @param request
    	 *            The request that receives the updated token.
    	 */
    	private void waitAndUpdateRequestToken(ServiceFilterRequest request)
    	{
    		MobileServiceUser user = null;
    		if (detectAndWaitForAuthentication())
    		{
    			user = mClient.getCurrentUser();
    			if (user != null)
    			{
    				request.removeHeader("X-ZUMO-AUTH");
    				request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
    			}
    		}
    	}


5. В файле ToDoActivity.java обновите метод `authenticate` класса ToDoActivity, чтобы он принимал логический параметр, позволяющий принудительно обновлять маркер и кэш маркеров. Нам также потребуется уведомлять все заблокированные потоки о завершении процесса проверки подлинности, чтобы они могли получить новый маркер.

	    /**
    	 * Authenticates with the desired login provider. Also caches the token. 
    	 * 
    	 * If a local token cache is detected, the token cache is used instead of an actual 
	     * login unless bRefresh is set to true forcing a refresh.
    	 * 
	     * @param bRefreshCache
    	 *            Indicates whether to force a token refresh. 
	     */
    	private void authenticate(boolean bRefreshCache) {
	        
		    bAuthenticating = true;
		    
		    if (bRefreshCache || !loadUserTokenCache(mClient))
	        {
	            // New login using the provider and update the token cache.
	            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
	                    new UserAuthenticationCallback() {
	                        @Override
	                        public void onCompleted(MobileServiceUser user,
	                                Exception exception, ServiceFilterResponse response) {
    
	                    		synchronized(mAuthenticationLock)
	                    		{
		                        	if (exception == null) {
		                                cacheUserToken(mClient.getCurrentUser());
                                        createTable();
		                            } else {
		                                createAndShowDialog(exception.getMessage(), "Login Error");
		                            }
		            				bAuthenticating = false;
		            				mAuthenticationLock.notifyAll();
	                    		}
	                        }
	                    });
	        }
		    else
		    {
		    	// Other threads may be blocked waiting to be notified when 
		    	// authentication is complete.
		    	synchronized(mAuthenticationLock)
		    	{
		    		bAuthenticating = false;
		    		mAuthenticationLock.notifyAll();
		    	}
                createTable();
		    }
	    }   



6. В файле ToDoActivity.java добавьте следующий код для нового класса `RefreshTokenCacheFilter` в классе ToDoActivity:

		/**
		* The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
		 * When 401 is encountered, the filter calls the authenticate method on the 
		 * UI thread. Out going requests and retries are blocked during authentication. 
		 * Once authentication is complete, the token cache is updated and 
		 * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
		 * that request.   
		 */
		private class RefreshTokenCacheFilter implements ServiceFilter {
		 
	        AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
	        
	        @Override
	        public ListenableFuture<ServiceFilterResponse> handleRequest(
	        		final ServiceFilterRequest request, 
	        		final NextServiceFilterCallback nextServiceFilterCallback
	        		)
	        {
	            // In this example, if authentication is already in progress we block the request
	            // until authentication is complete to avoid unnecessary authentications as 
	            // a result of HTTP status code 401. 
	            // If authentication was detected, add the token to the request.
	            waitAndUpdateRequestToken(request);
	 
	            // Send the request down the filter chain
	            // retrying up to 5 times on 401 response codes.
	            ListenableFuture<ServiceFilterResponse> future = null;
	            ServiceFilterResponse response = null;
	            int responseCode = 401;
	            for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
	            {
	                future = nextServiceFilterCallback.onNext(request);
	                try {
	                    response = future.get();
	                    responseCode = response.getStatus().getStatusCode();
	                } catch (InterruptedException e) {
	                   e.printStackTrace();
	                } catch (ExecutionException e) {
	                    if (e.getCause().getClass() == MobileServiceException.class)
	                    {
	                        MobileServiceException mEx = (MobileServiceException) e.getCause();
	                        responseCode = mEx.getResponse().getStatus().getStatusCode();
	                        if (responseCode == 401)
	                        {
	                            // Two simultaneous requests from independent threads could get HTTP status 401. 
	                            // Protecting against that right here so multiple authentication requests are
	                            // not setup to run on the UI thread.
	                            // We only want to authenticate once. Requests should just wait and retry 
	                            // with the new token.
	                            if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
	                            {
	                                // Authenticate on UI thread
	                                runOnUiThread(new Runnable() {
	                                    @Override
	                                    public void run() {
	                                        // Force a token refresh during authentication.
	                                        authenticate(true);
	                                    }
	                                });
	                            }
	
				                // Wait for authentication to complete then update the token in the request. 
				                waitAndUpdateRequestToken(request);
				                mAtomicAuthenticatingFlag.set(false);                                                  
	                        }
	                    }
	                }
	            }
	            return future;
	        }
		}


    Эта фильтр служб будет проверять каждый ответ на наличие кода состояния HTTP 401 "Не авторизован". При получении кода 401 будет запрошен новый вход в систему для получения нового маркера в потоке пользовательского интерфейса. Другие вызовы блокируются до завершения входа или 5 неудачных попыток. После получения нового токен запрос, который вызвал срабатывание по коду 401, будет запущен повторно с новым токеном, все заблокированные вызовы также будут перезапущены с новым токеном.

7. В файле ToDoActivity.java добавьте следующий код для нового класса `ProgressFilter` в классе ToDoActivity:
		
		/**
		* The ProgressFilter class renders a progress bar on the screen during the time the App is waiting for the response of a previous request.
		* the filter shows the progress bar on the beginning of the request, and hides it when the response arrived.
		*/
		private class ProgressFilter implements ServiceFilter {
			@Override
			public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback nextServiceFilterCallback) {

				final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();

				runOnUiThread(new Runnable() {

					@Override
					public void run() {
						if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
					}
				});

				ListenableFuture<ServiceFilterResponse> future = nextServiceFilterCallback.onNext(request);

				Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
					@Override
					public void onFailure(Throwable e) {
						resultFuture.setException(e);
					}

					@Override
					public void onSuccess(ServiceFilterResponse response) {
						runOnUiThread(new Runnable() {

							@Override
							public void run() {
								if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.GONE);
							}
						});

						resultFuture.set(response);
					}
				});

				return resultFuture;
			}
		}
		
	Этот фильтр будет отображать индикатор выполнения в начале запроса и скрывать его после получения ответа.

8. В файле ToDoActivity.java обновите метод `onCreate` следующим образом:

		@Override
	    public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    
		    setContentView(R.layout.activity_to_do);
		    mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
        
		    // Initialize the progress bar
		    mProgressBar.setVisibility(ProgressBar.GONE);
        
		    try {
		    	// Create the Mobile Service Client instance, using the provided
		    	// Mobile Service URL and key
		    	mClient = new MobileServiceClient(
		    			"https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
		    			"<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
            
		    	// Authenticate passing false to load the current token cache if available.
		    	authenticate(false);
		        	
		    } catch (MalformedURLException e) {
			    createAndShowDialog(new Exception("Error creating the Mobile Service. " +
			        "Verify the URL"), "Error");
		    }
	    }


       В этом коде `RefreshTokenCacheFilter` используется в дополнение к `ProgressFilter`. Также во время `onCreate` нужно загрузить кэш маркеров. Поэтому методу `authenticate` передается значение `false`.

<!---HONumber=Oct15_HO3-->