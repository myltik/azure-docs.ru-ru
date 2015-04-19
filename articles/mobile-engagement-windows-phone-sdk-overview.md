<properties 
	pageTitle="Интеграция пакета SDK для Windows Phone для Azure Mobile Engagement" 
	description="Последние обновления и указания для пакета SDK для Windows Phone для Azure Mobile Engagement" 					
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

#Пакет SDK для Windows Phone для Azure Mobile Engagement версии 2.0.0
Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в приложение Windows Phone. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-windows-phone-get-started.md).

Щелкните, чтобы просмотреть статью [Содержимое пакета SDK](mobile-engagement-windows-phone-sdk-content.md).

##Процедуры по интеграции

1. Начните с этого раздела: [Как интегрировать Mobile Engagement в приложение Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)

2. Информация об уведомлениях: [Как интегрировать Reach (Notifications) в приложение Windows Phone](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md)

##Заметки о выпуске

###Версия 2.0.0 (17.02.2015)
-   Первый выпуск Azure Mobile Engagement
-   Конфигурация appId/sdkKey заменена конфигурацией строки подключения.
-   Улучшения безопасности.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-windows-phone-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию пакета SDK, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить несколько процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-windows-phone-upgrade-procedure/). Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции "с 0.9.0 в 0.10.1", а затем процедуру миграции "с 0.10.1 в 0.11.0".

###От версии 1.1.1 до версии 2.0.0

Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение на платформе Azure Mobile Engagement. 

>[Azure.IMPORTANT] Службы Capptain и Azure Mobile Engagement отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Mobile Engagement.

При переносе с использованием более ранней версии сначала ознакомьтесь с информацией о переносе в версии 1.1.1 на веб-сайте Capptain, а затем примените следующую процедуру.

#### Пакет NuGet

Замените Capptain.WindowsPhone.nupkg на azuresdk-mobileengagement-windowsphone-VERSION.nupkg в папке lib архивного пакета.

#### Применение Mobile Engagement

В пакете SDK используется термин `Engagement`, поэтому необходимо обновить проект с учетом этого изменения.

Необходимо удалить текущий пакет NuGet Capptain. Советуем удалить все, что было изменено в папке ресурсов Capptain. Если хотите сохранить эти файлы, скопируйте их в другое место.

После этого установите в проекте новый пакет NuGet для Microsoft Azure Engagement. Его можно найти непосредственно на [веб-сайте NuGet] или здесь в указателе. В результате выполнения этого действия все файлы ресурсов, используемые Engagement, будут заменены, а в ссылки проекта будет добавлена новая библиотека DLL для Engagement.

Ссылки проекта необходимо очистить, удалив ссылки на библиотеку DLL Capptain. Если этого не сделать, возникнет конфликт с версией Capptain и будут происходить ошибки.

После настройки ресурсов Capptain скопируйте содержимое старых файлов и вставьте его в новые файлы Engagement. Обратите внимание, что XAML- и CS-файлы нужно обновить.

После выполнения этих шагов необходимо заменить старые ссылки Capptain на новые ссылки Engagement.

Все пространства имен Capptain должны быть обновлены.

Перед миграцией:

			using Capptain.Agent;
			using Capptain.Reach;

After migration:

			using Microsoft.Azure.Engagement;

All Capptain classes that contain "Capptain" should contain "Engagement".

Before migration:

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

Для XAML-файлов изменятся также пространство имен и атрибуты Capptain.

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

Обратите внимание, что для использования Engagement были переименованы и другие ресурсы, такие как изображения Capptain.

#### Идентификатор приложения и ключ SDK

Engagement использует строку подключения. При использовании Mobile Engagement не нужно указывать идентификатор приложения и ключ SDK. Достаточно просто задать строку подключения. Ее можно настроить в файле EngagementConfiguration.

Конфигурацию Engagement можно определить в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы указать:

-   строку подключения в приложении между тегами `<connectionString>` и `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

#### Изменение имени элементов

Каждый из элементов с именем ...capptain... был переименован в ...Engagement... То же касается Capptain и Engagement.

Примеры для часто используемых элементов Capptain:

> -   CapptainConfiguration теперь называется EngagementConfiguration.
> -   CapptainAgent теперь называется EngagementAgent.
> -   CapptainReach теперь называется EngagementReach.
> -   CapptainHttpConfig теперь называется EngagementHttpConfig.
> -   GetCapptainPageName теперь называется GetEngagementPageName.

Обратите внимание, что переименование также влияет на переопределенные методы.

### Обновление предыдущих версий

См. статью [Процедуры обновления](mobile-engagement-windows-phone-upgrades/).

<!--HONumber=47-->
