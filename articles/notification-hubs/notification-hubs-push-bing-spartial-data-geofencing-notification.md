<properties
	pageTitle="Отправка push-уведомлений с определением геозон с помощью Центров уведомлений Azure и Bing Spatial Data | Microsoft Azure"
	description="В этом руководстве вы узнаете, как отправлять push-уведомления с учетом географического расположения с помощью Центров уведомлений Azure и Bing Spatial Data."
	services="notification-hubs"
	documentationCenter="windows"
    keywords="push-уведомление, push-уведомление"
	authors="dend"
	manager="yuaxu"
	editor="dend"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/31/2016"
	ms.author="dendeli"/>
    
# Отправка push-уведомлений с определением геозон с помощью Центров уведомлений Azure и Bing Spatial Data
 
 > [AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

В этом руководстве вы узнаете, как отправлять push-уведомления с учетом географического расположения с помощью Центров уведомлений Azure и Bing Spatial Data, используемых из приложения универсальной платформы Windows (UWP).

##Предварительные требования
Прежде всего нужно убедиться в том, что у вас имеются все необходимые компоненты, а также в системе установлены все необходимые программные компоненты и службы:

* [Visual Studio 2015 с обновлением 1](https://www.visualstudio.com/ru-RU/downloads/download-visual-studio-vs.aspx) или более поздней версии (также подойдет [Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)); 
* последняя версия [пакета SDK для Azure ](https://azure.microsoft.com/downloads/); 
* [учетная запись Центра разработки Карт Bing](https://www.bingmapsportal.com/) (ее можно создать бесплатно и связать с учетной записью Майкрософт). 

##Приступая к работе

Для начала давайте создадим проект. В Visual Studio создайте новый проект типа **Пустое приложение (универсальное приложение Windows)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

После создания проекта вы получите окружение для приложения. Теперь давайте все настроим для инфраструктуры определения геозон. Так как для этого мы используем службы Bing, нам понадобится общедоступная конечная точка REST API, позволяющая запросить определенные области местоположения:

    http://spatial.virtualearth.net/REST/v1/data/
    
Для этого необходимо указать следующие параметры:

* **Идентификатор источника данных** и **имя источника данных**. В API Карт Bing источники данных содержат различные сегментированные метаданные, например о местоположении и часах работы. Дополнительные сведения об этих метаданных можно прочесть здесь. 
* **Имя сущности**. Это сущность, которую нужно использовать в качестве контрольной точки для уведомления. 
* **Ключ API Карт Bing**. Это ключ, полученный ранее при создании учетной записи Центра разработки Bing.
 
Давайте подробнее рассмотрим, как настроить каждый из этих элементов.

##Настройка источника данных

Это можно сделать в Центре разработки Карт Bing. Просто щелкните **Data sources** (Источники данных) на верхней панели навигации и выберите пункт **Manage Data Sources** (Управление источниками данных).

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Если вы не работали с API Карт Bing ранее, скорее всего, источников данных не будет. Поэтому можно просто создать источник данных, щелкнув Upload data to a data source (Передать данные в источник данных). Необходимо заполнить все обязательные поля:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Вам, наверное, интересно узнать, что представляет собой файл данных и что нужно отправлять? В рамках этого теста мы просто используем пример файла на основе канала, в котором выделена область Сан-Франциско у порта:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Фрагмент кода выше представляет этот объект:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Просто скопируйте строку выше, вставьте ее в новый файл, сохраните его как **NotificationHubsGeofence.pipe** и отправьте его в Центр разработки Bing.

>[AZURE.NOTE]Система может предложить указать **главный ключ**, отличный от **ключа запроса**. Просто создайте ключ на панели мониторинга и обновите страницу отправки источника данных.

После отправки файла данных необходимо опубликовать источник данных.

Перейдите на страницу **Manage Data Sources** (Управление источниками данных) в точности так, как это делалось выше, найдите свой источник данных в списке и в столбце **Actions** (Действия) щелкните **Publish** (Опубликовать). Чуть позже на вкладке **Published Data Sources** (Опубликованные источники данных) отобразится ваш источник данных:

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Если щелкнуть **Edit** (Изменить), можно быстро узнать, какие области местоположения представлены в источнике данных:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

На этом этапе на портале не отображаются границы созданной геозоны. Нужно просто подтвердить, что область указанного местоположения выбрана правильно.

Теперь выполнены все требования для источника данных. Чтобы получить дополнительные сведения об URL-адресе запроса для вызова API, в Центре разработки Карт Bing щелкните **Data sources** (Источники данных) и выберите пункт **Data Source Information** (Сведения об источнике данных).

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

Нам нужно значение в строке **Query URL** (URL-адрес запроса). Это конечная точка, для которой можно выполнять запросы, чтобы проверить, находится ли устройство в пределах местоположения. Для этого нам просто нужно выполнить вызов GET для URL-адреса запроса и добавить следующие параметры:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Таким образом указывается целевая точка, получаемая с устройства, а служба Карты Bing автоматически определит, находится ли эта точка в пределах геозоны. После выполнения запроса с помощью браузера (или cURL) отобразится стандартный ответ JSON:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Такой ответ получается, только если точка находится в установленных пределах. Если она находится за пределами, сегмент **results** будет пустым:

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##Настройка приложения UWP

Теперь после подготовки источника данных можно начать работу над приложением UWP, для которого ранее выполнена начальная загрузка.

Прежде всего для приложения необходимо включить службы обнаружения местоположения. Для этого дважды щелкните файл `Package.appxmanifest` в **обозревателе решений**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

На открывшейся вкладке свойств пакета щелкните **Возможности** и установите флажок **Местоположение**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Объявив возможности местоположения, в решении создайте папку `Core` и добавьте в нее новый файл `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

Сейчас класс `LocationHelper` выполняет всего одну функцию — позволяет получить сведения о местоположении пользователя посредством API системы:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Дополнительные сведения о получении сведений о местоположении пользователя в приложении UWP см. в официальном [документе MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).

Чтобы проверить, работает ли получение сведений о местоположении, откройте код главной страницы (`MainPage.xaml.cs`). В конструкторе `MainPage` создайте обработчик событий для события `Loaded`:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

Реализация обработчика событий выглядит следующим образом:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Обратите внимание, что обработчик объявлен как несинхронизируемый, так как операция `GetCurrentLocation` допускает ожидание и поэтому ее нужно выполнить без синхронизации. Кроме того, так как при определенных обстоятельствах мы можем не получить местоположение вообще (например, если службы определения местоположения отключены или приложению отказано в доступе к местоположению), следует обеспечить выполнение функции соответствующим образом с проверкой отсутствующих местоположений.

Запустите приложение. Обязательно разрешите доступ к местоположению:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

После запуска приложения координаты будут отображаться в окне **Вывод**:

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Проверив получение сведений о местоположении, можно удалить тестовый обработчик событий из загруженных, так как он нам больше не пригодится.

Дальше мы зафиксируем изменения местоположения. Для этого вернитесь к классу `LocationHelper` и добавьте обработчик событий для `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

После выполнения координаты местоположения отобразятся в окне **Вывод**:

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##Настройка сервера

Скачайте [пример сервера .NET](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers) с сайта GitHub. После этого откройте папку `NotifyUsers`, а затем файл `NotifyUsers.sln`.

Настройте проект `AppBackend` в качестве **запускаемого проекта** и запустите его.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Проект уже настроен для отправки push-уведомлений на целевое устройство. Поэтому нам нужно выполнить всего два действия — выгрузить соответствующую строку подключения для центра уведомлений и добавить определение границ, чтобы уведомление отправлялось, только если пользователь находится в пределах геозоны.

Чтобы настроить строку подключения, в папке `Models` откройте файл `Notifications.cs`. Функция `NotificationHubClient.CreateClientFromConnectionString` должна содержать сведения о центре уведомлений, которые можно получить на [портале Azure](https://portal.azure.com) (на странице **Параметры** в колонке **Политики доступа**). Сохраните обновленный файл конфигурации.

Теперь нужно создать модель для результатов API Карт Bing. Самый простой способ — щелкнуть правой кнопкой мыши папку `Models` и последовательно выбрать элементы **Добавить** > **Класс**. Назовите его `GeofenceBoundary.cs`. После этого скопируйте JSON из ответа API, который рассматривался в первом разделе, и в Visual Studio последовательно выберите элементы **Изменить** > **Специальная вставка** > **Вставить JSON как классы**.

Таким образом мы обеспечим десериализацию объекта, как и предполагалось. Итоговый набор классов должен выглядеть следующим образом:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Затем откройте `Controllers` > `NotificationsController.cs`. Необходимо настроить вызов Post, чтобы учитывалась широта и долгота целевой точки. Для этого просто добавьте две строки в сигнатуру функции — `latitude` и `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Создайте в проекте класс с именем `ApiHelper.cs`. Он будет использоваться для подключения к Bing с целью проверки пересечения границ с точкой. Выполните функцию `IsPointWithinBounds` следующим образом:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Обязательно замените конечную точку API на URL-адрес запроса, полученный ранее из Центра разработки Bing (то же необходимо сделать и для ключа API).

Если получены результаты по запросу, это значит, что указанная точка находится в пределах геозоны, поэтому возвращается значение `true`. Если результатов нет, Bing сообщает, что точка находится за пределами области поиска, поэтому возвращается значение `false`.

Вернитесь к файлу `NotificationsController.cs` и добавьте параметр проверки перед оператором switch:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Таким образом уведомление будет отправляться, только если точка находится в пределах области.

##Тестирование push-уведомлений в приложении UWP

Теперь мы сможем протестировать уведомления в приложении UWP. Создайте функцию `SendLocationToBackend` в классе `LocationHelper`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Переключите `POST_URL` в расположение развернутого веб-приложения, созданного при работе с предыдущим разделом. Сейчас его можно запустить локально. Однако если планируется развернуть общедоступную версию, его необходимо разместить у внешнего поставщика.

Теперь давайте зарегистрируем приложение UWP для push-уведомлений. В Visual Studio щелкните **Проект** > **Магазин** > **Связать приложение с Магазином**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

После входа в учетную запись разработчика выберите имеющееся приложение или создайте его и свяжите с ним пакет.

Перейдите в Центр разработки и откройте созданное приложение. Щелкните **Службы** > **Push-уведомления** > **Сайт служб Live**.

![](./media/notification-hubs-geofence/ms-live-services.png)

Запишите значения параметров **Секрет приложения** и **ИД безопасности пакета**, отображающиеся на сайте. Они потребуются вам на портале Azure. Откройте центр уведомлений, щелкните **Параметры** > **Службы Notification Services** > **Windows (WNS)** и введите сведения в соответствующие поля.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Щелкните **Save** (Сохранить).

В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Управление пакетами NuGet**. Нам потребуется добавить ссылку в **управляемую библиотеку служебной шины Microsoft Azure**. Для этого просто найдите `WindowsAzure.Messaging.Managed` и добавьте ее в проект.

![](./media/notification-hubs-geofence/vs-nuget.png)

В рамках тестирования можно еще раз создать обработчик событий `MainPage_Loaded` и добавить в него следующий фрагмент кода:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Он регистрирует приложение в центре уведомлений. Теперь все готово к работе.

В `LocationHelper` в обработчике `Geolocator_PositionChanged` можно добавить фрагмент тестового кода, который принудительно разместит местоположение в пределах геозоны:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Так как мы не передаем настоящие координаты (которые в данный момент могут выходить за пределы геозоны) и используем предопределенные тестовые значения, появится уведомление об обновлении:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##Дальнейшие действия

Чтобы убедиться, что решение готово для работы, нужно выполнить еще несколько действий.

Во-первых, необходимо убедиться в динамичности геозон. Для этого потребуется выполнить дополнительные операции с API Bing, чтобы передавать новые границы в существующий источник данных. Дополнительные сведения по этой теме см. в [документации по API служб Bing Spatial Data Services](https://msdn.microsoft.com/library/ff701734.aspx).

Во-вторых, чтобы обеспечить доставку данных соответствующим участникам, можно отслеживать их путем [добавления тегов](notification-hubs-tags-segment-push-message.md).

В представленном выше решении описывается сценарий, предполагающий использование множества целевых платформ, поэтому определение геозон не ограничено возможностями конкретной системы. Тем не менее универсальная платформа Windows предоставляет [готовые возможности для определения геозон](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Дополнительные сведения о возможностях центров уведомлений см. на [портале документации](https://azure.microsoft.com/documentation/services/notification-hubs/).

<!---HONumber=AcomDC_0622_2016-->