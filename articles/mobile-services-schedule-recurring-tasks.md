<properties urlDisplayName="Schedule Backend Tasks" pageTitle="Планирование серверных задач с помощью планировщика - мобильные службы" metaKeywords="" description="Использование планировщика мобильных служб Azure для планирования заданий для мобильного приложения." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Планирование повторяющихся заданий в мобильных службах 

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" class="current">Сервер JavaScript</a>
</div>
 
В этом разделе показано, как использовать функциональные возможности планировщика заданий на портале управления для определения кода серверного сценария, который выполняется на основе определенного расписания. В этом случае сценарий периодически проверяет удаленную службу (в данном случае Twitter) и сохраняет результаты в новой таблице. В число других периодических задач, которые могут быть запланированы, входят следующие:

+ Архивация старых или повторяющихся записей.
+ Запрос и сохранение внешних данных, например твитов, RSS-записей и сведений о расположении.
+ Обработка или изменение размеров сохраненных изображений.

В этом учебнике подробно описаны все действия, необходимые для использования планировщика заданий в целях создания запланированного задания, которое запрашивает данные твита из Twitter и сохраняет твиты в новой таблице Updates:

+ [Зарегистрируйтесь для доступа к Twitter и сохранения учетных данных]
+ [Создание новой таблицы Updates]
+ [Создание нового запланированного задания]

## <a name="get-oauth-credentials"></a>Регистрация для доступа к API Twitter v1.1 и сохранение учетных данных

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Создание новой таблицы Updates

Затем необходимо создать новую таблицу, в которой будут храниться твиты.

2. На портале управления щелкните вкладку **Данные** своей мобильной службы, затем нажмите кнопку **+Создать**.

   	![][2]

   	Откроется диалоговое окно **Создание новой таблицы**.

3. В поле **Имя таблицы** введите _Updates_ и нажмите кнопку проверки.

   	![][3]

  	Это приведет к созданию новой таблицы хранения **Updates**. 

## <a name="add-job"></a>Создание нового запланированного задания  

Теперь можно создать запланированное задание, которое получает доступ к Twitter и сохраняет данные твитов в новой таблице Updates.

2. Щелкните вкладку **Планировщик**, а затем нажмите кнопку **+Создать**. 

   	![][4]

    >[WACOM.NOTE]При работе в вашей мобильной связи на <em>бесплатном</em> уровне вы можете выполнить одновременно только одно запланированное задание. на оплачиваемых уровнях можно выполнять одновременно до десяти запланированных заданий.

3. В диалоговом окне планировщика введите _getUpdates_ в поле **Имя задания**, задайте интервал и единицы планирования, затем нажмите кнопку проверки. 
   
   	![][5]

   	Это создает новое задание с именем **getUpdates**. 

4. Щелкните новое, только что созданное задание, затем перейдите на вкладку **Сценарий**.

   	![][6] 

5. Замените функцию заполнителя **getUpdates** на следующий код:

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


   	Этот сценарий вызывает API запроса Twitter с использованием сохраненных учетных данных для запроса недавних твитов, содержащих хэш-тег #mobileservices. Повторяющиеся твиты и ответы удаляются из результатов, прежде чем происходит сохранение в таблице.

    >[WACOM.NOTE]В этом примере предполагается, что во время каждого запланированного выполнения в таблицу вставляются только несколько строк. В случае вставки большого количества строк в цикле при работе на бесплатном уровне могут закончиться подключения. В этом случае следует выполнить операции массовой вставки. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts">Практическое руководство. Выполнение массовой вставки</a>.

6. Щелкните **Запустить один раз** для проверки сценария. 

  	![][7]
       
   	Это приведет к сохранению и выполнению задания, несмотря на то, что оно отключено в планировщике.

7. Нажмите кнопку "Назад", щелкните **Данные**, щелкните таблицу **Updates**, нажмите кнопку **Обзор** и убедитесь, что данные Twitter были вставлены в таблицу.

   	![][8]

8. Нажмите кнопку "Назад", щелкните **Планировщик**, выберите **getUpdates**, затем щелкните **Включить**.

   	![][9]

   	Это приведет к включению задания для работы по определенному расписанию: в данном случае каждый час.

Поздравляем! Вы успешно создали новое запланированное задание в своей мобильной службе. Это задание выполняется в соответствии с расписанием, пока вы его не отключите или не измените.

## <a name="nextsteps"> </a>Дальнейшие действия

* [Справочник серверных скриптов мобильных служб]
  <br/>Регистрация и использование серверных скриптов.

<!-- Anchors. -->
[Зарегистрируйтесь для доступа к Twitter и сохранения учетных данных]: #get-oauth-credentials
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
[Регистрация приложений для входа в Twitter с помощью мобильных служб]: /ru-ru/develop/mobile/how-to-guides/register-for-twitter-authentication
[Разработчики Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Параметры приложения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
