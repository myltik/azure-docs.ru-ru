<properties 
	pageTitle="Интеграция пакета SDK для Windows Phone для Azure Mobile Engagement" 
	description="Как интегрировать Engagement в Windows Phone" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Магазин Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#Как интегрировать Engagement в Windows Phone

Здесь описана самая простая процедура активации функций раздела аналитики и мониторинга Engagement в приложении Windows Phone.

Достаточно выполнить следующие шаги, чтобы активировать отчеты по журналам, которые необходимы для вычисления всех статистических данных, касающихся пользователей, сеансов, действий, сбоев и технической информации. Отчеты по журналам, необходимые для вычисления других статистических данных, например, касающихся событий, ошибок и заданий, необходимо активировать вручную с помощью Engagement API (см. статью [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md) ), так как эти статистические данные зависят от приложения.

##Поддерживаемые версии

Пакет SDK для Windows Phone для Engagement можно интегрировать только в приложения под управлением:

-   Windows Phone OS 8.0
-   ОС Windows Phone 8.1 с Silverlight.

Пакет SDK для Windows Phone для Engagement совместим с устройствами Windows Phone версий 8.0 и 8.1.

##Внедрение пакета SDK для службы Engagement в проект Windows Phone

Пакет NuGet для Mobile Engagement еще не доступен в Интернете. Скачайте архив SDK, распакуйте его, а затем в Visual Studio щелкните правой кнопкой мыши проект, чтобы перейти к "Управление пакетами Nuget" - "Параметры", и добавьте папку lib как новый источник пакета.


> [AZURE.IMPORTANT] NuGet - это главный диспетчер пакетов для проектов .net. Он скачивает, добавляет и копирует все необходимое для использования пакета SDK для Engagement. Engagement в Windows Phone 8 использует сторонние библиотеки (Microsoft.Bcl.Build v1.0.10и пакет Microsoft.Bcl Portability Pack v1.1.3), чтобы обеспечить возможность переносимости между разными платформами. NuGet автоматически запросит вас их установить. Дополнительную информацию см. на [веб-сайте NuGet](http://docs.nuget.org/docs/start-here/overview)..

##Добавление возможностей

Для надлежащей работы пакету SDK для Engagement требуются некоторые возможности пакета SDK для Windows Phone.

Откройте файл `WMAppManifest.xml` и убедитесь, что следующие возможности отмечены на панели `Capabilities` :

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##Запуск пакета SDK для Engagement

### Конфигурация Engagement

Конфигурация Engagement централизована в файле `Resources\EngagementConfiguration.xml` проекта.

Измените файл, чтобы указать:

-   строку подключения в приложении между тегами `<connectionString>` и `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

### Инициализация Engagement

При создании проекта создается файл `App.xaml.cs` . Этот класс наследуется из `Application` и содержит много важных методов. Он также будет использоваться для инициализации пакета SDK для Engagement.

Измените `App.xaml.cs`:

-   Добавьте операторы `using` :

			using Microsoft.Azure.Engagement;

-   Вставьте `EngagementAgent.Instance.Init` в метод `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   Вставьте `EngagementAgent.Instance.OnActivated` в метод `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] Настоятельно рекомендуем не добавлять инициализацию Engagement в другом расположении приложения. Однако учтите, что метод `EngagementAgent.Instance.Init` выполняется в отдельном потоке, а не в потоке пользовательского интерфейса.

##Упрощенные отчеты

### Рекомендуемый метод: перегрузите классы `PhoneApplicationPage`

Чтобы активировать отчеты по всем журналам, необходимые в Engagement для вычисления статистических данных о пользователях, сеансах, действиях, сбоях и технической информации, можно просто сделать так, чтобы все ваши подклассы `PhoneApplicationPage` наследовались от классов EngagementPage.

Ниже приведен пример того, как сделать это для страницы приложения. То же самое можно сделать для всех страниц приложения.

#### Исходный файл на C\#

Измените файл страницы `.xaml.cs`:

-   Добавьте операторы `using`:

			using Microsoft.Azure.Engagement;

-   Замените `PhoneApplicationPage` на `EngagementPage`:

**Без Engagement:**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**С Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] Если страница наследуется из метода `OnNavigatedTo`, будьте внимательны при вызове `base.OnNavigatedTo(e)`. В противном случае служба не сообщит о действии. Более того, `EngagementPage` вызывает `StartActivity` в методе `OnNavigatedTo`.

#### XAML-файл

Измените файл страницы `.xaml`:

-   Добавьте в объявления пространств имен:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Замените `phone:PhoneApplicationPage` на `engagement:EngagementPage`:

**Без Engagement:**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**С Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### Переопределение действия по умолчанию

По умолчанию имя класса страницы сообщается как имя действия без дополнительной информации. Если класс использует суффикс Page, Engagement также удалит его.

Если требуется переопределить поведение по умолчанию для имени, просто добавьте в код следующую строку:

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			   /* your code */
			   return "new name";
			}

Чтобы сообщить дополнительную информацию о действии, добавьте в код следующую строку:

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			   /* your code */
			   return extra;
			}

Подробнее о такой дополнительной информации см. [здесь](../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters).

Эти методы вызываются из метода `OnNavigatedTo` вашей страницы.

### Альтернативный метод: вызов `StartActivity()` вручную

Если вам не удается перегрузить классы `PhoneApplicationPage` или вы не хотите этого делать, запустите действия, вызвав методы `EngagementAgent` напрямую.

Мы советуем вызывать `StartActivity` в методе `OnNavigatedTo` класса PhoneApplicationPage.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Убедитесь, что сеанс завершен правильно.
>
> Пакет SDK для Windows Phone автоматически вызывает метод `EndActivity` при закрытии приложения. Таким образом, мы **НАСТОЯТЕЛЬНО** рекомендуем вызывать метод `StartActivity` при каждом изменении действия пользователя и **НИКОГДА** не вызывать метод `EndActivity`, так как это приводит к завершению текущего сеанса.

##Расширенные отчеты

При желании можно также сообщать об определенных событиях, ошибках и заданиях приложения. Это можно сделать с помощью других методов в классе `EngagementAgent`. Engagement API позволяет использовать все дополнительные возможности Engagement.

Дополнительную информацию см. в разделе [Как использовать API для расширенного добавления тегов Mobile Engagement в приложение Windows Phone](mobile-engagement-windows-phone-use-the-engagement-api.md).

##Расширенная конфигурация

### Отключение автоматического создания отчетов о сбоях

Вы можете отключить функцию автоматического создания отчетов о сбоях в Engagement. После этого при возникновении необработанного исключения Engagement не будет предпринимать никаких действий.

> [AZURE.WARNING] Если вы планируете отключить эту функцию, учтите, что при возникновении необработанного сбоя в приложении Engagement не будет отправлять уведомление о сбое **И** не закроет сеанс и задания.

Чтобы отключить автоматическое создание отчетов о сбоях, просто настройте конфигурацию в зависимости от того, как она была объявлена:

#### Из файла `EngagementConfiguration.xml`

Задайте для параметра уведомления о сбоях значение `false` между тегами `<reportCrash>` и `</reportCrash>`.

#### Из объекта `EngagementConfiguration` во время выполнения

Задайте для параметра уведомления о сбоях значение false с помощью объекта EngagementConfiguration.

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### Пакетный режим [бета-версия]

По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение создает отчеты по журналам очень часто, лучше поместить журналы в буфер и создавать отчеты по всем журналам одновременно через регулярные промежутки времени (это называется "пакетный режим").

Для этого вызовите следующий метод:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Аргумент представляет собой значение в **миллисекундах**. Если вам в любое время потребуется повторно активировать ведение журнала в реальном времени, просто вызовите метод без каких-либо параметров или со значением 0.

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы советуем использовать пороговое значение пакета не более чем 30 000 (30 с).

> [AZURE.WARNING] Для порогового значения пакета нельзя настроить период менее одной секунды. При попытке задать значение менее одной секунды трассировка в пакете SDK отобразится с ошибкой, и будет автоматически восстановлено значения по умолчанию, т. е. ноль секунд. Это приведет к тому, что пакет SDK начнет создавать отчеты по журналам в режиме реального времени.

<!--HONumber=47-->
