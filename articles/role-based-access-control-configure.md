<properties 
	pageTitle="Контроль доступа на основе ролей на портале предварительной версии Azure" 
	description="Описывает, как работает управление доступом на основе ролей и как его настроить" 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="Ibiza" 
	ms.workload="infrastructure-services" 
	ms.date="02/20/2015" 
	ms.author="justinha"/>

<!--Это базовый шаблон, который показывает, как использовать разметку для создания раздела, в котором содержатся оглавления, разделы с подзаголовками, ссылки на другие разделы сайта azure.microsoft.com, ссылки на другие сайты, нумерованные и маркированные списки, фрагменты кода и образы, а также текст, написанный полужирным шрифтом и курсивом. Чтобы использовать улучшенную разметку, найдите опубликованный раздел и скопируйте нужную разметку или код HTML. Дополнительные сведения об использовании разметка см. http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Управление доступом на основе ролей в предварительной версии портала Azure 

<p> Мы добавили поддержку для управления доступом на основе ролей (RBAC) в портале Azure предварительной версии, чтобы помочь организациям, которые соответствуют их требований управления доступом, просто и точно. <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">В блоге</a> будет позволяют Краткое введение функции и приступить к работе. В данном разделе приведены подробные сведения о ней и описаны дополнительные варианты использования. </p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Управление доступом на основе ролей в Azure
                                                                   
Каждая подписка Azure связана со службой Azure Active Directory. Пользователи и службы, пытающиеся получить доступ к ресурсам подписки через портал управления Azure или API диспетчера ресурсов Azure, сначала должны пройти проверку подлинности в Azure Active Directory.

![][1] 

Управление доступом на основе ролей Azure позволяет предоставить соответствующие права доступа для пользователей, групп и служб, Azure AD путем назначения ролей на подписки или группы ресурсов или на уровне отдельных ресурсов. Назначенная роль определяет уровень доступа пользователя, группы или службы к ресурсу Azure. 

### Роль

Роль - это коллекция действий, которые могут выполняться над ресурсами Azure. Пользователям или службам разрешено выполнять определенное действие с ресурсом Azure, если им назначена роль, содержащая это действие. Список встроенных ролей и **их** действия и **действия не** см [встроенные роли](#builtinroles)(Справочник по командной строке Psconfig).

### Назначение роли

Доступ предоставляется пользователям Azure AD и службам, назначив соответствующие роли на ресурсе Azure. 

#### Субъекты безопасности Azure AD

Роли можно назначать субъектам безопасности Azure AD описанных ниже типов.

+ **Пользователи**: роли могут быть назначены пользователи организации, которые находятся в Azure AD, с которым связана подписка Azure. Роли можно также назначать пользователям внешних учетных записей Майкрософт (например, joe@outlook.com), используя действие "Пригласить" для назначения пользователя на роль на портале предварительной версии Azure. При этом в Azure AD для такого пользователя создается учетная запись гостя. Если эта учетная запись гостя отключена в каталоге, внешнего пользователя не будет разрешен доступ к любому ресурсу Azure, пользователю предоставлен доступ к.
+ **Группы**: роли могут быть назначены группам безопасности Azure AD. Пользователю автоматически предоставляется доступ к ресурсу, когда он становится членом группы, у которой есть доступ к этому ресурсу. Аналогично после удаления из группы пользователь автоматически теряет доступ к ресурсу. Управлять доступом рекомендуется путем назначения ролей группам и добавления пользователей в эти группы, а не путем назначения ролей отдельным пользователям. Azure RBAC не разрешает назначение ролей в списки рассылки.
	Способность назначать роли группам позволяет организации расширять ее существующую модель контроля доступа от своего локального каталога к облаку, и группы прав доступа, уже настроенные для управления доступом к локальным ресурсам, могут быть использованы и для контроля доступа к ресурсам на портале предварительной версии Azure. Дополнительные сведения о различных параметрах для синхронизации пользователей и групп из локального каталога в разделе [Интеграция каталогов](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium также предлагает [делегировать функции группы управления](http://msdn.microsoft.com/library/azure/dn641267.aspx) с возможность создания и настройки групп могут быть переданы пользователям без прав администратора из Azure AD.
+ **Участники службы**: удостоверения службы представлены как участники службы в каталоге. Они проходят проверку подлинности в Azure AD и безопасно взаимодействуют друг с другом. Службе можно предоставить доступ к ресурсам Azure путем назначения ролей субъекту-службе Azure AD, представляющему данную службу, с помощью модуля Azure для Windows PowerShell. 

#### Область ресурсов

Доступ необязательно должны быть предоставлены для всей подписки. Роли также можно назначать для отдельных ресурсов или их групп. В RBAC Azure дочерний ресурс наследует назначения ролей от родительского. По этой причине, если пользователю, группе или службе предоставлен доступ только к определенной группе ресурсов в подписке, то доступ будет разрешен только к ней и входящим в нее ресурсам, но не к другим группам ресурсов в подписке. Возможна также ситуация, когда группе безопасности назначена роль "Читатель" для группы ресурсов и роль "Участник" для базы данных в этой группе ресурсов.

![][2]

## Сосуществование RBAC с соадминистраторы подписки

Администратор и соадминистраторы подписки будут по-прежнему иметь полный доступ к порталам и API управления Azure. В модели RBAC им назначается роль владельца на уровне подписки.  
При этом новая модель RBAC поддерживается только порталом предварительной версии Azure и API диспетчера ресурсов Azure. Пользователи и службы, которым назначены роли RBAC, не могут получать доступ к порталу управления Azure и интерфейсам API управления службами Azure. Пользователь, которому назначена роль "Владелец" для подписки на портале предварительной версии Azure, не становится соадминистратором подписки на полнофункциональном портале Azure.

Если нужно дать пользователю доступ к ресурсу Azure, недоступному для управления через портал предварительной версии Azure, то этого пользователя надо добавить в соадминистраторы подписки, используя портал полной версии Azure. Примерами ресурсов, которыми на сегодняшний день невозможно управлять с помощью RBAC, являются служебная шина и облачные службы.

## Предоставление прав на управление и операции с данными

Управление доступом на основе ролей поддерживается только для операций управления ресурсов Azure в предварительной версии портала Azure и API-интерфейсы диспетчера ресурсов Azure. С помощью RBAC можно разрешить доступ не ко всем операциям на уровне данных для ресурсов Azure. Например, с помощью RBAC можно управлять разрешениями на создание, чтение, обновление и удаление учетных записей хранения. Однако на сегодняшний день невозможно применять RBAC для управления разрешениями на создание, чтение, обновление и удаление BLOB-объектов или таблиц в учетной записи хранения. Аналогично с помощью RBAC можно управлять разрешениями на создание, чтение, обновление и удаление базы данных SQL, но не разрешениями на создание, чтение, обновление и удаление таблиц SQL в базе данных.

## Предоставление и запрет доступа

Рассмотрим пример того, как владелец ресурса в организации может управлять доступом. В этом сценарии имеется множество сотрудников, занятых в различных тестовых и рабочих проектах, которые выполняются с помощью ресурсов Azure. Вы намереваетесь следовать рекомендуемым правилам управления доступом. Пользователи должны иметь доступ ко всем необходимым им ресурсам, но доступ к остальным ресурсам должен быть закрыт. Вы хотите воспользоваться всеми процессами и средствами, в которые уже были сделаны инвестиции, и применить группы безопасности, настроенные в локальной службе каталогов Active Directory. Настройка доступа к ресурсам описана в следующих разделах:

* [Добавление доступа(#add)
* [Удалить доступ(#remove)
* [Добавление или удаление доступа для внешних пользователей](#addremoveext)

<h3><a id="add"></a>Добавление доступа</h3>

В таблице ниже перечислены требуемые права доступа и указаны способы их настройки в Azure.

Пользователь/группа  | Требование доступа  | Роль и область для доступа	
------------- | -------------  | ------------
Вся команда Джилл Сантос  | Чтение всех ресурсов Azure  | Добавить группу AD, представляющую команду Джилл Сантос, в роль читателя для подписки Azure
Вся команда Джилл Сантос  | Создание и управление всеми ресурсами группы ресурсов Test | Добавить группу AD, представляющую команду Джилл Сантос, в роль участника для группы ресурсов Test
Брок | Создание и управление всеми ресурсами группы ресурсов Prod | Добавить Брока в роль участника для группы ресурсов Prod


Сначала добавим доступ для чтения для всех ресурсов подписки. Щелкните **Обзор > все > подписки**.

![][3] 

Щелкните *name of your subscription*** > чтения > Добавить**. В списке пользователей и групп выберите или введите имя группы Active Directory.

![][4]

Затем добавьте командных роль участника в тестовой группе ресурсов. Щелкните группу ресурсов, чтобы открыть колонку с ее свойствами. В разделе **ролей**, щелкните **Участник > Добавить** и введите имя рабочей группы.

![][5]

Чтобы добавить роль участника группы ресурсов Prod Brock, нажать кнопку группы ресурсов **Участник > Добавить** и введите имя Brock элемента. 

![][6]

Назначения ролей можно также управлять с помощью модуля Microsoft Azure для Windows PowerShell. Вот пример добавления учетной записи Павла не через портал, а с помощью командлета New-AzureRoleAssignment:

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Дополнительные сведения об использовании Windows PowerShell для добавления и удаления доступа см. в разделе [управление доступом на основе ролей с помощью Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/). 

<h3><a id="remove"></a>Удалить доступ</h3>

Также можно легко удалить назначения. Допустим, нужно удалить пользователя по имени Брэд Адамс из роли читателя для группы ресурсов под названием TestDB. Откройте выноска группы ресурсов, щелкните **чтения > Adams Брэд > Удалить**.

![][7]

В этом примере показано, как удалить Артема Кузнецова с помощью командлета Remove-AzureRoleAssignment:

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>Добавление или удаление доступа для внешних пользователей</h3>

**Настройка** каталога включает параметры для управления доступом для внешних пользователей. Эти параметры можно менять только в интерфейсе пользователя (методы Windows PowerShell или API для этого отсутствуют) на портале полной версии Azure глобальным администратором каталога. 
Открытие **Настройка** в полный портал Azure, щелкните **Active Directory**, а затем щелкните имя каталога.

![][10]

Затем можно изменить параметры для управления доступом для внешних пользователей. 

![][8]

По умолчанию, Гости не удалось перечислить содержимое каталога, поэтому они не видят все пользователи и группы в **список членов**. Они могут найти пользователя, введя его полный адрес электронной почты, а затем предоставить ему доступ. По умолчанию для гостей действуют следующие ограничения:

- они не могут получить перечень пользователей и групп в каталоге;
- они могут просматривать ограниченные сведения о пользователе, если знают его адрес электронной почты;
- они могут просматривать ограниченные сведения о группе, если знают ее имя.

Возможность гостям ограниченные сведения пользователя или группы в разделе позволяет пригласить других людей и просматривать некоторые сведения совместная работа с которыми они.  

Воспроизведем процедуру добавления доступа для внешнего пользователя. Мы добавим внешнего пользователя к той же роли чтения для группы ресурсов TestDB так, пользователь может помочь в отладке ошибки. Откройте выноска группы ресурсов, щелкните **чтения > Добавить > пригласить** и введите адрес электронной почты пользователя, который требуется добавить. 

![][9]

При добавлении внешних пользователей гостя создается в каталоге. Затем этого гостя можно добавить в группу или удалить из нее, а также добавить в отдельную роль и удалить из нее так же, как любого другого пользователя в каталоге. 

Можно также удалить гостя из любой роли, как можно удалить любой пользователь. При удалении из роли для конкретного ресурса гость не будет удален из каталога. 
 
## Известные проблемы при использовании управления доступом на основе ролей

В случае возникновения проблем при использовании функции управления доступом на основе ролей при при ее в режиме предварительного просмотра см. в разделе [Устранение неполадок управления доступом на основе ролей](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/) известных проблем, которые могут быть связаны с проблемой.


## Встроенные роли

Управление доступом на основе ролей Azure поставляется с следующие встроенные роли, которые могут быть назначены пользователей, групп и служб. Изменить определение встроенных ролей нельзя. В предстоящем выпуске Azure RBAC можно определить пользовательские роли, создав набор действий из списка доступных действий, которые могут выполняться над ресурсами Azure.

Щелкните соответствующую ссылку в разделе **действия** и **действия не** свойства определения роли. **Действия** свойство задает разрешенные действия на ресурсы Azure. В строках действий можно использовать подстановочные знаки. **Действия не** определение роли определяет действия, которые должны быть исключены из разрешенные действия. 


Имя роли | Описание  	
------------- | -------------  
[Участник службы управления API](#APIMgmt) | Позволяет управлять службой управления API, но не к ним доступ.
[Участник компонент приложения дополнительная информация](#AppInsights) | Позволяет управлять компонентами Application Insights, но не к ним доступ.
[Участник BizTalk(#BizTalk) | Позволяет управлять службы BizTalk, но не к ним доступ.
[Участник ClearDB MySQL DB](#ClearDB) | Позволяет управлять баз данных ClearDB MySQL, но не к ним доступ.
[Участник(#Contributor) | Участники могут управлять все, кроме доступа.
[Участник фабрика данных](#DataFactory) | Позволяет управлять фабрики данных, но не к ним доступ.
[Учетная запись участника DB документа](#DocDBContrib) | Позволяет управлять использования documentdb по плану счетов, но не к ним доступ.
[Учетная запись участника интеллектуальных систем](#IntelliSysContrib) | Позволяет управлять учетными записями интеллектуальных систем, но не к ним доступ.
[Учетная запись участника NewRelic APM](#NewRelicContrib) | Позволяет управлять учетными записями управления производительностью приложений New Relic и приложения, но не к ним доступ.
[Владелец(#Owner) | Владелец может управлять все, включая доступ.
[Модуль чтения(#Reader) | Читатели можно просмотреть все элементы, но не могут вносить изменения.
[Участник кэша redis](#Redis) | Позволяет управлять кэша Redis, но не к ним доступ.
[Участник БД SQL](#SQLDBContrib) | Позволяет управлять базами данных SQL, но не к ним доступ. Кроме того нельзя управлять их политики безопасности или свои серверы родительского SQL Server.
[Диспетчер безопасности SQL](#SQLSecMgr) | Позволяет управлять политиками безопасности серверов SQL Server и баз данных, но не к ним доступ.
[Участник сервера SQL](#SQLSrvContrib) | Позволяет управлять серверами SQL Server и баз данных, но не доступ к ним и не свои политики безопасности.
[Участник коллекции заданий планировщика](#SchedContrib) | Позволяет управлять коллекций заданий планировщика, но не к ним доступ.
[Участник службы поиска](#SearchContrib) | Позволяет управлять службы поиска, но не к ним доступ.
[Участник учетной записи хранилища](#StorageContrib) | Позволяет управлять учетными записями хранения, но не к ним доступ.
[Администратор доступа пользователя](#UserAccessAdmin) | Позволяет управлять доступом пользователей к ресурсам Azure.
[Участник виртуальной машины](#VMContrib) | Позволяет управлять виртуальных машин, но не для доступа к ним и не виртуальной сети или учетной записи хранилища, в которой они подключены.
[Участник виртуальной сети](#VNetContrib) | Позволяет управлять виртуальными сетями, но не к ним доступ.
[Участник плана Web](#WebPlanContrib) | Позволяет управлять планы веб-сайтов для веб-сайтов, но не для доступа к ним.
[Участник веб-сайта](#WebsiteContrib) | Позволяет управлять веб-сайтов (не планы веб-сайтов), но не к ним доступ.


<h3><a id="APIMgmt"></a>Участник службы управления API</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Создание и управление API управления службами</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="AppInsights"></a>Участник компонент приложения дополнительная информация</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Создание и управление компонентами дополнительная информация</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Создание и управление веб-тестов</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="BizTalk"></a>Участник BizTalk</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>Создание и управление ими служб BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="ClearDB"></a>Участник ClearDB MySQL DB</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>Создание и управление базами данных ClearDB MySQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="Contributor"></a>Участник</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>*</td>
<td>Создание и управление ресурсами для всех типов</td>
</tr>
<tr>
<th colspan="2">Запрещенные действия</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Не удается создать роли и назначения ролей </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Не удается удалить роли и назначения ролей</td>
</tr>
</table>

<h3><a id="DataFactory"></a>Участник фабрика данных</h3>

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Создание и управление ими данных фабрики</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="DocDBContrib"></a>Учетная запись участника DB документа</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Создание и управление учетными записями использования documentdb по плану</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="IntelliSysContrib"></a>Учетная запись участника интеллектуальных систем</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Создание и управление учетными записями интеллектуальных систем</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="NewRelicContrib"></a>Учетная запись участника NewRelic APM</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>Создание и управление учетными записями для управления производительностью приложений NewRelic</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="Owner"></a>Владелец</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>*</td>
<td>Создание и управление ресурсами для всех типов</td>
</tr>
</table>

<h3><a id="Reader"></a>Модуль чтения</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>* / чтение</td>
<td>Чтение ресурсов всех типов. Не удается прочитать хотя секретные данные.</td>
</tr>
</table>

<h3><a id="Redis"></a>Участник кэша redis</h3>

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Создание и управление кэш Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="SQLDBContrib"></a>Участник БД SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Чтение серверов SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>Создание и управление базами данных SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
<tr>
<th colspan="2">Запрещенные действия</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Нельзя управлять политики аудита базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Нельзя управлять политиками подключения базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Невозможно управлять SQL базы данных маскирование политики</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Не удается управлять метрики безопасности базы данных SQL</td>
</tr>
</table>

<h3><a id="SQLSecMgr"></a>Диспетчер безопасности SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Чтение серверов SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Создание и управление политиками аудита SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>Чтение базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Создание и управление политиками аудита базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Создание и управление политиками подключений базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Создание и управление данными в базе данных SQL маскирование политики</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Создание и управление ими метрики безопасности базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="SQLSrvContrib"></a>Участник сервера SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Создание и управление API управления службами</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
<tr>
<th colspan="2">Запрещенные действия</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Невозможно управлять SQL Server политики аудита</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Нельзя управлять политики аудита базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Нельзя управлять политиками подключения базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Невозможно управлять SQL базы данных маскирование политики</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Не удается управлять метрики безопасности базы данных SQL</td>
</tr>
</table>

<h3><a id="SchedContrib"></a>Участник коллекции заданий планировщика</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Создание и управление коллекциями планировщика заданий</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="SearchContrib"></a>Участник службы поиска</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Создание и управление ими служб поиска</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="StorageContrib"></a>Участник учетной записи хранилища</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>Создание и управление учетными записями хранения</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="UserAccessAdmin"></a>Администратор доступа пользователя</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>* / чтение</td>
<td>Чтение ресурсов всех типов</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Создание и управление ими ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="VMContrib"></a>Участник виртуальной машины</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Чтение учетных записей хранилища</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Получить ключи учетной записи хранения</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>Чтение виртуальных сетей</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>Присоединение виртуальных сетей</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>Чтение зарезервированные IP-адреса</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>Ссылка на зарезервированные IP-адреса</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>Создание и управление облачными службами</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>Создание и управление виртуальными машинами</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="VNetContrib"></a>Участник виртуальной сети</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>Создание и управление виртуальными сетями</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="WebPlanContrib"></a>Участник плана Web</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Создание и управление ими планы веб-сайтов</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

<h3><a id="WebsiteContrib"></a>Участник веб-сайта</h3>

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Планы веб-сайтов чтения</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Присоединение планы веб-сайтов</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Создание и управление веб-сайтами</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Создание и управление сертификатами веб-сайта</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначений ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение групп ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и Управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>


## Предоставление отзыва

Повторите попытку Azure RBAC и отправьте нам [отзывы](http://aka.ms/azurerbacfeedback). 


## Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы, помогающие использовать управление доступом на основе ролей. 

+ [Управление доступом на основе ролей с помощью Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
+ [Управление доступом на основе ролей XPLAT CLI](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
+ [Устранение неполадок управления доступом на основе ролей](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium и Basic](http://msdn.microsoft.com/library/azure/dn532272.aspx)
+ [Как подписки Azure связаны с Azure AD](http://msdn.microsoft.com/library/azure/dn629581.aspx)
+ Введение в Управление группами самообслуживания для групп безопасности см. в разделе [блог группы Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png



<!--HONumber=47-->
