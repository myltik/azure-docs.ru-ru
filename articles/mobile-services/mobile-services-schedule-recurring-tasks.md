---
title: Планирование задач в серверной мобильной службе JavaScript | Microsoft Docs
description: Используйте планировщик в мобильных служб Azure для запуска заданий серверной части JavaScript по расписанию.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Планирование повторяющихся заданий в мобильных службах
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

> [!div class="op_single_selector"]
> * [Сервер .NET.](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
> * [Серверная служба JavaScript](mobile-services-schedule-recurring-tasks.md)
> 
> 

В этой статье объясняется, как использовать функциональные возможности планировщика заданий на классическом портале Azure для создания кода серверного сценария, который выполняется на основе определенного расписания. В этом случае сценарий периодически проверяет удаленную службу (в данном случае Twitter) и сохраняет результаты в новой таблице. В число других периодических задач, которые могут быть запланированы, входят следующие:

* Архивация старых или повторяющихся записей.
* Запрос и сохранение внешних данных, например твитов, RSS-записей и сведений о расположении.
* Обработка или изменение размеров сохраненных изображений.

В этом учебнике показаны все действия, необходимые для использования планировщика заданий в целях создания запланированного задания, которое запрашивает данные твита из Twitter и сохраняет твиты в новой таблице Updates:

## <a name="get-oauth-credentials"></a>Регистрация для доступа к API v1.1 Твиттера и сохранение учетных данных
[!INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Создание новой таблицы Updates
Затем необходимо создать новую таблицу, в которой будут храниться твиты.

1. На [классическом портале Azure] откройте вкладку **Данные** своей мобильной службы, затем щелкните **+Создать**.
2. В поле **Имя таблицы** введите *Updates* и нажмите кнопку проверки.

## <a name="add-job"></a>Создание запланированного задания
Теперь можно создать запланированное задание, которое получает доступ к Twitter и сохраняет данные твитов в новой таблице Updates.

1. Щелкните вкладку **Планировщик**, а затем нажмите кнопку **+Создать**.
   
   > [!NOTE]
   > При работе в вашей мобильной службе на <em>бесплатном</em> уровне вы можете выполнять одновременно только одно запланированное задание. На оплачиваемых уровнях можно выполнять одновременно до десяти запланированных заданий.
   > 
   > 
2. В диалоговом окне планировщика для параметра *Имя задания* введите значение **getUpdates**, задайте интервал и единицы планирования, а затем нажмите кнопку «Проверить».
   
       В результате создается новое задание **getUpdates**.
3. Щелкните только что созданное задание, перейдите на вкладку **Скрипт** и замените функцию заполнителя **getUpdates** следующим кодом:
   
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

       Этот скрипт вызывает API запросов Твиттера с использованием сохраненных учетных данных для запроса недавних твитов, содержащих хэш-тег `#mobileservices`. Повторяющиеся твиты и ответы исключаются из результатов еще до того, как они сохраняются в таблице.

    >[AZURE.NOTE]В этом примере предполагается, что во время каждого запланированного выполнения в таблицу вставляются только несколько строк. В случае вставки большого количества строк в цикле при работе на бесплатном уровне могут закончиться подключения. В этом случае следует выполнить операции массовой вставки. Дополнительную информацию см. в разделе [Практическое руководство. Выполнение массовой вставки](mobile-services-how-to-use-server-scripts.md#bulk-inserts).

1. Щелкните **Запустить один раз** для проверки сценария.
   
       Это приведет к сохранению и выполнению задания, несмотря на то, что оно отключено в планировщике.
2. Нажмите кнопку "Назад", щелкните **Данные**, щелкните таблицу **Updates**, щелкните **Обзор** и убедитесь, что данные Twitter были вставлены в таблицу.
3. Нажмите кнопку «Назад», щелкните **Планировщик**, выберите **getUpdates**, затем щелкните **Включить**.
   
       Это приведет к включению задания для работы по определенному расписанию: в данном случае каждый час.

Поздравляем, новое запланированное задание успешно создано в вашей мобильной службе. Это задание будет выполняться как запланированное, пока не будет отключено или изменено.

## <a name="nextsteps"> </a>См. также
* [Справочник серверных скриптов мобильных служб] <br/>Узнайте больше о регистрации и использовании серверных скриптов.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[классическом портале Azure]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0727_2016-->