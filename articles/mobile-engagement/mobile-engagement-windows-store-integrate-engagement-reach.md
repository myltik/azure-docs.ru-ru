---
title: Интеграция пакета SDK Reach для универсальных приложений для Windows
description: Интеграция Служб мобильного взаимодействия Azure Reach с универсальными приложениями для Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8472af2ac72a1976871970963851be775b19859b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Интеграция пакета SDK Reach для универсальных приложений для Windows
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе [Интеграция пакета SDK Engagement для универсальных приложений для Windows](mobile-engagement-windows-store-integrate-engagement.md) .

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Внедрение пакета SDK для Engagement Reach в проект универсального приложения для Windows
Ничего добавлять не нужно. `EngagementReach` уже включены в проект.

> [!TIP]
> Вы можете изменить изображения, которые находятся в папке `Resources` проекта, в частности фирменный значок (по умолчанию используется значок Engagement). Для универсальных приложений также можно переместить папку `Resources` в общий проект, чтобы сделать его содержимое доступным для нескольких приложений, но необходимо сохранить файл `Resources\EngagementConfiguration.xml` в папке по умолчанию, так как он зависит от платформы.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Включение службы уведомлений Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Только для Windows 8.x и Windows Phone 8.1
Чтобы использовать **службу уведомлений Windows** (WNS), в файле `Package.appxmanifest` на вкладке `Application UI` щелкните `All Image Assets` в поле слева. Справа от поля в `Notifications` измените значение `toast capable` с `(not set)` на `(Yes)`.

### <a name="all-platforms"></a>Все платформы
Необходимо синхронизировать приложение с учетной записью Майкрософт и платформой Engagement. Для этого необходимо создать учетную запись или войти в [Центр разработки для Windows](https://dev.windows.com). Затем создайте новое приложение и найдите SID и секретный ключ. В интерфейсном компоненте Engagement перейдите в параметры приложения и в разделе `native push` вставьте свои учетные данные. После этого щелкните правой кнопкой мыши проект и выберите `store` и `Associate App with the Store...`. Чтобы синхронизировать созданное ранее приложение, просто выберите его.

## <a name="initialize-the-engagement-reach-sdk"></a>Запуск пакета SDK для Engagement Reach
Измените `App.xaml.cs`:

* Вставьте `EngagementReach.Instance.Init` сразу после `EngagementAgent.Instance.Init` в методе `InitEngagement`:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` выполняется в выделенном потоке. Вам не придется делать это самостоятельно.

> [!NOTE]
> Если вы используете push-уведомления в другом месте в приложении, то необходимо [предоставить общий доступ к каналу push-уведомлений](#push-channel-sharing) с помощью Engagement Reach.
> 
> 

## <a name="integration"></a>Интеграция
Engagement предоставляет два способа добавления баннеров в приложении Reach и внутренние представления для объявлений и опросов в приложении: интеграция наложения и интеграция веб-представлений вручную. На одной странице следует использовать только один способ.

Чтобы выбрать нужный вариант интеграции, обратите внимание на приведенные далее рекомендации.

* Выберите интеграцию наложения, если ваши страницы уже наследуют от агента `EngagementPage`— вам придется лишь заменить `EngagementPage` на `EngagementPageOverlay` и `xmlns:engagement="using:Microsoft.Azure.Engagement"` на `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` на страницах.
* Выберите интеграцию веб-представлений вручную, чтобы добиться точного размещения пользовательского интерфейса Reach на страницах, или если вы не хотите добавлять на страницы еще один уровень наследования. 

### <a name="overlay-integration"></a>Интеграции наложения
Наложение Engagement динамически добавляет элементы пользовательского интерфейса, используемые для отображения рекламных кампаний на странице. Если наложение не подходит для макета, рекомендуется использовать интеграцию веб-представлений вручную.

В XAML-файле измените ссылку `EngagementPage` на `EngagementPageOverlay`.

* Добавьте в объявления пространств имен:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Замените `engagement:EngagementPage` на `engagement:EngagementPageOverlay`:

**EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Затем в CS-файле пометьте страницу тегом `EngagementPageOverlay`, а не `EngagementPage`, и импортируйте `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Замените `EngagementPage` на `EngagementPageOverlay`:

**EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Наложение Engagement добавляет элемент `Grid` поверх страницы и два элемента `WebView` — одни для баннера и другой для внутреннего представления.

Элементы наложения можно настроить непосредственно в файле `EngagementPageOverlay.cs`.

### <a name="web-views-manual-integration"></a>Интеграция веб-представлений вручную
Reach будет искать на страницах два элемента `WebView`, ответственных за отображение баннера и внутреннего представления. Единственное, что нужно сделать — добавить эти два элемента `WebView` на страницы. Ниже приведен пример.

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


В этом примере элементы `WebView` растягиваются в соответствии с размером их контейнера, который автоматически меняет их размер в случае изменения размера экрана или поворота окна.

> [!WARNING]
> Важно сохранить те же имена — `engagement_notification_content` и `engagement_announcement_content` — для элементов `WebView`. Reach определяет элементы по имени. 
> 
> 

## <a name="handle-datapush-optional"></a>Обработка отправленных данных (необязательно)
Если вы хотите, чтобы приложения могли получать отправленные данные рекламных кампаний, необходимо реализовать два события класса EngagementReach:

В App.xaml.cs в конструкторе App() добавьте:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Как можно видеть, обратный вызов каждого метода возвращает логическое значение. После отправки данных Engagement отправляет отзыв серверной части. Если обратный вызов возвращает значение false, будет отправлен отзыв `exit` . В противном случае отправляется `action`. Если для событий не задан обратный вызов, Engagement будет возвращен отзыв `drop` .

> [!WARNING]
> Engagement не может получать несколько отзывов для отправленных данных. Не забывайте, что если вы планируете установить для события несколько обработчиков, отзывы будут соответствовать последнему из них. Во избежание путаницы с отзывами на внешних компонентах советуем, чтобы в этом случае возвращаемое значение было всегда одинаковым.
> 
> 

## <a name="customize-ui-optional"></a>Настройка пользовательского интерфейса (необязательно)
### <a name="first-step"></a>Первый шаг
Мы позаботились о том, чтобы вы могли настраивать функциональность пользовательского интерфейса по своему усмотрению.

Для этого необходимо создать подкласс класса `EngagementReachHandler` .

**Пример кода:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Затем задайте содержимое поля `EngagementReach.Instance.Handler`, используя настраиваемый объект в классе `App.xaml.cs` метода `App()`.

**Пример кода:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> По умолчанию Engagement использует собственную реализацию `EngagementReachHandler`.
> Ее не обязательно создавать отдельно, но даже если вы ее создадите, вам не придется переопределять каждый метод. Поведение по умолчанию — выбор базового объекта Engagement.
> 
> 

### <a name="web-view"></a>Веб-представление
По умолчанию рекламная кампания будет использовать внедренные ресурсы библиотеки DLL для отображения уведомлений и страниц.

Чтобы обеспечить все возможности настройки, мы используем только веб-представление. Если вы хотите настроить макеты, переопределите непосредственно файлы ресурсов `EngagementAnnouncement.html` и `EngagementNotification.html`. Для правильной работы Engagement весь код должен быть в тегах `<body></body>` . Однако можно добавить тег снаружи `engagement_webview_area`.

Тем не менее, вы можете использовать собственные ресурсы.

Вы можете переопределить методы `EngagementReachHandler` в подклассе, чтобы в Engagement использовались ваши макеты, но не забудьте внедрить механизм Engagement:

**Пример кода:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


По умолчанию AnnouncementHTML — это `ms-appx-web:///Resources/EngagementAnnouncement.html`. Представляет HTML-файл, который задает структуру содержимого push-сообщения (текстовое объявление, веб-объявление и объявление опроса). AnnouncementName — `engagement_announcement_content`. Это имя структуры веб-представления на XAML-странице.

NotfificationHTML — `ms-appx-web:///Resources/EngagementNotification.html`. Представляет HTML-файл, который задает структуру уведомления в push-сообщении. NotfificationName — `engagement_notification_content`. Это имя структуры веб-представления на XAML-странице.

### <a name="customization"></a>Настройка
Вы можете настроить необходимое веб-представление уведомлений и объявлений, если сохраните объект Engagement. Проследите, чтобы объект веб-представления был описан трижды: первый раз — в XAML-файле, второй раз — в CS-файле в методе setwebview() и третий раз в HTML-файле.

* В XAML-файле описывается текущий компонент веб-представления графического макета.
* В CS-файл можно определить setwebview(), в котором задается размер двух веб-представлений (уведомления, объявления). Это очень эффективно при изменении размера приложения.
* В HTML-файле Engagement описывается содержимое веб-представления, его структура и взаимное расположение элементов.

### <a name="launch-message"></a>Запуск сообщения
Если щелкнуть системное уведомление (всплывающее уведомление), Engagement запустит приложение, загрузит содержимое push-сообщений и отобразит страницу для соответствующей кампании.

Между запуском приложения и отображением страницы есть определенная задержка (в зависимости от скорости сети).

Чтобы уведомить пользователя о загрузке, необходимо предоставить визуальную информацию, например индикатор выполнения. Engagement не может обработать ее самостоятельно, но предоставляет несколько обработчиков, которыми вы можете воспользоваться.

Чтобы реализовать обратный вызов, в файле App.xaml.cs в Public App(){} добавьте следующее:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Вы можете задать обратный вызов в методе «Public App(){}» файла `App.xaml.cs` (предпочтительно перед вызовом `EngagementReach.Instance.Init()`).

> [!TIP]
> Каждый обработчик вызывается потоком пользовательского интерфейса. При использовании MessageBox или других компонентов, связанных с пользовательским интерфейсом, об этом можно не беспокоится.
> 
> 

## <a id="push-channel-sharing"></a> Общий доступ к каналу push-уведомлений
При использовании push-уведомлений в других целях в приложении необходимо использовать функцию общего доступа к каналу push-уведомлений из пакета SDK для Engagement. Это позволяет избежать пропуска push-уведомлений.

* Можно предоставить собственный канал push-уведомлений для инициализации Engagement Reach. Пакет SDK будет использовать его вместо запроса нового канала.

Обновите инициализацию Engagement Reach с применением своего канала push-уведомлений в методе `InitEngagement` из файла `App.xaml.cs`:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Если вам нужно просто использовать канал push-уведомлений после инициализации Reach, можно задать обратный вызов в Engagement Reach для получения канала push-уведомлений после его создания с помощью пакета SDK.

Задайте обратный вызова в любом месте **после** инициализации Reach:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Совет по настраиваемой схеме
Мы предоставляем возможность использования настраиваемой схемы. Вы можете отправлять из внешнего компонента Engagement универсальный код ресурса другого типа. Этот код будет использоваться в работе приложения. Управление схемой по умолчанию, например `http, ftp, ...`, осуществляет операционная система Windows. Если на устройстве не установлено приложение по умолчанию, появится окно с запросом. Также можно создать собственную настраиваемую схему для своего приложения.

Настраиваемую схему можно с легкостью задать в приложении, открыв `Package.appxmanifest` и панель `Declarations`. Прокрутите ползунок поля «Доступные объявления», выберите `Protocol` и добавьте его. Замените значение в поле `Name` необходимым именем нового протокола.

Чтобы использовать этот протокол, измените файл `App.xaml.cs`, задав в нем метод `OnActivated`, и не забудьте также инициализировать Engagement:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

