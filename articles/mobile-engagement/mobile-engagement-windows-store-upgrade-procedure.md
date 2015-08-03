<properties 
	pageTitle="Процедуры обновления SDK универсальных приложений для Windows " 
	description="Процедуры обновления пакета SDK универсальных приложений для Windows для Azure Mobile Engagement" 					
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
	ms.date="04/02/2015" 
	ms.author="piyushjo" />

#Процедуры обновления SDK универсальных приложений для Windows

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить несколько процедур. Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции «с 0.9.0 в 0.10.1», а затем процедуру миграции «с 0.10.1 в 0.11.0».

##Из версии 2.0.0 в 3.0.0

### Ресурсы
Этот шаг относится только к настраиваемым ресурсам. Если вы настроили ресурсы, предоставляемые в SDK (HTML-код, изображения, наложения), необходимо создать их резервную копию перед обновлением ресурсов и повторным применением к ним настроек.

##От версии 1.1.1 до версии 2.0.0

Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение на платформе Azure Mobile Engagement.

> [Azure.IMPORTANT]Службы Capptain и Azure Mobile Engagement отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Mobile Engagement.

При переносе с использованием более ранней версии сначала ознакомьтесь с информацией о переносе в версии 1.1.1 на веб-сайте Capptain, а затем примените следующую процедуру.

### Пакет NuGet

Замените **Capptain.WindowsPhone** на пакет Nuget **MicrosoftAzure.MobileEngagement**.

### Применение Mobile Engagement

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
	> [AZURE.IMPORTANT]Меняется также и наложение. Его новым пространство имен является `Microsoft.Azure.Engagement.Overlay`. Оно должно использоваться в XAML- и CS-файлах. Кроме того, `CapptainGrid` будет называться `EngagementGrid`, `capptain_notification_content` — `engagement_notification_content`, а `capptain_announcement_content` — `engagement_announcement_content`.
	
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

### Объявление проекта

В Package.appxmanifest были обновлены следующие элементы `File Type Associations`:

 -   capptain_reach_content заменяется engagement_reach_content
 -   capptain_log_file заменяется engagement_log_file

### Идентификатор приложения и ключ SDK

Engagement использует строку подключения. При использовании Mobile Engagement не нужно указывать идентификатор приложения и ключ SDK. Достаточно просто задать строку подключения. Ее можно настроить в файле EngagementConfiguration.

Конфигурацию Engagement можно определить в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы указать:

-   строку подключения приложения между тегами `<connectionString>` и `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

Строка подключения для приложения отображается на портале управления Azure.

### Изменение имени элементов

Каждый из элементов с именем *capptain* был переименован в *engagement*. Аналогичным образом имя *Capptain* изменилось на *Engagement*.

Примеры часто используемых элементов Capptain:

-   CapptainConfiguration теперь называется EngagementConfiguration.
-   CapptainAgent теперь называется EngagementAgent.
-   CapptainReach теперь называется EngagementReach.
-   CapptainHttpConfig теперь называется EngagementHttpConfig.
-   GetCapptainPageName теперь называется GetEngagementPageName.

Обратите внимание, что переименование также влияет на переопределенные методы.

 

<!---HONumber=July15_HO4-->