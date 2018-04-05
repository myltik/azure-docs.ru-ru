---
title: 'Процедуры обновления SDK универсальных приложений для Windows '
description: Процедуры обновления пакета SDK универсальных приложений для Windows для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a9d6cbcdf353f7eea991c344c3efe65378abe336
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Процедуры обновления SDK универсальных приложений для Windows 
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить несколько процедур. Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции «с 0.9.0 в 0.10.1», а затем процедуру миграции «с 0.10.1 в 0.11.0».

## <a name="from-330-to-340"></a>С 3.3.0 в 3.4.0
### <a name="test-logs"></a>Журналы тестирования
Теперь журналы консоли, созданные с помощью пакета SDK, можно включать, отключать или фильтровать. Чтобы настроить это действие, обновите свойство `EngagementAgent.Instance.TestLogEnabled` до одного из значений, доступных в перечислении `EngagementTestLogLevel`, например:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Ресурсы
Было улучшено наложение Reach. Оно входит в состав ресурсов пакета SDK NuGet.

Во время обновления до новой версии пакета SDK можно выбрать возможность сохранения существующих файлов из папки наложения ресурсов.

* Если вы используете предыдущее наложение или интегрируете элементы `WebView` вручную, можно оставить существующие файлы — наложение будет работать. 
* Если вы хотите выполнить обновление до нового наложения, просто замените всю папку `overlay` из ресурсов на новую из пакета SDK (приложения UWP: после обновления можно получить новую папку наложения из %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Новое наложение перезапишет изменения, внесенные в предыдущую версию.
> 
> 

## <a name="from-320-to-330"></a>С 3.2.0 в 3.3.0
### <a name="resources"></a>Ресурсы
Этот шаг относится только к настраиваемым ресурсам. Если вы настроили ресурсы, предоставляемые в SDK (HTML-код, изображения, наложения), необходимо создать их резервную копию перед обновлением ресурсов и повторным применением к ним настроек.

## <a name="from-310-to-320"></a>С 3.1.0 на 3.2.0
### <a name="resources"></a>Ресурсы
Этот шаг относится только к настраиваемым ресурсам. Если вы настроили ресурсы, предоставляемые в SDK (HTML-код, изображения, наложения), необходимо создать их резервную копию перед обновлением ресурсов и повторным применением к ним настроек.

### <a name="webview-integration"></a>Интеграция веб-представления
В этой версии улучшено соответствие форм-факторам различных устройств. Убедитесь, что интеграция веб-представлений выглядит представленным ниже образом.

На XAML-странице ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

В соответствующем файле CS:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>Из версии 2.0.0 в 3.0.0
### <a name="resources"></a>Ресурсы
Этот шаг относится только к настраиваемым ресурсам. Если вы настроили ресурсы, предоставляемые в SDK (HTML-код, изображения, наложения), необходимо создать их резервную копию перед обновлением ресурсов и повторным применением к ним настроек.

## <a name="from-111-to-200"></a>От версии 1.1.1 до версии 2.0.0
Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение Служб мобильного взаимодействия Azure. 

> [!IMPORTANT]
> Службы Capptain и Служб мобильного взаимодействия Azure отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Служб мобильного взаимодействия.
> 
> 

При переносе с использованием более ранней версии сначала ознакомьтесь с информацией о переносе в версии 1.1.1 на веб-сайте Capptain, а затем примените следующую процедуру.

### <a name="nuget-package"></a>Пакет NuGet
Замените **Capptain.WindowsPhone** на пакет NuGet **MicrosoftAzure.MobileEngagement**.

### <a name="applying-mobile-engagement"></a>Применение Служб мобильного взаимодействия
В пакете SDK используется термин `Engagement`, поэтому необходимо обновить проект с учетом этого изменения.

Необходимо удалить текущий пакет NuGet Capptain. Советуем удалить все, что было изменено в папке ресурсов Capptain. Если хотите сохранить эти файлы, скопируйте их в другое место.

После этого установите в проекте новый пакет NuGet для Microsoft Azure Engagement. Его можно найти непосредственно на [веб-сайте NuGet] или здесь в указателе. В результате выполнения этого действия все файлы ресурсов, используемые Engagement, будут заменены, а в ссылки проекта будет добавлена новая библиотека DLL для Engagement.

Ссылки проекта необходимо очистить, удалив ссылки на библиотеку DLL Capptain. Если этого не сделать, возникнет конфликт с версией Capptain и будут происходить ошибки.

После настройки ресурсов Capptain скопируйте содержимое старых файлов и вставьте его в новые файлы Engagement. Обратите внимание, что XAML- и CS-файлы нужно обновить.

После выполнения этих шагов необходимо заменить старые ссылки Capptain на новые ссылки Engagement.

1. Все пространства имен Capptain должны быть обновлены.
   
    Перед миграцией:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    После миграции:
   
        using Microsoft.Azure.Engagement;
2. Все классы Capptain, содержащие Capptain должны содержать Engagement.
   
    Перед миграцией:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    После миграции:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Для XAML-файлов изменятся также пространство имен и атрибуты Capptain.
   
    Перед миграцией:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    После миграции:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Изменяется страница наложения
   
   > [!IMPORTANT]
   > Меняется также и наложение. Его новым пространство имен является `Microsoft.Azure.Engagement.Overlay`. Оно должно использоваться в XAML- и CS-файлах. Кроме того, `CapptainGrid` будет называться `EngagementGrid`, `capptain_notification_content` — `engagement_notification_content`, а `capptain_announcement_content` — `engagement_announcement_content`.
   > 
   > 
   
    Для наложения:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Это будет выглядеть так:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Обратите внимание, что для использования Engagement были переименованы и другие ресурсы, такие как изображения Capptain и HTML-файлы.

### <a name="project-declaration"></a>Объявление проекта
В Package.appxmanifest были обновлены следующие элементы `File Type Associations` :

* capptain\_reach\_content to engagement\_reach\_content
* capptain\_log\_file to engagement\_log\_file

### <a name="application-id--sdk-key"></a>Идентификатор приложения и ключ SDK
Engagement использует строку подключения. При использовании Служб мобильного взаимодействия не нужно указывать идентификатор приложения и ключ SDK. Достаточно просто задать строку подключения. Ее можно настроить в файле EngagementConfiguration.

Конфигурацию Engagement можно определить в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы указать:

* строку подключения приложения между тегами `<connectionString>` and `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

### <a name="items-name-change"></a>Изменение имени элементов
Каждый из элементов с именем *capptain* был переименован на *engagement*. Аналогичным образом имя *Capptain* изменилось на *Engagement*.

Примеры часто используемых элементов Capptain:

* CapptainConfiguration теперь называется EngagementConfiguration.
* CapptainAgent теперь называется EngagementAgent.
* CapptainReach теперь называется EngagementReach.
* CapptainHttpConfig теперь называется EngagementHttpConfig.
* GetCapptainPageName теперь называется GetEngagementPageName.

Обратите внимание, что переименование также влияет на переопределенные методы.

