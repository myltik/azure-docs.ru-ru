<properties
	pageTitle="Управление доступом на основе ролей на портале Microsoft Azure"
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
	ms.date="06/29/2015"
	ms.author="justinha"/>

# Управление доступом на основе ролей на портале Microsoft Azure

На портал предварительной версии Microsoft Azure добавлена дополнительная поддержка управления доступом на основе ролей (RBAC), чтобы помочь организациям просто и точно выполнять требования к управлению доступом. В [этой записи блога](http://go.microsoft.com/fwlink/?LinkId=511576) вы найдете краткую вводную информацию, которая поможет вам приступить к использованию этой функции. В данном разделе приведены подробные сведения о ней и описаны дополнительные варианты использования.


## Управление доступом на основе ролей в Azure

Каждая подписка Azure связана с Azure Active Directory. Пользователи и службы, пытающиеся получить доступ к ресурсам подписки через портал управления Microsoft Azure или API диспетчера ресурсов Azure, сначала должны пройти аутентификацию в Azure Active Directory.

![][1]

Функция управления доступом на основе ролей для Azure позволяет предоставлять права доступа для пользователей Azure AD, групп и служб, назначая им роли в подписке, группе ресурсов или на уровне индивидуальных ресурсов. Назначенная роль определяет уровень доступа пользователя, группы или службы к ресурсу Azure.

### Роль

Роль — это набор действий, которые можно выполнять с ресурсами Azure. Пользователям или службам разрешено выполнять определенное действие с ресурсом Azure, если им назначена роль, содержащая это действие. Список встроенных ролей, а также их свойства **actions** и **not actions** см. в разделе [Встроенные роли](#builtinroles).

### Назначение роли

Доступ предоставляется пользователям и службам Azure AD путем назначения им соответствующей роли для ресурса Azure.

#### Субъекты безопасности Azure AD

Роли можно назначать субъектам безопасности Azure AD описанных ниже типов.

+ **Пользователи**: роли можно назначать пользователям организации, включенным в каталог Azure AD, с которым связана подписка Azure. Роли можно также назначать пользователям с внешними учетными записями Майкрософт (например, joe@outlook.com) с помощью действия приглашения на портале Azure. При этом в Azure AD для такого пользователя создается учетная запись гостя. Если эта гостевая учетная запись отключена в каталоге, то внешний пользователь не сможет реализовать свое право на доступ к какому-либо из ресурсов Azure.
+ **Группы**: роли можно назначать группам безопасности Azure AD. Пользователю автоматически предоставляется доступ к ресурсу, когда он становится членом группы, у которой есть доступ к этому ресурсу. Аналогично после удаления из группы пользователь автоматически теряет доступ к ресурсу. Управлять доступом рекомендуется путем назначения ролей группам и добавления пользователей в эти группы, а не путем назначения ролей отдельным пользователям. Управление доступом на основе ролей для Azure RBAC не позволяет назначать роли для списков распределения. Способность назначать роли группам позволяет организации расширять ее существующую модель контроля доступа от своего локального каталога к облаку, и группы безопасности, уже настроенные для управления доступом к локальным ресурсам, могут быть использованы и для контроля доступа к ресурсам на портале Azure. Дополнительные сведения о различных вариантах синхронизации пользователей и групп из локального каталога см. в статье [Общие сведения об интеграции каталогов](http://technet.microsoft.com/library/jj573653.aspx). В службе Azure AD Premium также предусмотрена [функция делегированного управления группами](http://msdn.microsoft.com/library/azure/dn641267.aspx), благодаря которой право на создание групп и управление ими можно делегировать обычным пользователям (не администраторам) из Azure AD.
+ **Субъекты-службы**: удостоверения службы представлены как субъекты-службы в каталоге. Они проходят проверку подлинности в Azure AD и безопасно взаимодействуют друг с другом. Службе можно предоставить доступ к ресурсам Azure путем назначения ролей субъекту-службе Azure AD, представляющему данную службу, с помощью модуля Azure для Windows PowerShell.

#### Область ресурсов

Доступ не обязательно предоставлять ко всей подписке. Роли также можно назначать для отдельных ресурсов или их групп. В RBAC Azure дочерний ресурс наследует назначения ролей от родительского. По этой причине, если пользователю, группе или службе предоставлен доступ только к определенной группе ресурсов в подписке, то доступ будет разрешен только к ней и входящим в нее ресурсам, но не к другим группам ресурсов в подписке. Возможна также ситуация, когда группе безопасности назначена роль "Читатель" для группы ресурсов и роль "Участник" для базы данных в этой группе ресурсов.

![][2]

## Сосуществование управления доступом на основе ролей и соадминистраторов подписки

Администратор и соадминистраторы подписки будут по-прежнему иметь полный доступ к порталам и API управления Azure. В модели RBAC им назначается роль владельца на уровне подписки. При этом новая модель RBAC поддерживается только порталом Azure и API диспетчера ресурсов Azure. Пользователи и службы, которым назначены роли RBAC, не могут получать доступ к порталу управления Azure и интерфейсам API управления службами Azure. Пользователь, которому назначена роль «Владелец» для подписки на портале Azure, не становится соадминистратором подписки на полнофункциональном портале Azure.

Если нужно дать пользователю доступ к ресурсу Azure, недоступному для управления через портал Azure, то этого пользователя надо добавить в соадминистраторы подписки, используя портал управления Azure. Примерами ресурсов, которыми на сегодняшний день невозможно управлять с помощью RBAC, являются служебная шина и облачные службы.

## Предоставление прав на управление и операции с данными

Управление доступом на основе ролей поддерживается только для операций управления ресурсами Azure на портале Azure и в API диспетчера ресурсов Azure. С помощью RBAC можно разрешить доступ не ко всем операциям на уровне данных для ресурсов Azure. Например, с помощью RBAC можно управлять разрешениями на создание, чтение, обновление и удаление учетных записей хранения. Однако на сегодняшний день невозможно применять RBAC для управления разрешениями на создание, чтение, обновление и удаление BLOB-объектов или таблиц в учетной записи хранения. Аналогично с помощью RBAC можно управлять разрешениями на создание, чтение, обновление и удаление базы данных SQL, но не разрешениями на создание, чтение, обновление и удаление таблиц SQL в базе данных.

## Предоставление и запрет доступа

Рассмотрим пример того, как владелец ресурса в организации может управлять доступом. В этом сценарии имеется множество сотрудников, занятых в различных тестовых и рабочих проектах, которые выполняются с помощью ресурсов Azure. Вы намереваетесь следовать рекомендуемым правилам управления доступом. Пользователи должны иметь доступ ко всем необходимым им ресурсам, но доступ к остальным ресурсам должен быть закрыт. Вы хотите воспользоваться всеми процессами и средствами, в которые уже были сделаны инвестиции, и применить группы безопасности, настроенные в локальной службе каталогов Active Directory. Настройка доступа к ресурсам описана в следующих разделах:

* [Предоставление доступа](#add-access)
* [Запрет доступа](#remove-access)
* [Предоставление или запрет доступа для внешнего пользователя](#add-or-remove-access-for-external-user)

### Предоставление доступа

В таблице ниже перечислены требуемые права доступа и указаны способы их настройки в Azure.

Пользователь или группа | Требуемые права доступа | Роль и область доступа
------------- | -------------  | ------------
Все члены рабочей группы Ольги Зуевой | Чтение всех ресурсов Azure | Добавьте группу Active Directory, представляющую рабочую группу Ольги Зуевой, в роль "Читатель" на уровне подписки Azure.
Все члены рабочей группы Ольги Зуевой | Создание всех ресурсов в группе ресурсов "Тест" и управление ими | Добавьте группу Active Directory, представляющую рабочую группу Ольги Зуевой, в роль "Участник" для группы ресурсов "Тест".
Павел | Создание всех ресурсов в группе ресурсов "Производство" и управление ими | Добавьте Павла в роль "Участник" для группы ресурсов "Производство".


Сначала добавим доступ на чтение ко всем ресурсам подписки. Последовательно выберите пункты **Browse > Everything > Subscriptions** (Обзор > Все > Подписки).

![][3]

Последовательно щелкните *имя подписки*\*\* > Reader (Читатель) > Add (Добавить)\*\*. В списке пользователей и групп выберите или введите имя группы Active Directory.

![][4]

Затем добавьте ту же самую команду в роль участника группы ресурсов Test. Щелкните группу ресурсов, чтобы открыть колонку с ее свойствами. В разделе **Roles** (Роли) нажмите **Contributor > Add** (Участник > Добавить) и введите имя рабочей группы.

![][5]

Чтобы добавить Павла в роль "Участник" группы ресурсов "Производство", выберите эту группу ресурсов, нажмите **Contributor > Add** и введите имя сотрудника.

![][6]

Назначение ролей можно также выполнять с помощью модуля Microsoft Azure для Windows PowerShell. Вот пример добавления учетной записи Павла не через портал, а с помощью командлета New-AzureRoleAssignment:

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Дополнительные сведения об использовании Windows PowerShell для предоставления и запрета доступа см. в разделе [Управление доступом на основе ролей с помощью Windows PowerShell](role-based-access-control-powershell.md).

### Запрет доступа

Назначения также можно с легкостью удалять. Предположим, что нужно удалить пользователя по имени Артем Кузнецов из роли "Читатель" для группы ресурсов с именем "Тестовая\_БД". Откройте колонку группы ресурсов и последовательно щелкните **Reader > Артем Кузнецов > Remove** (Читатель > Артем Кузнецов > Удалить).

![][7]

В этом примере показано, как удалить Брэда Адамса с помощью командлета Remove-AzureRoleAssignment:

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### Предоставление или запрет доступа для внешнего пользователя

На вкладке **Configure** (Настройка) каталога содержатся параметры для управления доступом внешних пользователей. Эти параметры можно менять только в интерфейсе пользователя (методы Windows PowerShell или API для этого отсутствуют) на портале полной версии Azure глобальным администратором каталога. Чтобы открыть вкладку **Конфигурация** на портале Azure, щелкните **Active Directory**, а затем щелкните имя каталога.

![][10]

Теперь можно изменить параметры контроля доступа для внешних пользователей.

![][8]

По умолчанию гости не могут получить перечень содержимого каталога, поэтому они не видят пользователей и группы в **списке членов**. Они могут найти пользователя, введя его полный адрес электронной почты, а затем предоставить ему доступ. По умолчанию для гостей действуют следующие ограничения:

- они не могут получить перечень пользователей и групп в каталоге;
- они могут просматривать ограниченные сведения о пользователе, если знают его адрес электронной почты;
- они могут просматривать ограниченные сведения о группе, если знают ее имя.

Возможность просмотра гостями ограниченных сведений о пользователях и группах позволяет им приглашать других людей к совместной работе и узнавать о них некоторые подробности.

Рассмотрим процесс предоставления доступа внешнему пользователю. Мы добавим внешнего пользователя в роль "Читатель" группы ресурсов "Тестовая\_БД", чтобы он мог помочь выполнить отладку для поиска ошибки. Откройте колонку группы ресурсов, щелкните **Reader > Add > Invite** (Читатель > Добавить > Пригласить) и введите адрес электронной почты пользователя, которого нужно добавить.

![][9]

При добавлении внешнего пользователя в каталоге создается гостевая учетная запись. Затем этого гостя можно добавить в группу или удалить из нее, а также добавить в отдельную роль и удалить из нее так же, как любого другого пользователя в каталоге.

Вы также можете удалить гостя из любой роли, как всякого другого пользователя. При удалении из роли для конкретного ресурса гость не будет удален из каталога.

## Как отслеживать изменения назначений ролей

Изменения назначений ролей регистрируются в [журналах аудита](http://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) вместе с остальными событиями. Вы можете получить журнал изменений назначений ролей с помощью [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) или [REST API диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx).

Например, чтобы получить список изменений назначений ролей для всей подписки, выполните следующие два командлета с помощью Azure PowerShell. Первый из них выполняет переключение в режим диспетчера ресурсов Azure.

`Switch-AzureMode -name AzureResourceManager`

`Get-AzureSubscriptionIdLog –DetailedOutput -StartTime '06-15-15' -EndTime '06-29-15'`

Изменения назначений ролей записываются в событиях, где значение параметра ResourceProviderName — `Microsoft.Authorization`. Фактические данные о назначении содержатся в подробной информации о событии (какой субъект был присвоен, для какой роли и области применения). Изменения назначений ролей отображаются при просмотре журналов аудита на портале, но на нем не отображается информация о событии. Чтобы просмотреть подробную информацию о событии, используйте Azure PowerShell.

###Информация о событии

Ниже приведен пример подробной информации о событии изменения назначения роли:

```
Authorization        :
                       Scope     : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-W
                       estUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authoriza
                       tion/roleAssignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
                       Action    : Microsoft.Authorization/roleAssignments/write
                       Role      : Subscription Admin
                       Condition :
Caller               : William.Hennum@contoso.com
Claims               :
                       aud            : https://management.core.windows.net/
                       iss            : https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/
                       iat            : 1435333533
                       nbf            : 1435333533
                       exp            : 1435337433
                       ver            : 1.0
                       http://schemas.microsoft.com/identity/claims/tenantid: 72f988bf-86f1-41af-91ab-2d7cd011db47
                       http://schemas.microsoft.com/identity/claims/objectidentifier:
                       dda50086-5e3d-4a4b-b8bc-f54771104d89
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn: William.Hennum@contoso.com
                       puid           : 10030000803CDC0B
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier:
                       MJwntjqWaULfl30NJMiDRVSVCWMX5GzmMNU4oqitDXs
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname: William
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname: Hennum
                       name           : William Hennum
                       http://schemas.microsoft.com/claims/authnmethodsreferences: rsa,wia,mfa
                       _claim_names   : {"groups":"src1"}
                       _claim_sources : {"src1":{"endpoint":"https://graph.windows.net/72f988bf-86f1-41af-91ab-2d7cd011
                       db47/users/dda50086-5e3d-4a4b-b8bc-f54771104d89/getMemberObjects"}}
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name: William.Hennum@contoso.com
                       onprem_sid     : S-1-5-21-1721254763-462695806-1538882281-3175325
                       appid          : c44b4083-3bb0-49c1-b47d-974e53cbdf3c
                       appidacr       : 2
                       http://schemas.microsoft.com/identity/claims/scope: user_impersonation
                       http://schemas.microsoft.com/claims/authnclassreference: 1
CorrelationId        : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
Description          :
EventChannels        : Operation
EventDataId          : ed8e79b6-c7d1-4332-adcf-70d37546c5a6
EventName            : BeginRequest
EventSource          : Administrative
EventTimestamp       : 6/26/2015 3:53:34 PM
HttpRequest          :
                       ClientId        : F7272386-295A-4545-96BD-21F0856A43FE
                       Method          : PUT
                       Url             :
                       ClientIpAddress : 23.99.81.159
Id                   : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78/events/ed8e79b6-c7d1-4332-adcf-70d37546c5a6/ticks/
                       635709308140011864
Level                : Informational
OperationId          : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
OperationName        : Microsoft.Authorization/roleAssignments/write
Properties           :
                       requestbody    : {"Id":"531f036a-37ff-40c1-9bb9-aa580ebe7e78","Properties":{"PrincipalId":"dda50
                       086-5e3d-4a4b-b8bc-f54771104d89","RoleDefinitionId":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5
                       fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7"
                       ,"Scope":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-Wes
                       tUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes"}}
ResourceGroupName    : Default-Storage-WestUS
ResourceProviderName : Microsoft.Authorization
ResourceId           : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
Status               : Started
SubmissionTimestamp  : 6/26/2015 3:53:50 PM
SubscriptionId       : ff945b8d-441a-41ef-a9db-7bd5fcc99978
SubStatus            :`
```

Информация в событии интерпретируется следующим образом:

| Поле | Значение | Сведения |
| --- | --- | --- |
| Caller |	`William.Hennum@contoso.com` | Субъект, совершивший назначение ролей. Субъектом может быть пользователь, группа или субъект-служба.
| HttpRequest: Method | `PUT` | Выполненное действие. PUT присваивает назначение, а DELETE удаляет его. |
| Properties: PrincipalId | `dda50086-5e3d-4a4b-b8bc-f54771104d89` | 	Идентификатор объекта для субъекта, который был назначен для роли. Субъектом может быть пользователь, группа или субъект-служба. Вы можете определить имя и тип субъекта, выполнив их поиск в Azure Active Directory с помощью Azure PowerShell. |
| Properties: RoleDefinitionId |	`/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7	` | Назначенная роль. Вы можете определить отображаемое имя роли с помощью Azure PowerShell. |
| Properties: Scope | `/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes` |	Ресурс, в котором было выполнено назначение роли. Это может быть ресурс, группа ресурсов или подписка. |

###Примеры фрагментов кода PowerShell

Для начала рассмотрим пример кода Azure PowerShell для сопоставления PrincipalId с именем и типом:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
```

Ниже также приведен пример кода Azure PowerShell для сопоставления Scope с именем и типом ресурса:

```
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
```
Следующий пример кода Azure PowerShell приведен для сопоставления RoleDefinitionId с отображаемым именем роли:

```

# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}

```

###Пример сценария Azure PowerShell

Чтобы соединить все это вместе, ниже приведен пример сценария, который получает события назначений ролей для указанного диапазона дат и выводит их в виде таблицы:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}
# Sample - output the list of role assignment events
function Get-AzureRBACAuditLog($startDateTime, $endDateTime)
{
    $log = Get-AzureSubscriptionIdLog -DetailedOutput -StartTime $startDateTime -EndTime $endDateTime
    $log = $log | ? { $_.ResourceProviderName -ieq "Microsoft.Authorization" }
    $startEvents = $log | ? { $_.httpRequest -and $_.Status -ieq "Started" }
    $endEvents = @{}
    $log | ? { $_.httpRequest -and $_.Status -ne "Started" } | % { $endEvents[$_.OperationId] = $_ }

    $startEvents | ? { $endEvents.ContainsKey($_.OperationId) } | % {
        $endEvent = $endEvents[$_.OperationId];
        $out = "" | select Timestamp, Caller, Action, PrincipalId, PrincipalName, PrincipalType, RoleName, Scope, ScopeName, ScopeType, RoleDefinitionId
        $out.Timestamp = $endEvent.EventTimestamp
        $out.Caller = $_.Caller
        if ($_.HttpRequest.Method -ieq "PUT") {
            $out.Action = "Granted"
            if ($_.Properties.Content.ContainsKey("requestbody")) {
                $messageBody = ConvertFrom-Json $_.Properties.Content["requestbody"]
            }
        }
        elseif ($_.HttpRequest.Method -ieq "DELETE") {
            $out.Action = "Revoked"
            if ($endEvent.Properties.Content.ContainsKey("responseBody")) {
                $messageBody = ConvertFrom-Json $endEvent.Properties.Content["responseBody"]
            }
        }

        if ($messageBody) {
            $out.PrincipalId = $messageBody.properties.principalId
            $pd = Get-PrincipalDetails $out.PrincipalId
            $out.PrincipalName = $pd.Name
            $out.PrincipalType = $pd.Type
            $out.RoleName = (Get-AzureRoleDefinitionName $messageBody.properties.roleDefinitionId)
            $out.Scope = $messageBody.properties.Scope
            $rd = Get-ResourceDetails $out.Scope
            $out.ScopeName = $rd.Name
            $out.ScopeType = $rd.Type
            $out.RoleDefinitionId = $messageBody.properties.roleDefinitionId
        }

        $out
    }
}

```

А вот команды для выполнения сценария:

```
$log = Get-AzureRBACAuditLog '2015-06-26' '2015-06-27'

$log | Format-Table
```

## Известные проблемы при использовании управления доступом на основе ролей

Если при использовании функции управления доступом на основе ролей у вас возникла проблема, см. известные причины проблем в статье [Устранение неполадок при управлении доступом на основе ролей](role-based-access-control-troubleshooting.md).


## Встроенные роли

Управление доступом на основе ролей Azure поставляется с следующими встроенными ролями, которые могут быть назначены пользователям, группам и службам. Изменить определение встроенных ролей нельзя. В следующих выпусках RBAC в Azure появится возможность определять настраиваемые роли, выбирая наборы действий, которые можно выполнять с ресурсами Azure.

Щелкните соответствующую ссылку для просмотра свойств **actions** и **not actions** в определении роли. Свойство **actions** указывает разрешенные действия с ресурсами Azure. В строках действий можно использовать подстановочные знаки. Свойство **not actions** определения роли указывает действия, которые должны быть исключены из списка разрешенных действий.


Имя роли | Описание
------------- | -------------  
[Участник службы управления API](#api-management-service-contributor) | Позволяет управлять службами управления API, но не доступом к ним.
[Участник компонента Application Insights](#application-insights-component-contributor) | Позволяет управлять компонентами Application Insights, но не доступом к ним.
[Участник BizTalk](#biztalk-contributor) | Позволяет управлять службами BizTalk, но не доступом к ним.
[Участник базы данных ClearDB MySQL](#cleardb-mysql-db-contributor) | Позволяет управлять базами данных ClearDB MySQL, но не доступом к ним.
[Участник](#contributor) | Участники могут управлять всем, кроме доступа.
[Участник фабрики данных](#data-factory-contributor) | Позволяет управлять фабриками данных, но не доступом к ним.
[Участник учетной записи DocumentDB](#document-db-account-contributor) | Позволяет управлять учетными записями DocumentDB, но не доступом к ним.
[Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) | Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним.
[Участник учетной записи NewRelic APM](#newrelic-apm-account-contributor) | Позволяет управлять учетными записями управления производительностью приложений New Relic и приложениями, но не доступом к ним.
[Владелец](#owner) | Владелец может управлять всем, включая доступ.
[Читатель](#reader) | Читатель может все просматривать, но не может вносить изменения.
[Участник кэша Redis](#redis-cache-contributor) | Позволяет управлять кэшами Redis, но не доступом к ним.
[Участник базы данных SQL](#sql-db-contributor) | Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, нельзя управлять их политиками безопасности или их родительскими серверами SQL Server.
[Диспетчер безопасности SQL](#sql-security-manager) | Позволяет управлять политиками безопасности серверов SQL Server и баз данных, но не доступом к ним.
[Участник SQL Server](#sql-server-contributor) | Позволяет управлять серверами SQL Server и базами данных, за исключением доступа к ним и политик безопасности.
[Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним.
[Участник службы поиска](#search-service-contributor) | Позволяет управлять службами поиска, но не доступом к ним.
[Участник учетной записи хранения](#storage-account-contributor) | Позволяет управлять учетными записями хранения, но не доступом к ним.
[Администратор доступа пользователей](#user-access-administrator) | Позволяет управлять доступом пользователей к ресурсам Azure.
[Участник виртуальной машины](#virtual-machine-contributor) | Позволяет управлять виртуальными машинами, кроме доступа к ним, а также виртуальных сетей и учетных записей хранения, к которым они подключены.
[Участник виртуальной сети](#virtual-network-contributor) | Позволяет управлять виртуальными сетями, но не доступом к ним.
[Участник веб-плана](#web-plan-contributor) | Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним.
[Участник веб-сайта](#website-contributor) | Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним.


### Участник службы управления API

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Создание служб управления API и управление ими</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник компонента Application Insights

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Создание и управление компонентами Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Создание и управление веб-тестами</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Создание и управление правилами оповещения</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

### Участник BizTalk

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>создание и управление службами BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник ClearDB MySQL DB

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>*</td>
<td>Создание и управление ресурсами всех типов</td>
</tr>
<tr>
<th colspan="2">Запрещенные действия</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Не может создавать роли и назначения ролей </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Не может удалять роли и назначения ролей</td>
</tr>
</table>

### Участник фабрики данных

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Создание и управление фабриками данных</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник учетной записиDocument DB

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Создание и управление учетными записями DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник учетной записи интеллектуальных систем

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник учетной записиNewRelic APM

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### владелец;

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>*</td>
<td>Создание и управление ресурсами всех типов</td>
</tr>
</table>

### читатель.

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>*/чтение</td>
<td>Чтение ресурсов всех типов. Не может читать секретные данные.</td>
</tr>
</table>

### Участник кэша Redis

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Создание и управление кэшами Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник БД SQL

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Чтение ресурсов подписки</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Чтение ресурсов группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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
<td>Не может управлять политиками аудита базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Не может управлять политиками подключения базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Не может управлять политиками маскирования данных базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Не может управлять метриками безопасности базы данных SQL</td>
</tr>
</table>

### Диспетчер безопасности SQL

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
<td>Не может управлять политиками маскирования данных базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Создание и управление метриками безопасности базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник SQL Server

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>Создание серверов SQL Server и управление ими</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Чтение ресурсов подписки</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Чтение ресурсов группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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
<td>Не может управлять политиками аудита SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Не может управлять политиками аудита базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Не может управлять политиками подключения базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Не может управлять политиками маскирования данных базы данных SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Не может управлять метриками безопасности базы данных SQL</td>
</tr>
</table>

### Участник коллекции заданий планировщика

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник службы поиска

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Создание и управление службами поиска</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник учетной записи хранения

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Администратор доступа пользователей

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>*/чтение</td>
<td>Чтение ресурсов всех типов.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Создание и управление ролями и назначениями ролей</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Создание и управление запросами на обслуживание</td>
</tr>
</table>

### Участник виртуальной машины

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Чтение учетных записей хранения</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Получение ключей учетной записи хранения</td>
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
<td>Чтение зарезервированных IP-адресов</td>
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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник виртуальной сети

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
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник веб-плана

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Создание и управление веб-планами</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

### Участник веб-сайта

<table style=width:100%">
<tr>
<th colspan="2">Действия</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Чтение веб-планов</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Присоединение веб-планов</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Создание и управление веб-сайтами</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Создание и управление сертификатами веб-сайтов</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Чтение ролей и назначения ролей</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Чтение группы ресурсов</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Создание и управление развертываниями группы ресурсов</td>
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

Испытайте функцию управления доступом на основе ролей в Azure и отправьте нам [отзыв](http://aka.ms/azurerbacfeedback).


## Дальнейшие действия

Дополнительные ресурсы для работы с управлением доступом на основе ролей:

+ [Управление доступом на основе ролей с помощью Windows PowerShell](role-based-access-control-powershell.md)
+ [Управление доступом на основе ролей с помощью интерфейса командной строки Azure](role-based-access-control-xplat-cli.md)
+ [Устранение неполадок управления доступом на основе ролей](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium и Basic](active-directory-editions.md)
+ [Как подписки Azure связаны с Azure AD](active-directory-how-subscriptions-associated-directory.md)
+ Базовые сведения о самостоятельном управлении группами безопасности в [блоге группы разработчиков Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)

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

<!---HONumber=July15_HO5-->