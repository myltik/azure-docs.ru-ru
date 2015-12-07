<properties 
	pageTitle="Общие сведения о пакете SDK универсальных приложений для Windows" 
	description="Общие сведения о пакете SDK универсальных приложений для Windows для Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Общие сведения о пакете SDK для универсальных приложений для Windows для Azure Mobile Engagement

Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в универсальное приложение для Windows. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-windows-store-dotnet-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-windows-store-sdk-content.md).

##Процедуры по интеграции

1. Начните с этого раздела: [Как интегрировать Mobile Engagement в универсальное приложение для Windows](mobile-engagement-windows-store-integrate-engagement.md)

2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в универсальное приложение для Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Mobile Engagement в универсальном приложении для Windows](mobile-engagement-windows-store-use-engagement-api.md)

##Заметки о выпуске

###3\.2.0 (11/20/2015)

-   Добавлена поддержка универсальных приложений для Windows 10.
-   Добавлена функция совместного использования push-уведомлений для устранения конфликтов каналов (теперь она совместима с центрами уведомлений Azure).
-   Исправлена проблема сбоев при запросе идентификатора устройства сразу после инициализации.
-   Улучшены журналы консоли.
-   Исправлена проблема сбоев при анализе некоторых необработанных исключений.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-windows-store-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить ряд процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-windows-store-upgrade-procedure.md). Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции «с 0.9.0 в 0.10.1», а затем процедуру миграции «с 0.10.1 в 0.11.0».

###С 3.1.0 на 3.2.0

#### Ресурсы
Этот шаг относится только к настраиваемым ресурсам. Если вы настроили ресурсы, предоставляемые в SDK (HTML-код, изображения, наложения), необходимо создать их резервную копию перед обновлением ресурсов и повторным применением к ним настроек.

#### Интеграция веб-представления
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

### Обновление предыдущих версий

См. статью [Процедуры обновления](mobile-engagement-windows-store-upgrade-procedure/).

<!---HONumber=AcomDC_1125_2015-->