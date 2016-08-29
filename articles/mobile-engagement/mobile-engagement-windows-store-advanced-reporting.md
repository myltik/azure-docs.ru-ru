.<properties
	pageTitle="Создание расширенных отчетов для универсальных приложений для Windows с помощью MobileApps Engagement"
	description="Интеграция Azure Mobile Engagement с универсальными приложениями для Windows" 					
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

# Создание расширенных отчетов с помощью пакета SDK службы Engagement для универсальных приложений для Windows

> [AZURE.SELECTOR]
- [Универсальная платформа Windows](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

В этом разделе описаны дополнительные сценарии создания отчетов в универсальном приложении для Windows. В них описываются параметры, которые можно применить для приложения, созданного в [учебнике по началу работы](mobile-engagement-windows-store-dotnet-get-started.md).

## Предварительные требования

[AZURE.INCLUDE [Предварительные требования](../../includes/mobile-engagement-windows-store-prereqs.md)]

Прежде чем приступать к этому учебнику, необходимо изучить учебник [по началу работы](mobile-engagement-windows-store-dotnet-get-started.md), в котором намеренно подобраны понятные и простые задания. В этом учебнике рассматриваются дополнительные параметры, доступные для выбора.

## Указание конфигурации Engagement во время выполнения

Конфигурация Engagement сосредоточена в файле `Resources\EngagementConfiguration.xml` вашего проекта, где она была задана при изучении учебника [по началу работы](mobile-engagement-windows-store-dotnet-get-started.md).

Однако вы можете также указать его во время выполнения, так как можно вызвать приведенный ниже метод до инициализации агента Engagement.

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## Рекомендуемый метод: перегрузка классов `Page`

Чтобы активировать создание отчетов по всем журналам, необходимым для службы Engagement при вычислении статистики пользователей, сеансов, действий, сбоев и технической информации, вы можете задать для подклассов `Page` наследование из классов `EngagementPage`.

Ниже приведен пример страницы приложения. То же самое можно сделать для всех страниц приложения.

### Исходный файл на C#

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

### XAML-файл

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

### Переопределение действия по умолчанию

По умолчанию имя класса страницы сообщается как имя действия без дополнительной информации. Если класс использует суффикс Page, служба Engagement также удалит его.

Чтобы переопределить поведение по умолчанию для имени, добавьте следующий код.

		// in the .xaml.cs file
		protected override string GetEngagementPageName()
		{
		  /* your code */
		  return "new name";
		}

Чтобы сообщить дополнительную информацию о действии, добавьте приведенный ниже код.

		// in the .xaml.cs file
		protected override Dictionary<object,object> GetEngagementPageExtra()
		{
		  /* your code */
		  return extra;
		}

Эти методы вызываются из метода `OnNavigatedTo` вашей страницы.

### Альтернативный метод: вызов `StartActivity()` вручную

Если вам не удается перегрузить классы `Page` или вы не хотите этого делать, запустите действия, вызвав методы `EngagementAgent` напрямую.

Рекомендуем вызывать `StartActivity` внутри метода `OnNavigatedTo` своей страницы.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]  Убедитесь, что сеанс завершен правильно.
>
> Пакет SDK для универсальных приложений для Windows автоматически вызывает метод `EndActivity` при закрытии приложения. Поэтому мы **настоятельно** рекомендуем вызывать метод `StartActivity` при каждом изменении действия пользователя и **никогда** не вызывать метод `EndActivity`. Этот метод уведомляет сервер Engagement о том, что текущий пользователь вышел из приложения, и это отразится во всех журналах приложений.

## Расширенные отчеты

При желании можно также сообщать об определенных событиях, ошибках и заданиях приложения. Это можно сделать с помощью других методов в классе `EngagementAgent`. Engagement API позволяет использовать все дополнительные возможности Engagement.

Дополнительную информацию см. в разделе [Как использовать API для расширенного добавления тегов Mobile Engagement в универсальном приложении для Windows](mobile-engagement-windows-store-use-engagement-api.md).

<!---HONumber=AcomDC_0817_2016-->