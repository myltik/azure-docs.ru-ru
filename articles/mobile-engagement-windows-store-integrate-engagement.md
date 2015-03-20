<properties 
	pageTitle="Обзор пакета SDK для Магазина Windows для Azure Mobile Engagement" 
	description="Последние обновления и указания для пакета SDK для Магазина Windows для Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Магазин Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#Как интегрировать Engagement в Windows

Здесь описана самая простая процедура активации функций раздела аналитики и мониторинга Engagement в приложении Windows.

Достаточно выполнить следующие шаги, чтобы активировать отчеты по журналам, которые необходимы для вычисления всех статистических данных, касающихся пользователей, сеансов, действий, сбоев и технической информации. Отчеты по журналам, необходимые для вычисления других статистических данных, например, касающихся событий, ошибок и заданий, необходимо активировать вручную с помощью Engagement API (см. статью [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении Магазина Windows](../mobile-engagement-windows-store-use-engagement-api/)), так как эти статистические данные зависят от приложения.

##Поддерживаемые версии

Этот пакет SDK для Windows поддерживает только:

-   приложение Магазина Windows 8 на C\#;
-   приложение Магазина Windows 8,1 на C\#;

##Внедрение пакета SDK для Engagement в проект Windows

Внедрите пакет SDK для Engagement с помощью диспетчера пакетов NuGet для платформы разработки Майкрософт.

Пакет NuGet для Mobile Engagement еще не доступен в Интернете. Скачайте архив SDK, распакуйте его, а затем в Visual Studio щелкните правой кнопкой мыши проект, чтобы перейти к "Управление пакетами Nuget" - "Параметры", и добавьте папку lib как новый источник пакета.

> [AZURE.NOTE] NuGet - это главный диспетчер пакетов для проектов .net. Он скачивает, добавляет и копирует все необходимое для использования пакета SDK для Engagement. Приложение Engagement C\# Metro в Windows 8 использует сторонние библиотеки (Microsoft.Bcl.Build v1.0.10и пакет Microsoft.Bcl Portability Pack v1.1.3), чтобы обеспечить возможность переносимости между разными платформами. NuGet автоматически запросит вас их установить. Дополнительную информацию см. на [веб-сайте NuGet].

##Добавление возможностей

Для надлежащей работы пакету SDK для Engagement требуются некоторые возможности пакета SDK для Windows.

Откройте файл `Package.appxmanifest` и убедитесь, что следующие возможности отмечены:

-   `Internet (Client)`

Перейдите на панель `Declarations` файла Package.appxmanifest.

В разделе `Доступные объявления` выберите `Сопоставления типов файлов` и добавьте их. На экране справа в поле `Имя` введите `engagement_log_file`, а в поле "Тип файла" - `.set`.

Затем в разделе `Доступные объявления` выберите `Средство обновления кэшированных файлов` и добавьте его.

##Запуск пакета SDK для Engagement

### Конфигурация Engagement

Конфигурация Engagement централизована в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы указать:

-   строку подключения в приложении между тегами `<connectionString>` и `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

### Инициализация Engagement

При создании проекта создается файл `App.xaml.cs`. Этот класс наследует классу `Application` и содержит много важных методов. Он также будет использоваться для инициализации пакета SDK для Engagement.

Измените `App.xaml.cs`:

-   Добавьте операторы `using`:

			using Microsoft.Azure.Engagement;

-   Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

-   При запуске приложения с использованием настраиваемой схемы, другого приложения или командной строки вызывается метод `OnActivated`. При активации приложения необходимо также запустить агент Engagement. Чтобы это сделать, переопределите метод `OnActivated`:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] Настоятельно рекомендуем не добавлять инициализацию Engagement в другом расположении приложения.

##Упрощенные отчеты

### Рекомендуемый метод: перегрузить классы `Page`

Чтобы активировать отчет по всем журналам, необходимым для Engagement при вычислении статистики пользователй, сеансов, действий, сбоев и технической информации, можно просто задать наследование подклассами `Page` классам `EngagementPage` .

Ниже приведен пример того, как сделать это для страницы приложения. То же самое можно сделать для всех страниц приложения.

#### Исходный файл на C\#

Измените файл страницы `.xaml.cs`:

-   Добавьте операторы `using`:

			using Microsoft.Azure.Engagement;

-   Замените `Page` на `EngagementPage`:

**Без Engagement:**

			namespace Example
			{
			  public sealed partial class ExamplePage : Page
			  {
			    [...]
			  }
			}

**С Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.IMPORTANT] Если страница переопределяет метод `OnNavigatedTo`, необходимо обязательно вызвать `base.OnNavigatedTo(e)`. В противном случае о действии не будет сообщено (`EngagementPage` вызывает `StartActivity` внутри метода `OnNavigatedTo`).

#### XAML-файл

Измените файл страницы `.xaml`:

-   Добавьте в объявления пространств имен:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Замените `Page` на `engagement:EngagementPage`:

**Без Engagement:**

			<Page>
			    <!-- layout -->
			    ...
			</Page>

**С Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <!-- layout -->
			    ...
			</engagement:EngagementPage >

#### Переопределение действия по умолчанию

По умолчанию имя класса страницы сообщается как имя действия без дополнительной информации. Если класс использует суффикс Page, Engagement также удалит его.

Если требуется переопределить поведение по умолчанию имени, просто добавьте в код следующее:

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

Подробнее о дополнительной информации можно узнать на странице \<windows-sdk-extras-parameters\>.

Эти методы вызываются из метода `OnNavigatedTo` вашей страницы.

### Альтернативный метод: вызов `StartActivity()` вручную

Если вам не удается перегрузить классы `Page` или вы не хотите этого делать, запустите действия, вызвав методы `EngagementAgent` напрямую.

Рекомендуем вызывать `StartActivity` внутри метода `OnNavigatedTo` своей страницы.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Пакет SDK для Windows автоматически вызывает метод `EndActivity` при закрытии приложения. Таким образом, мы *HIGHLY* рекомендуем вызывать метод `StartActivity` при каждом изменении действия пользователя и *NEVER* не вызывать метод `EndActivity`, так как это приводит к завершению текущего сеанса.

##Расширенные отчеты

При желании можно также сообщать об определенных событиях, ошибках и заданиях приложения. Это можно сделать с помощью других методов в классе `EngagementAgent`. Engagement API позволяет использовать все дополнительные возможности Engagement.

Дополнительную информацию см. в статье "Как использовать Engagement API в Windows".

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
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### Пакетный режим [бета-версия]

По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение создает отчеты по журналам очень часто, лучше поместить журналы в буфер и создавать отчеты по всем журналам одновременно через регулярные промежутки времени (это называется "пакетный режим").

Для этого вызовите следующий метод:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Аргумент представляет собой значение в **миллисекундах**. Если вам в любое время потребуется повторно активировать ведение журнала в реальном времени, просто вызовите метод без каких-либо параметров или со значением 0.

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы советуем использовать пороговое значение пакета не более чем 30 000 (30 с).

> [AZURE.WARNING] Пороговое значение для пакета нельзя задать на период меньше 1 с. При попытке задать значение менее одной секунды трассировка в пакете SDK отобразится с ошибкой, и будет автоматически восстановлено значения по умолчанию, т. е. ноль секунд. Это приведет к тому, что пакет SDK начнет создавать отчеты по журналам в режиме реального времени.

[здесь]:http://www.nuget.org/packages/Capptain.WindowsCS
[Веб-сайт NuGet]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
