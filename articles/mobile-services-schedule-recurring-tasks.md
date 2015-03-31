<properties 
	pageTitle="Планирование серверных задач с помощью планировщика - мобильные службы" 
	description="Использование планировщика мобильных служб Azure для планирования заданий для мобильного приложения." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Планирование повторяющихся заданий в мобильных службах 

> [AZURE.SELECTOR-LIST (Платформа | Серверная часть)]
- [(Любая | .NET)](/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/)
- [(Любая | JavaScript)](/documentation/articles/mobile-services-schedule-recurring-tasks/)
 
В этом разделе показано, как использовать функциональные возможности планировщика заданий на портале управления для определения кода серверного сценария, который выполняется на основе определенного расписания. В этом случае сценарий периодически проверяет удаленную службу (в данном случае Twitter) и сохраняет результаты в новой таблице. В число других периодических задач, которые могут быть запланированы, входят следующие:

+ Архивация старых или повторяющихся записей.
+ Запрос и сохранение внешних данных, например твитов, RSS-записей и сведений о расположении.
+ Обработка или изменение размеров сохраненных изображений.

В этом учебнике подробно описаны все действия, необходимые для использования планировщика заданий в целях создания запланированного задания, которое запрашивает данные твита из Twitter и сохраняет твиты в новой таблице Updates:

+ [Регистрация для доступа к Twitter и сохранения учетных данных]
+ [Создание новой таблицы Updates]
+ [Создание нового запланированного задания]

##<a name="get-oauth-credentials"></a>Регистрация для доступа к API Twitter версии 1.1 и сохранение учетных данных

[AZURE.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Создание новой таблицы Updates

Затем необходимо создать новую таблицу, в которой будут храниться твиты.

2. На портале управления щелкните вкладку **Данные** своей мобильной службы, затем нажмите кнопку **+Создать**.

3. В поле **Имя таблицы** введите _Updates_ и нажмите кнопку проверки.

##<a name="add-job"></a>Создание нового запланированного задания  

Теперь можно создать запланированное задание, которое получает доступ к Twitter и сохраняет данные твитов в новой таблице Updates.

2. Щелкните вкладку **Планировщик**, а затем нажмите кнопку **+Создать**. 

    >[AZURE.NOTE]При выполнении мобильной службы на <em>бесплатном</em> уровне вы сможете единовременно запускать только одно запланированное задание. На оплачиваемых уровнях можно выполнять одновременно до десяти запланированных заданий.

3. В диалоговом окне планировщика введите _getUpdates_ для поля **Имя задания**, задайте интервал и единицы планирования, затем нажмите кнопку проверки. 

   	В результате создается новое задание **getUpdates**. 

4. Щелкните только что созданное новое задание, выберите **Сценарий** и замените функцию заполнителя **getUpdates** следующим кодом:

		var updatesTable = tables.getTable('Updates');
		var request = require('request');
		var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

		// Get the service configuration module.
		var config = require('mobileservice-config');
		
		// Get the stored Twitter consumer key and secret. 
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
		
		function getUpdates() {   
		    // Check what is the last tweet we stored when the job last ran
		    // and ask Twitter to only give us more recent tweets
		    appendLastTweetId(
		        twitterUrl, 
		        function twitterUrlReady(url){            
		            // Create a new request with OAuth credentials.
		            request.get({
		                url: url,                
		                oauth: {
		                    consumer_key: consumerKey,
		                    consumer_secret: consumerSecret,
		                    token: accessToken,
		                    token_secret: accessTokenSecret
		                }},
		                function (error, response, body) {
		                if (!error && response.statusCode == 200) {
		                    var results = JSON.parse(body).statuses;
		                    if(results){
		                        console.log('Fetched ' + results.length + ' new results from Twitter');                       
		                        results.forEach(function (tweet){
		                            if(!filterOutTweet(tweet)){
		                                var update = {
		                                    twitterId: tweet.id,
		                                    text: tweet.text,
		                                    author: tweet.user.screen_name,
		                                    date: tweet.created_at
		                                };
		                                updatesTable.insert(update);
		                            }
		                        });
		                    }            
		                } else { 
		                    console.error('Could not contact Twitter');
		                }
		            });
		
		        });
		 }
		// Find the largest (most recent) tweet ID we have already stored
		// (if we have stored any) and ask Twitter to only return more
		// recent ones
		function appendLastTweetId(url, callback){
		    updatesTable
		    .orderByDescending('twitterId')
		    .read({success: function readUpdates(updates){
		        if(updates.length){
		            callback(url + '&since_id=' + (updates[0].twitterId + 1));           
		        } else {
		            callback(url);
		        }
		    }});
		}
		
		function filterOutTweet(tweet){
		    // Remove retweets and replies
		    return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
		}


   	Этот скрипт вызывает API запроса Twitter с использованием сохраненных учетных данных для запроса недавних твитов, содержащих хэш-тег `#mobileservices`. Повторяющиеся твиты и ответы исключаются из результатов еще до того, как они сохраняются в таблице.

    >[AZURE.NOTE]В этом примере предполагается, что во время каждого запланированного выполнения в таблицу вставляются только несколько строк. В случае вставки большого количества строк в цикле при работе на бесплатном уровне могут закончиться подключения. В этом случае следует выполнить операции массовой вставки. Дополнительную информацию см. в разделах <a href="/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts">Практическое руководство. Выполнение массовой вставки</a>.

6. Щелкните **Запустить один раз** для проверки сценария. 

   	Это приведет к сохранению и выполнению задания, несмотря на то, что оно отключено в планировщике.

7. Нажмите кнопку "Назад", щелкните **Данные**, щелкните таблицу **Updates**, щелкните **Обзор** и убедитесь, что данные Twitter были вставлены в таблицу.

8. Нажмите кнопку "Назад", щелкните **Планировщик**, выберите **getUpdates**, затем щелкните **Включить**.

   	Это приведет к включению задания для работы по определенному расписанию: в данном случае каждый час.

Поздравляем, новое запланированное задание успешно создано в вашей мобильной службе. Это задание будет выполняться как запланированное, пока не будет отключено или изменено.

## <a name="nextsteps"> </a>Дальнейшие действия

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительная информация о регистрации и использовании серверных сценариев.

<!-- Anchors. -->
[Регистрация для доступа к Twitter и сохранения учетных данных]: #get-oauth-credentials
[Создание новой таблицы Updates]: #create-table
[Создание нового запланированного задания]: #add-job
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-my-apps.png
[1]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-app-secrets.png
[2]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
[3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
[4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[5]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
[6]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
[7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
[8]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
[9]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
[10]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-app-settings.png
[11]: ./media/mobile-services-schedule-recurring-tasks/mobile-identity-tab-twitter-only.png

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[Регистрация приложений для входа в Twitter с помощью мобильных служб]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Разработчики Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Параметры приложения]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=47-->
