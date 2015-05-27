<properties 
	pageTitle="Корпоративные многоканальные приложения" 
	description="Обзор охвата многоканальным приложением локальных ресурсов и облачных программных служб." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# Создание многоканальных приложений для предприятия

## Обзор

Корпоративные многоканальные приложения представляют данные для клиентов, используя множество технологий. Потребители корпоративных веб-приложений, мобильных приложений и приложений API возвращают и обрабатывают информацию из разных источников. Эти источники охватывают внутренние локальные системы, а также облачные службы.

Корпоративные приложения также требуют использовать безопасный доступ, чтобы сотрудники и деловые партнеры подключались к данным с помощью безопасных удостоверений под непосредственным контролем предприятия.

Корпоративные приложения, запущенные в службе приложений Azure (AAS), могут предоставить все эти возможности.

В примере приложения командировок сотрудника ниже показано корпоративное многоканальное приложение, защищенное с помощью службы Azure Active Directory (AAD), интегрированной в локальные ресурсы и службы Software-as-a-Service (SaaS), такие как Office 365 и Salesforce.

## <a name="acceptablefiles"></a>Контроль доступа с помощью Azure Active Directory

Пользователи приложения командировок должны сначала пройти проверку подлинности в корпоративной службе Active Directory. Были предприняты несколько шагов для настройки использования Azure Active Directory (AAD) в приложении.

* Служба Azure Active Directory была создана для предприятия.
* Корпоративная локальная служба Active Directory была включена в федерацию с помощью Azure Active Directory.
* Наконец, приложение было зарегистрировано в AAD с помощью функции простой интеграции AAD службы приложений Azure. 

Конечный результат: приложение просит пользователей войти в AAD (и, в зависимости от расширения, в корпоративную службу AD).
	
![Вход в AAD][AADLogin]

Дополнительные сведения о настройке интеграции службы приложений Azure в AAD см. в разделе [Интеграция службы приложений Azure в AAD][AASIntegrationwithAAD].

## <a name="acceptablefiles"></a>Доступ к локальным ресурсам

После того как пользователь войдет в AAD, он увидит список запланированных корпоративных командировок. Так как веб-приложение выполняется в Microsoft Azure, ему требуется способ доступа к локальному серверу SQL Server, который содержит эти сведения.

![Данные из локального сервера SQL Server][DatafromOnpremisesSqlServer]

Приложение настроено с помощью функции интеграции VNET «точка — сайт». Это позволяет приложению использовать стандартную логику доступа к данным (в этом случае Entity Framework) для прозрачного доступа к удаленному SQL Server, выполняющемуся в корпоративной сети.

Дополнительные сведения об интеграции VNET «точка — сайт» см. в разделе [Интеграция VNET][VNETIntegration].

## <a name="acceptablefiles"></a>Интеграция с Office 365

При каждом создании сотрудником записи о командировке веб-приложение создает запрос на командировку в списке SharePoint Online. Сотрудник может использовать ссылку в приложении для простого доступа к списку Sharepoint:

![Ссылка на список SharePoint][SharepointListLink]

щелкнув ссылку, он автоматически перейдет к списку SharePoint. Так как сотрудники выполнили вход в корпоративную службу AAD, они выполнили прозрачную проверку подлинности в Office 365, используя токен безопасности, выданный AAD.

![Список SharePoint][SharepointList]

Веб-приложение также создает документ командировки в библиотеке документов Sharepoint Online.

![Библиотека документов SharePoint][SharepointDocumentLibrary]

Ресурсы, созданные в SharePoint Online, позволяют веб-приложению использовать возможности Office 365. Например, рабочий процесс решения или утверждения можно активировать при каждом создании элемента в списке Sharepoint.

Дополнительные сведения об интеграции Office 365 см. в разделе [Интеграция Office 365][Office365Integration].

## <a name="acceptablefiles"></a>Интеграция со службами SaaS

Современные компании используют разные службы SaaS, и им нужно взаимодействовать с данными SaaS из других приложений. Приложение путешествия представляет один пример такого сценария. Оно обновляет сведения об учетной записи клиента в Salesforce при каждом планировании командировки сотрудником.

![Интеграция SalesForce][SalesforceIntegration]

Корпоративная учетная запись Salesforce настраивается в AAD, разрешая прозрачную проверку подлинности в Salesforce с использованием учетных данных AAD. В результате, после того как сотрудник войдет в приложение командировок с помощью AAD, приложение сможет читать и записывать данные в Salesforce.

Дополнительные сведения об интеграции SaaS см. в разделе [Интеграция SaaS][SaaSIntegration].

## <a name="NextSteps"></a>Дальнейшие действия

Дополнительные сведения см. в разделе [Службы приложений Azure][AzureApplicationServices].
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: app-service-cloud-app-platform.md
[SaaSIntegration]: app-service-cloud-app-platform.md
[AzureApplicationServices]: app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png
<!--HONumber=54-->