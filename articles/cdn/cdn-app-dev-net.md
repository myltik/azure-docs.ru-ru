<properties
	pageTitle="Приступая к работе с библиотекой Azure CDN для .NET | Microsoft Azure"
	description="Узнайте, как с помощью Visual Studio создавать приложения .NET для управления Azure CDN."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="casoper"/>

# Приступая к работе с библиотекой Azure CDN для .NET

С помощью [библиотеки Azure CDN для .NET](https://msdn.microsoft.com/library/mt657769.aspx) можно автоматизировать создание профилей и конечных точек CDN и управление ими. В этом учебнике описывается создание простого консольного приложения .NET, которое демонстрирует некоторые из доступных операций. Данный учебник не содержит подробных сведений о всех аспектах библиотеки Azure CDN для .NET.

Для работы с этим учебником требуется Visual Studio 2015. Вы можете бесплатно скачать выпуск [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx).

Полный пример этого учебника можно найти [здесь](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

## Подготовка

Прежде чем можно будет создавать код управления CDN, необходимо выполнить определенную подготовку. В первую очередь следует создать группу ресурсов для профиля CDN, который мы создадим в этом учебнике. Затем мы настроим Azure Active Directory, чтобы обеспечить аутентификацию нашего приложения. После этого мы применим разрешения для группы ресурсов, чтобы только авторизованные пользователи могли с помощью клиента Azure AD взаимодействовать с профилем CDN.

### Создание группы ресурсов

1. Войдите на [портал Azure](https://portal.azure.com).

2. Нажмите кнопку **Создать** в верхнем левом углу, затем щелкните **Управление** и **Группа ресурсов**.
	
	![Создание новой группы ресурсов](./media/cdn-app-dev-net/cdn-new-rg-1.png)

3. Назовите группу ресурсов *CdnConsoleTutorial*. Выберите свою подписку и ближайшее расположение. При необходимости можно установить флажок **Закрепить на панели мониторинга**, чтобы закрепить группу ресурсов на панели мониторинга на портале. Так ее будет проще найти. Выбрав необходимые параметры, нажмите кнопку **Создать**.

	![Присвоение имени группе ресурсов](./media/cdn-app-dev-net/cdn-new-rg-2.png)

4. Если вы не закрепили созданную группу ресурсов на панели мониторинга, то ее можно найти, щелкнув **Обзор** > **Группы ресурсов**. Щелкните группу ресурсов, чтобы открыть ее. Запишите **идентификатор подписки**. Он понадобится нам позднее.

	 ![Присвоение имени группе ресурсов](./media/cdn-app-dev-net/cdn-subscription-id.png)

### Создание приложения Azure AD

Существует два подхода к аутентификации приложения с помощью Azure Active Directory: использование отдельных пользователей или субъекта-службы. Субъект-служба аналогичен учетной записи службы в Windows. Вместо того, чтобы предоставить разрешения на взаимодействие с профилями CDN определенному пользователю, мы предоставляем их субъекту-службе. Субъекты-службы обычно используются для автоматических, неинтерактивных процессов. Несмотря на то, что в этом учебнике создается интерактивное приложение консоли, мы будем использовать субъект-службу.

Создание субъекта-службы состоит из нескольких шагов, включая создание приложения Azure Active Directory. Для этого воспользуемся [инструкциями данного учебника](../resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Обязательно выполните все шаги [связанного учебника](../resource-group-create-service-principal-portal.md). *Крайне важно* выполнить все в точности, как описано. Обязательно запишите **идентификатор клиента**, **доменное имя клиента** (обычно это домен *.onmicrosoft.com*, если не был выбран личный домен), **идентификатор клиента** и **ключ аутентификации клиента**, так как они понадобятся позже. Следует очень ответственно отнестись к защите **идентификатора клиента** и **ключа аутентификации**, так как с помощью этих учетных данных любой пользователь может выполнять операции в качестве субъекта-службы.
> 	
> На шаге [Настройка мультитенантного приложения](../resource-group-create-service-principal-portal.md#configure-multi-tenant-application) выберите **Нет**.
> 
> На шаге [Назначение роли приложению](../resource-group-create-service-principal-portal.md#assign-application-to-role) укажите группу ресурсов *CdnConsoleTutorial*, созданную ранее, но вместо роли **Читатель** назначьте роль **CDN Profile Contributor** (Участник профиля CDN). Назначив приложению роль **CDN Profile Contributor** (Участник профиля CDN) для группы ресурсов, вернитесь к данному учебнику.

После создания субъекта-службы и назначения роли **CDN Profile Contributor** (Участник профиля CDN) колонка **Пользователи** вашей группы ресурсов должна иметь следующий вид.

![Колонка "Пользователи"](./media/cdn-app-dev-net/cdn-service-principal.png)


### Интерактивная аутентификация пользователей

Если вместо субъекта-службы требуется настроить интерактивную аутентификацию отдельных пользователей, то необходимые действия будут похожи на настройку субъекта-службы. На самом деле потребуется выполнить ту же самую процедуру, но с незначительными изменениями.

>[AZURE.IMPORTANT] Выполните следующие шаги, только если решили использовать аутентификацию отдельных пользователей, а не субъект-службу.

1. При создании приложения вместо **Веб-приложение** выберите **Native application** (Собственное приложение). 
	
	![Собственное приложение](./media/cdn-app-dev-net/cdn-native-application.png)
	
2. На следующей странице вам будет предложено ввести **универсальный код ресурса (URI) перенаправления**. Универсальный код ресурса (URI) не будет проверяться, но запомните, что вы ввели. Он понадобится вам позднее.

3. Нет необходимости создавать **ключ аутентификации клиента**.

4. Вместо того, чтобы назначить роль **CDN Profile Contributor** (Участник роли CDN) участнику-службе, мы назначим ее отдельным пользователям или группам. В этом примере можно видеть, что пользователю *CDN Demo User* назначена роль **CDN Profile Contributor** (Участник роли CDN).
	
	![Индивидуальный доступ пользователей](./media/cdn-app-dev-net/cdn-aad-user.png)


## Создание проекта и добавление пакетов Nuget

Создав группу ресурсов для профилей CDN и назначив приложению Azure AD разрешения для управления профилями CDN и конечными точками в этой группе, мы можем приступить к созданию своего приложения.

В Visual Studio 2015 щелкните **Файл** > **Создать** > **Проект...**, чтобы открыть диалоговое окно нового проекта. Разверните **Visual C#** и выберите **Windows** в области слева. Щелкните **Консольное приложение** в центральной области. Присвойте проекту имя и нажмите кнопку **ОК**.

![Новый проект](./media/cdn-app-dev-net/cdn-new-project.png)

В нашем проекте будут использоваться некоторые библиотеки Azure, содержащиеся в пакетах Nuget. Давайте добавим их в проект.

1. Щелкните **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

	![Управление пакетами NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. В консоли диспетчера пакетов выполните следующую команду, чтобы установить **библиотеку аутентификации Active Directory (ADAL)**.

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Выполните следующую команду для установки **библиотеки управления Azure CDN**.

	`Install-Package Microsoft.Azure.Management.Cdn`

## Директивы, константы, главный метод и вспомогательные методы

Давайте напишем базовую структуру нашей программы.

1. На вкладке Program.cs замените директивы `using`, расположенные вверху, на следующее.

	```
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. Необходимо определить несколько констант, которые будут использоваться нашими методами. В классе `Program` над методом `Main` добавьте следующий код. Обязательно замените заполнители, включая **&lt;угловые скобки&gt;**, своими собственными значениями.

	```
	//Tenant app constants
	private const string clientID = "<YOUR CLIENT ID>";
	private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

	//Application constants
	private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
	private const string profileName = "CdnConsoleApp";
	private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
	private const string resourceGroupName = "CdnConsoleTutorial";
	private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. Кроме того, на уровне класса определите эти две переменные. Мы воспользуемся ими позже, чтобы определить, существует ли профиль или конечная точка.

	```
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  Замените метод `Main` следующим образом.

	```
	static void Main(string[] args)
	{
		//Get a token
		AuthenticationResult authResult = GetAccessToken();

		// Create CDN client
		CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
			{ SubscriptionId = subscriptionId };

		ListProfilesAndEndpoints(cdn);

		// Create CDN Profile
		CreateCdnProfile(cdn);

		// Create CDN Endpoint
		CreateCdnEndpoint(cdn);
		
		Console.WriteLine();

		// Purge CDN Endpoint
		PromptPurgeCdnEndpoint(cdn);

		// Delete CDN Endpoint
		PromptDeleteCdnEndpoint(cdn);

		// Delete CDN Profile
		PromptDeleteCdnProfile(cdn);

		Console.WriteLine("Press Enter to end program.");
		Console.ReadLine();
	}
	```

5. Некоторые из других методов будет запрашивать у пользователя ответ на вопрос в формате "Да/нет". Добавьте следующий метод, чтобы упростить это.

	```
	private static bool PromptUser(string Question)
	{
		Console.Write(Question + " (Y/N): ");
		var response = Console.ReadKey();
		Console.WriteLine();
		if (response.Key == ConsoleKey.Y)
		{
			return true;
		}
		else if (response.Key == ConsoleKey.N)
		{
			return false;
		}
		else
		{
			// They're not pressing Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

Теперь, когда базовая структура программы написана, следует создать методы, вызываемые методом `Main`.

## Аутентификация

Прежде чем можно будет использовать библиотеку управления Azure CDN, необходимо аутентифицировать наш субъект-службу и получить маркер аутентификации. Этот метод использует ADAL для получения маркера.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority); 
	ClientCredential credential = new ClientCredential(clientID, clientSecret);
	AuthenticationResult authResult = 
		authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

	return authResult;
}
```

Если вы используете аутентификацию отдельных пользователей, метод `GetAccessToken` будет выглядеть немного иначе.

>[AZURE.IMPORTANT] Используйте приведенный пример кода, только если решили использовать аутентификацию отдельных пользователей, а не субъект-службу.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority);
	AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
		clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

	return authResult;
}
```

Обязательно замените `<redirect URI>` универсальным кодом ресурса (URI) перенаправления, введенным при регистрации приложения в Azure AD.

## Вывод списка профилей CDN и конечных точек

Теперь все готово к выполнению операций CDN. Первое, что делает наш метод, — выводит список профилей и конечных точек в группе ресурсов и, если он обнаруживает совпадение с именами профиля и конечных точек, указанными в константах, то учитывает это на будущее, чтобы мы не пытались создать повторяющиеся значения.

```
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
	// List all the CDN profiles in this resource group
	var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
	foreach (Profile p in profileList)
	{
		Console.WriteLine("CDN profile {0}", p.Name);
		if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
		{
			// Hey, that's the name of the CDN profile we want to create!
			profileAlreadyExists = true;
		}

		//List all the CDN endpoints on this CDN profile
		Console.WriteLine("Endpoints:");
		var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
		foreach (Endpoint e in endpointList)
		{
			Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
			if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
			{
				// The unique endpoint name already exists.
				endpointAlreadyExists = true;
			}
		}
		Console.WriteLine();
	}
}
```

## Создание профилей CDN и конечных точек

Далее мы создадим профиль.

```
private static void CreateCdnProfile(CdnManagementClient cdn)
{
	if (profileAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating profile {0}.", profileName);
		ProfileCreateParameters profileParms =
			new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
		cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
	}
}
```

После этого мы создадим конечную точку.

```
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
	if (endpointAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
		EndpointCreateParameters endpointParms =
			new EndpointCreateParameters()
			{
				Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
				IsHttpAllowed = true,
				IsHttpsAllowed = true,
				Location = resourceLocation
			};
		cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
	}
}
```

>[AZURE.NOTE] Приведенный выше пример назначает конечной точке источник *Contoso* с именем узла `www.contoso.com`. Это следует изменить, указав собственное имя узла источника.

## Очистка конечной точки

После создания конечной точки одной из распространенных задач, которая может выполняться в нашей программе, является очистка содержимого в конечной точке.

```
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
	if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
	{
		Console.WriteLine("Purging endpoint. Please wait...");
		cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

>[AZURE.NOTE] В приведенном выше примере строка `/*` означает, что я хочу очистить все в корне конечной точки. Аналогичный результат даст установка флажка **Очистить все** в диалоговом окне "Очистить" на портале Azure. В методе `CreateCdnProfile` я создал профиль **Azure CDN from Verizon** с помощью кода `Sku = new Sku(SkuName.StandardVerizon)`, поэтому операция будет выполнена успешно. Однако профили **Azure CDN from Akamai** не поддерживают функцию **Очистить все**, и если бы я использовал для этого учебника профиль Akamai, пришлось бы добавить специальные пути для очистки.

## Удаление профилей CDN и конечных точек

Последние методы, которые мы добавим, удаляют нашу конечную точку и профиль.

```
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
	{
		Console.WriteLine("Deleting endpoint. Please wait...");
		cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
	{
		Console.WriteLine("Deleting profile. Please wait...");
		cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

## Запуск программы

Теперь можно скомпилировать и запустить программу, нажав кнопку **Запустить** в Visual Studio.

![Выполнение программы](./media/cdn-app-dev-net/cdn-program-running-1.png)

Когда программа дойдет до упомянутого выше запроса, вы сможете вернуться к группе ресурсов на портале Azure и увидеть, что профиль создан.

![Готово!](./media/cdn-app-dev-net/cdn-success.png)

Затем мы можем подтвердить запросы, чтобы была выполнена оставшаяся часть программы.

![Завершение программы](./media/cdn-app-dev-net/cdn-program-running-2.png)

## Дальнейшие действия

Чтобы просмотреть полный проект из этого пошагового руководства, [скачайте пример](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Чтобы найти дополнительную документацию по библиотеке управления Azure CDN для .NET, воспользуйтесь [справочником на сайте MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

<!---HONumber=AcomDC_0615_2016-->