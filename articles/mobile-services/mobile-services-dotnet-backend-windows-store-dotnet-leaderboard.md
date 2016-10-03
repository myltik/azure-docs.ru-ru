<properties
	pageTitle="Создание приложения списка лидеров для Магазина Windows с помощью внутреннего сервера .NET | Мобильные службы Azure"
	description="Узнайте, как создать приложение списка лидеров для Магазина Windows с помощью мобильных служб Azure с внутренним сервером .NET."
	documentationCenter="windows"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="glenga"/>

# Создание приложения списка лидеров с помощью внутреннего сервера .NET мобильных служб Azure

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


В этом учебнике показано, как создать приложение Магазина Windows с помощью мобильных служб Azure с внутренним сервером .NET. Мобильные службы Azure предоставляют масштабируемый и безопасный внутренний сервер со встроенной проверкой подлинности, мониторингом, push-уведомлениями и другими функциями, включая кроссплатформенную клиентскую библиотеку для создания мобильных приложений. Внутренний сервер .NET для мобильных служб основан на [веб-интерфейсе API ASP.NET](http://asp.net/web-api) и предоставляет разработчикам .NET лучший способ создания API REST.

## Обзор

Веб-интерфейс API — это платформа с открытым исходным кодом, которая предоставляет разработчикам .NET лучший способ создания API REST. Вы можете разместить решение веб-интерфейса API на веб-сайтах Azure, в мобильных службах Azure, использующих внутренний сервер .NET, или даже автономно в пользовательском процессе. Мобильные службы — это среда размещения, разработанная специально для мобильных приложений. При размещении службы веб-интерфейсов API в мобильных службах кроме хранилища данных вы получаете следующие преимущества.

- Встроенная проверка подлинности с помощью поставщиков социальных сетей и Azure Active Directory (AAD).
- Push-уведомления в приложениях, использующих службы уведомлений устройства.
- Полный набор клиентских библиотек, который упрощает доступ к службе из любого приложения.
- Встроенный вход и диагностика.

Изучив данный учебник, вы научитесь:

- создавать API REST, используя мобильные службы Azure;
- публиковать службы в Azure;
- создавать приложения Магазина Windows, использующие службу;
- использовать Entity Framework (EF) для создания отношений между внешними ключами и объектами передачи данных (DTO);
- использовать веб-интерфейсы API ASP.NET для определения пользовательского API.

В этом учебнике используется [последнее обновление Visual Studio 2013](http://go.microsoft.com/fwlink/p/?LinkID=390465).


## Сведения о примере приложения

*Список лидеров* отображает список игроков с их баллами и рейтингом. Список лидеров может быть частью крупной игры или отдельным приложением. Список лидеров — это реальное приложение, но оно достаточно простое для использования в учебнике. Вот снимок экрана приложения:

![][1]

Для простоты приложение не связано с реальной игрой. Вместо этого вы можете добавлять игроков и отправлять баллы для каждого из них. При отправке баллов мобильная служба подсчитывает новые рейтинги. На внутреннем сервере мобильная служба создает базу данных с двумя таблицами:

- Player. Содержит идентификатор игрока и имя.
- PlayerRank. Содержит баллы и рейтинг игрока.

PlayerRank содержит внешний ключ к Player. У каждого игрока может быть один параметр PlayerRank или ни одного.

В приложении списка лидеров значение PlayerRank может также содержать идентификатор игры, чтобы игрок мог отправлять баллы для нескольких игр.

![][2]

Клиентское приложение может выполнять все операции CRUD применительно к игрокам. Оно может читать или удалять существующие сущности PlayerRank, но не может создавать или обновлять их напрямую. Это связано с тем, что значение рейтинга подсчитывает служба. Вместо этого клиент отправляет баллы, а служба обновляет рейтинги для всех игроков.

Скачайте весь проект [здесь](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).


## Создание проекта

Запустите Visual Studio и создайте новый проект веб-приложения ASP.NET. Назовите проект "Список лидеров".

![][3]

В Visual Studio 2013 в проект веб-приложения ASP.NET будет включен шаблон для мобильной службы Azure. Выберите этот шаблон и нажмите кнопку **ОК**.

![][4]

В шаблон проекта входит пример контроллера и объект данных.

![][5]

Они не нужны для учебника, поэтому вы можете удалить их из проекта. Также удалите ссылки на приложение TodoItem в файлах WebApiConfig.cs и LeaderboardContext.cs.

## Добавление моделей данных

Для определения таблиц баз данных будет использоваться [EF Code First](http://msdn.microsoft.com/data/ee712907#codefirst). В папке DataObjects добавьте класс с именем `Player`.

	using Microsoft.WindowsAzure.Mobile.Service;

	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

Добавьте еще один класс с именем `PlayerRank`.

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }

	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

Обратите внимание, что оба класса наследуются из класса **EntityData**. Наследование из класса **EntityData** упрощает использование данных приложением с помощью кроссплатформенной клиентской библиотеки для мобильных служб Azure. **EntityData** также упрощает [обработку конфликтов записи базы данных](mobile-services-windows-store-dotnet-handle-database-conflicts.md) для приложения.

Класс `PlayerRank` содержит [свойство навигации](http://msdn.microsoft.com/data/jj713564.aspx), которое указывает на связанную сущность `Player`. Атрибут **[ForeignKey]** сообщает EF, что свойство `Player` представляет внешний ключ.

## Добавление контроллеров веб-API

Далее будет показано, как добавить контроллеры веб-интерфейса API для `Player` и `PlayerRank`. Вместо простых контроллеров веб-интерфейса API будет добавлен контроллер с именем *контроллер таблиц*, разработанный специально для мобильных служб Azure.

Щелкните правой кнопкой мыши папку "Контроллеры", нажмите кнопку **Добавить** и выберите **Создать элемент скаффолдинга**.

![][6]

В диалоговом окне **Добавление элемента скаффолдинга**, разверните вкладку **Общие** с левой стороны и выберите **Мобильные службы Azure**. Затем выберите **Контроллер таблиц мобильных служб Azure**. Щелкните **Добавить**.

![][7]

В диалоговом окне **Добавление контроллера**:

1.	В поле **Класс модели** выберите Player.
2.	В поле **Класс контекста данных** выберите MobileServiceContext.
3.	Назовите контроллер PlayerContoller.
4.	Щелкните **Добавить**.


В этом шаге в проект добавляется файл с именем PlayerController.cs.

![][8]

Контроллер является производным от **TableController<T>**. Этот класс наследует **ApiController**, но он специализирован для мобильных служб Azure.

- Маршрутизация. Маршрут по умолчанию для **TableController** — `/tables/{table_name}/{id}`, где *table\_name* совпадает с именем сущности. Поэтому маршрут для контроллера Player — */tables/player/{id}*. Это соглашение о маршрутизации выполняет согласование **TableController** с [API REST](http://msdn.microsoft.com/library/azure/jj710104.aspx) мобильных служб.
- Доступ к данным. Для операций баз данных класс **TableController** использует интерфейс **IDomainManager**, который определяет абстракцию для доступа к данным. Для формирования шаблонов используется параметр **EntityDomainManager**, который является конкретной реализацией интерфейса **IDomainManager**, заключающего в оболочку контекст EF.

Теперь добавьте второй контроллер для сущностей PlayerRank. Выполните те же действия, но выберите класс модели PlayerRank. Используйте тот же класс контекста данных, а не создавайте новый. Назовите контроллер PlayerContoller.

## Использование DTO для получения связанных сущностей

Вспомните, что `PlayerRank` содержит связанную сущность `Player`.

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

Клиентская библиотека мобильной службы не поддерживает свойства навигации, и они не будут сериализованы. Вот пример необработанного ответа HTTP для GET `/tables/PlayerRank`:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT

	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

Обратите внимание, что `Player` не включается в граф объекта. Чтобы включить игрока, можно выполнить сведение графа объекта, определив *объект передачи данных* (DTO).

DTO — это объект, который определяет способ отправки данных по сети. DTO полезны, если нужно, чтобы формат подключения отличался от модели базы данных. Чтобы создать DTO для `PlayerRank`, добавьте новый класс с именем `PlayerRankDto` в папку DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

В классе `PlayerRankController` метод **Select** LINQ будет использоваться для преобразования экземпляров `PlayerRank` в экземпляры `PlayerRankDto`. Обновите методы контроллера `GetAllPlayerRank` и `GetPlayerRank` следующим образом:

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}

	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });

	    return SingleResult<PlayerRankDto>.Create(result);
	}

С этими изменениями два метода GET вернут объекты `PlayerRankDto` в клиент. Свойство `PlayerRankDto.PlayerName` задается для имени игрока. Вот пример ответа после внесения этого изменения:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT

	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

Обратите внимание, что полезная нагрузка JSON теперь включает имена игроков.

Вместо операторов Select LINQ можно использовать AutoMapper. Этот параметр возвращает дополнительный код настройки, но включает автоматическое сопоставление из сущностей домена в DTO. Дополнительные сведения см. в разделе [Сопоставление между типами базы данных и типами клиентов на внутреннем сервере .NET с помощью AutoMapper](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx).

## Определение пользовательского API для отправки оценок

Сущность `PlayerRank` включает свойство `Rank`. Это значение рассчитывает сервер, и не нужно, чтобы клиенты задавали его. Вместо этого клиенты будут использовать пользовательский API для отправки баллов игрока. При получении сервером нового количества баллов он обновит все рейтинги игрока.

Сначала добавьте в папку DataObjects класс с именем `PlayerScore`.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

В классе `PlayerRankController` переместите переменную `MobileServiceContext` из конструктора в переменную класса:

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


Удалите следующие методы из `PlayerRankController`:

- `PatchPlayerRank`
- `PostPlayerRank`
- `DeletePlayerRank`

Затем добавьте следующий код в `PlayerRankController`:

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

Метод `PostPlayerScore` принимает экземпляр `PlayerScore` в качестве входных данных. (Клиент отправит `PlayerScore` в HTTP-запросе POST.) Метод делает следующее:

1.	Добавляет новый `PlayerRank` для игрока, если его еще нет в базе данных.
2.	Обновляет баллы игрока.
3.	запускает SQL-запрос, который массово обновляет все рейтинги игрока.

Атрибут **[Route]** определяет пользовательский маршрут для этого метода.

	[Route("api/score")]

Можно также поместить метод в отдельный контроллер. В любом случае нет определенного преимущества, все зависит от способа организации кода. Дополнительные сведения об атрибуте **[Route]** см. в статье [Маршрутизация атрибутов в веб-API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2).

## Создание приложения Магазина Windows

В этом разделе описывается приложение Магазина Windows, использующее мобильную службу. Однако здесь не будут подробно рассматриваться XAML или пользовательский интерфейс. Вместо этого будут приведены сведения о логике приложения. Скачать весь проект можно [здесь](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).

Добавьте новый проект приложения Магазина Windows в решение. Здесь используется шаблон "Пустое приложение (Windows)".

![][10]

С помощью диспетчера пакетов NuGet добавьте клиентскую библиотеку мобильных служб. В Visual Studio в меню **Сервис** выберите **Диспетчер пакетов NuGet**. Затем щелкните **Консоль диспетчера пакетов**. В окне "Консоль диспетчера пакетов" введите следующую команду.

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

Переключатель -Project указывает, в какой проект следует установить пакет.

## Добавление классов модели

Создайте папку с именем "Модели" и добавьте следующие классы:

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }

	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }

	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Эти классы соответствуют непосредственным сущностям данных в мобильной службе.

## Создание модели представления

Model-View-ViewModel (MVVM) является вариантом Model-View-Controller (MVC). Шаблон MVVM позволяет отделить логику приложения от презентации.

- Модель представляет данные домена (игрок, рейтинг игрока и баллы игрока).
- Модель представления является абстрактным представлением.
- Представление отображает модель представления и отправляет входные данные пользователя в модель представления. Представление для приложения Магазина Windows определено в XAML.

![][11]

Добавьте класс с именем `LeaderboardViewModel`.

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;

	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;

	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

Реализуйте **INotifyPropertyChanged** в модели представления, чтобы модель представления могла участвовать в привязке данных.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }
    }

Далее добавьте наблюдаемые свойства. XAML привяжет данные к этим свойствам.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

Свойство `IsPending` имеет значение "Истина", когда асинхронная операция находится в режиме ожидания в службе. Свойство `ErrorMessage` сохраняет все сообщения об ошибках из службы.

Наконец, добавьте методы, которые вызывают слой службы.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            };

            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }
    }

## Добавление экземпляра MobileServiceClient

Откройте файл *App.xaml.cs* и добавьте экземпляр **MobileServiceClient** в класс `App`.

	// New code:
	using Microsoft.WindowsAzure.MobileServices;

	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);


	        // ...
	    }
	}

После локальной отладки мобильная служба запускается в IIS Express. Visual Studio назначает случайный номер порта, поэтому локальный URL-адрес указывается как http://localhost:*port*, где *port* — номер порта. Чтобы получить номер порта, запустите службу в Visual Studio, нажав клавишу F5 для отладки. Visual Studio запустит браузер и перейдет по URL-адресу службы. Локальный URL-адрес можно также найти в свойствах проекта в разделе **Веб-служба**.

## Создание главной страницы

На главной странице добавьте экземпляр модели представления. Затем задайте модель представления как **DataContext** для страницы.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


Как упоминалось ранее, здесь не будет показан весь XAML для приложения. Одним из преимуществ шаблона MVVM является возможность отделить представление от логики приложения, чтобы можно было легко изменить пользовательский интерфейс, если вам не нравится пример приложения.

Список игроков отображается в поле **ListBox**:

	<ListBox Width="200" Height="400" x:Name="PlayerListBox"
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

Рейтинги отображаются в поле **ListView**:

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

Все привязки данных осуществляются через модель представлений.

## Публикация мобильной службы

В этом шаге предстоит опубликовать мобильную службу в Microsoft Azure и изменить приложение для использования веб-службы.

В обозревателе решений щелкните правой кнопкой мыши проект "Список лидеров" и нажмите **Опубликовать**.

![][12]

В диалоговом окне **Публикация** щелкните **Мобильные службы Azure**.

![][13]

Если вы еще не вошли в учетную запись Azure, щелкните кнопку **Войти**.

![][14]


Выберите существующую мобильную службу или нажмите кнопку **Создать**, чтобы создать новую. Затем нажмите кнопку **ОК** для публикации.

![][15]

В процессе публикации будет автоматически создана база данных. Вам не нужно настраивать строку подключения.

Теперь вы можете подключить приложение списка лидеров к веб-службе. Потребуется следующее.

- URL-адрес службы
- Ключ приложения

И то и другое можно получить на классическом портале Azure. На портале щелкните **Мобильные службы**, а затем выберите мобильную службу. URL-адрес службы указан на вкладке "Панель мониторинга". Чтобы получить ключ приложения, щелкните **Управление ключами**.

![][16]

В диалоговом окне **Управление ключами доступа** скопируйте значение для ключа приложения.

![][17]


Передайте URL-адрес службы и ключ приложения в конструктор **MobileServiceClient**.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

После запуска приложение подключается к реальной службе.

## Дальнейшие действия

* [Дополнительные сведения о мобильных службах Azure]
* [Дополнительные сведения о веб-API]
* [Обработка конфликтов записей базы данных]
* [Добавление push-уведомлений]; например, при добавлении нового игрока или обновлении баллов.
* [Приступая к работе с проверкой подлинности]

<!-- Anchors. -->
[Overview]: #overview
[About the sample app]: #about-the-sample-app
[Create the project]: #create-the-project
[Add data models]: #add-data-models
[Add Web API controllers]: #add-web-api-controllers
[Use a DTO to return related entities]: #use-a-dto-to-return-related-entities
[Define a custom API to submit scores]: #define-a-custom-api-to-submit-scores
[Create the Windows Store app]: #create-the-windows-store-app
[Add model classes]: #add-model-classes
[Create a view model]: #create-a-view-model
[Add a MobileServiceClient instance]: #add-a-mobileserviceclient-instance
[Create the main page]: #create-the-main-page
[Publish your mobile service]: #publish-your-mobile-service
[Next Steps]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[Дополнительные сведения о мобильных службах Azure]: /develop/mobile/resources/
[Дополнительные сведения о веб-API]: http://asp.net/web-api
[Обработка конфликтов записей базы данных]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[Добавление push-уведомлений]: ../notification-hubs-windows-store-dotnet-get-started.md
[Приступая к работе с проверкой подлинности]: /develop/mobile/tutorials/get-started-with-users-dotnet

<!---HONumber=AcomDC_0921_2016-->