<properties
	pageTitle="Планирование задач в серверной мобильной службе .NET | Microsoft Azure"
	description="Используйте планировщик в мобильных служб Azure для запуска заданий серверной части .NET по расписанию."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>

# Планирование повторяющихся заданий в мобильных службах

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript backend](mobile-services-schedule-recurring-tasks.md)

В этой статье объясняется, как использовать функциональные возможности планировщика заданий на классическом портале Azure для создания кода серверного сценария, который выполняется на основе определенного расписания. В этом случае сценарий периодически проверяет удаленную службу (в данном случае Twitter) и сохраняет результаты в новой таблице. В число других периодических задач, которые могут быть запланированы, входят следующие:

+ Архивация старых или повторяющихся записей.
+ Запрос и сохранение внешних данных, например твитов, RSS-записей и сведений о расположении.
+ Обработка или изменение размеров сохраненных изображений.

В этом учебнике подробно описаны все действия, необходимые для использования планировщика заданий в целях создания запланированного задания, которое запрашивает данные твита из Twitter и сохраняет твиты в новой таблице Updates:

>[AZURE.NOTE]В этом учебнике используется сторонняя библиотека LINQ to Twitter для упрощения доступа OAuth 2.0 к Twitter v1.1 API. Необходимо загрузить и установить пакет NuGet LINQ to Twitter для работы с этим учебником. Дополнительные сведения см. в разделе [Проект LINQ to Twitter CodePlex].

##<a name="get-oauth-credentials"></a>Регистрация для доступа к API v1.1 Твиттера и сохранение учетных данных

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

&nbsp;&nbsp;7. В обозревателе решений Visual Studio откройте файл web.config для проекта мобильной службы, найдите параметры `MS_TwitterConsumerKey` и `MS_TwitterConsumerSecret` и замените их значения на ключ клиента и секрет клиента Twitter, заданные на портале.

&nbsp;&nbsp;8. В том же разделе добавьте следующие новые параметры приложения, заменив местозаполнители на маркер доступа и секрет маркера доступа Twitter, заданные на портале.

	<add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
	<add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

Эти сохраненные параметры используются мобильной службой при запуске на локальном компьютере, и это позволяет проверить запланированное задание перед публикацией. При работе в Azure мобильная служба вместо этого использует значения параметров приложения, заданные на портале, и игнорирует эти параметры проекта.

##<a name="install-linq2twitter"></a>Загрузка и установка библиотеки LINQ to Twitter

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2. В левой области выберите категорию **Интернет**, выполните поиск `linq2twitter`, щелкните пункт **Установить**, выделив пакет **linqtotwitter**, а затем прочитайте и примите лицензионные соглашения.

  	![][1]

  	При этом библиотека Linq to Twitter добавляется в проект мобильной службы.

Затем необходимо создать новую таблицу, в которой будут храниться твиты.

##<a name="create-table"></a>Создание новой таблицы Updates

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши папку DataObjects, разверните узел **Добавить**, щелкните **Класс**, введите `Updates` в поле **Имя** и нажмите кнопку **Добавить**.

	При этом создается новый файл проекта для класса Updates.

2. Щелкните правой клавишей **Ссылки**, выберите **Добавить ссылку...**, а затем **Платформа** в разделе **Сборки**, установите флажок **System.ComponentModel.DataAnnotations** и нажмите кнопку **ОК**.

	![][7]

	Это действие добавит ссылку на новую сборку.

2. Добавьте следующие инструкции **using** в этот новый класс:

		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. Замените определение класса **Updates** следующим кодом:

		public class Updates
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Разверните папку Models, откройте файл контекста модели данных (под названием *имя\_службы*Context.cs) и добавьте следующее свойство, которое возвращает типизированный набор **DbSet**:

		public DbSet<Updates> Updates { get; set; }

	Таблица Updates, которая создается в базе данных при первом обращении к DbSet, используется службой для хранения данных твитов.

	>[AZURE.NOTE] В случае использования инициализатора базы данных по умолчанию Entity Framework будет удалять и заново создавать базу данных при каждом обнаружении изменения модели данных в определении модели Code First. Чтобы сделать это изменение модели данных и сохранить существующие данные в базе данных, необходимо использовать Code First Migrations. Нельзя использовать инициализатор по умолчанию для базы данных SQL в Azure. Дополнительные сведения см. в разделе [Как использовать Code First Migrations для обновления модели данных](mobile-services-dotnet-backend-use-code-first-migrations.md).

Теперь можно создать запланированное задание, которое получает доступ к Twitter и сохраняет данные твитов в новой таблице Updates.

##<a name="add-job"></a>Создание нового запланированного задания

1. Разверните папку ScheduledJobs и откройте файл проекта SampleJob.cs.

	Этот класс, который наследуется от **ScheduledJob**, представляет собой задание, которое можно запланировать на классическом портале Azure для запуска по фиксированному расписанию или по запросу.

2. Замените содержимое файла SampleJob.cs на код, приведенный ниже:

		using System;
		using System.Linq;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web.Http;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
		using LinqToTwitter;
		using todolistService.Models;
		using todolistService.DataObjects;

		namespace todolistService
		{
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;

		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
					CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);

		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }

		        public async override Task ExecuteAsync()
		        {
		            // Try to get the stored Twitter access token from app settings.
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }

		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store =
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };

		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;

		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);

		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }

		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();

		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");

		            // Store new tweets in the Updates table.
		            foreach (Status tweet in filteredTweets)
		            {
		                Updates newTweet =
		                    new Updates
		                    {
		                        TweetId = Convert.ToInt64(tweet.StatusID),
		                        Text = tweet.Text,
		                        Author = tweet.User.Name,
		                        Date = tweet.CreatedAt
		                    };

		                context.Updates.Add(newTweet);
		            }

		            await context.SaveChangesAsync();
		        }
		        protected override void Dispose(bool disposing)
		        {
		            base.Dispose(disposing);
		            if (disposing)
		            {
		                context.Dispose();
		            }
		        }
		    }
		}

	В приведенном выше коде необходимо заменить строки _todolistService_ и _todolistContext_ на пространство имен и DbContext скачанного проекта, т. е. *mobile&#95;service&#95;name*Service и *mobile&#95;service&#95;name*Context соответственно.

	В приведенном выше коде переопределяющий метод **ExecuteAsync** вызывает API запросов Твиттера с помощью сохраненных учетных данных для запроса последних твитов, содержащих хэш-тег `#mobileservices`. Повторяющиеся твиты и ответы исключаются из результатов еще до того, как они сохраняются в таблице.

##<a name="run-job-locally"></a>Локальное тестирование запланированного задания

Запланированные задания можно протестировать локально перед их публикацией в Azure и регистрацией на портале.

1. В Visual Studio, где проект мобильной службы задан как запускаемый проект, нажмите клавишу F5.

	При этом запустится проект мобильной службы и появится новое окно браузера со страницей приветствия.

2. Щелкните **Попробовать**, а затем **POST jobs/{jobName}**.

	![][8]

4. Щелкните **Попробовать**, введите `Sample` в качестве значения параметра **{jobName}**, а затем щелкните **Отправить**.

	![][9]

	Так будет отправлен новый запрос POST в конечную точку задания Sample. В локальной службе запускается метод **ExecuteAsync**. Вы можете задать в этом методе точку прерывания для отладки кода.

3. В обозревателе серверов разверните пункты **Подключения к данным**, **MSTableConnectionString** и **таблицы**. Щелкните правой кнопкой мыши **Updates** и выберите пункт **Показать данные таблицы**.

	Новые твиты вводятся как строки в таблице данных.

##<a name="register-job"></a>Публикация службы и регистрация нового задания

Задание должно быть зарегистрировано на вкладке **Планировщик**, чтобы мобильные службы могли выполнять его по заданному расписанию.

3. Повторно опубликуйте проект мобильной службы в Azure.

4. На [классическом портале Azure] щелкните элемент «Мобильные службы», а затем выберите свое приложение.

2. Щелкните вкладку **Планировщик**, а затем нажмите кнопку **+Создать**.

    >[AZURE.NOTE]При работе в вашей мобильной службе на <em>бесплатном</em> уровне вы можете выполнять одновременно только одно запланированное задание. На оплачиваемых уровнях можно выполнять одновременно до десяти запланированных заданий.

3. В диалоговом окне планировщика введите _Sample_ в поле **Имя задания**, задайте интервал и единицы планирования, а затем нажмите кнопку проверки.

   	![][4]

   	В результате создается новое задание **Sample**.

4. Щелкните новое, только что созданное задание, затем щелкните **Запустить один раз**, чтобы протестировать сценарий.

   	Это приведет к выполнению задания, несмотря на то, что оно отключено в планировщике. На этой странице можно настроить задание и изменить расписание в любое время.

	>[AZURE.NOTE]Запрос POST по-прежнему можно использовать для запуска запланированного задания. Однако по умолчанию при авторизации применяется пользователь, т. е. запрос должен содержать ключ приложения в заголовке.

4. Необязательно: на [классическом портале Azure] щелкните «Управление» для базы данных, связанной с вашей мобильной службой.

    ![][6]

5. На классическом портале Azure выполните запрос для просмотра изменений, внесенных приложением. Ваш запрос будет похож на показанный ниже, но вместо `todolist` укажите имя вашей мобильной службы в качестве имени схемы.

        SELECT * FROM [todolist].[Updates]

Поздравляем, новое запланированное задание успешно создано в вашей мобильной службе. Это задание будет выполняться как запланированное, пока не будет отключено или изменено.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[классическом портале Azure]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[Проект LINQ to Twitter CodePlex]: http://linqtotwitter.codeplex.com/

<!---HONumber=AcomDC_0211_2016-->