<properties
   pageTitle="Управление доступом к ресурсам"
   description="Использование управления доступом на основе ролей (RBAC) для управления разрешениями пользователей для ресурсов, развернутых в Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="11/28/2015"
   ms.author="tomfitz"/>

# Управление доступом к ресурсам

Благодаря диспетчеру ресурсов Azure, вы сможете убедиться, что сотрудники вашей организации имеют соответствующие разрешения управлять ресурсами и получать доступ к ресурсам. Диспетчер ресурсов использует управление доступом на основе ролей (RBAC), поэтому вы с легкостью сможете применить политики безопасности к отдельным ресурсам или группам ресурсов. Например вы можете предоставить пользователю доступ к определенной виртуальной машине в подписке, или предоставить пользователю возможность управлять всеми веб-сайтами в подписке, но не другими ресурсами.

Этот раздел посвящен командам, которые можно использовать для назначения ролей и разрешений. Общие сведения об контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей на портале Microsoft Azure](active-directory/role-based-access-control-configure.md).

## Основные понятия

Вам необходимо разобраться с несколькими основными понятиями управления доступом на основе ролей.

1. Субъект — сущность, которой предоставлено разрешение, например пользователь, группа безопасности или приложение Azure Active Directory.
2. Роль — набор разрешенных и исключенных действий.
3. Действие — действие, которое выполняется с ресурсом (например, чтение или создание). 
4. Область — уровень, к которому применяется роль, например подписка, группа ресурсов или ресурс.
5. Назначение роли — процесс привязки субъекта к роли и задания области.

Вы можете получить список поддерживаемых **действий** с помощью кроссплатформенной программы командной строки Azure (xPlat CLI) или модуля Azure PowerShell.

используйте следующую команду в кроссплатформенной программе командной строки Azure, чтобы вывести список **всех** действий для всех поставщиков ресурсов.

    azure provider operations show --operationSearchString '*';

Если вы используете модуль PowerShell диспетчера ресурсов Azure (AzureRm), используйте следующую команду, чтобы вывести список **всех** действий для **всех** поставщиков ресурсов.

    Get-AzureRmProviderOperation -OperationSearchString *;

## Примеры ролей
Давайте рассмотрим некоторые примеры общих определений ролей, чтобы лучше разобраться с основными понятиями RBAC.

| Роль | Разрешенные действия |
| ------- | ----------------- |
| читатель. | */чтение (чтение чего-угодно) || Владелец | * (чтение и запись чего-угодно) |

Чтобы назначить роль **Чтение** **Пользователю A** для группы ресурсов с именем **ExampleGroup** и роль **Владелец** **Пользователю Б** для всей подписки, вы должны назначить следующее.

| Роль | Субъект | Область |
| --------|-------------|---------- |
| читатель. | Пользователь А | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| владелец; | Пользователь Б | /subscriptions/{subscriptionId} |

## Примеры сценариев

В этом разделе вы узнаете, как выполнить следующие типовые сценарии с помощью Azure PowerShell, интерфейса командной строки Azure для Mac, Linux и Windows, а также REST API.

1. Просмотр доступных ролей в подписке и разрешенных действий для этих ролей.
2. Предоставление разрешений для чтения участникам группы в подписке.
3. Предоставление разрешений участника приложению, чтобы приложение могло управлять ресурсами в группе ресурсов.
4. Предоставление разрешения владельца на определенном веб-сайте одному пользователю.
5. Составление списка журналов аудита группы ресурсов.


## Как использовать PowerShell для управления доступом

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


### Просмотр доступных ролей
Чтобы просмотреть все доступные роли для подписки, выполните команду **Get-AzureRmRoleDefinition**.

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### Предоставление разрешений для чтения группе для подписки.
1. Просмотрите определение роли **Читатель**, указав имя роли при выполнении команды **Get-AzureRmRoleDefinition**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. Найдите нужную группу безопасности, выполнив команду **Get-AzureRmADGroup**. Укажите фактическое имя группы в своей подписке. Ниже приведен пример группы ExampleAuditorGroup.

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. Создайте назначение ролей для группы безопасности аудитора. По завершении выполнения команды возвращается новое назначение ролей.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###Предоставьте разрешение участника приложению для группы ресурсов.
1. Просмотрите определение роли **Участник**, указав имя роли при выполнении команды **Get-AzureRmRoleDefinition**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. Получите идентификатор объекта субъекта-службы, выполнив команду **Get-AzureRmADServicePrincipal** и указав имя приложения в своей подписке. Ниже приведен пример приложения ExampleApplication.

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. Создайте назначения ролей для субъекта-службы, выполнив команду **New-AzureRmRoleAssignment**.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Более подробное описание настройки приложения Azure Active Directory и субъекта-службы см. в разделе [Аутентификация субъекта-службы с помощью диспетчера ресурсов Azure](resource-group-authenticate-service-principal.md).

###Предоставление разрешения владельца пользователю для ресурса.
1. Просмотрите определение роли **Владелец**, указав имя роли при выполнении команды **Get-AzureRmRoleDefinition**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        PS C:\> Get-AzureRmRoleDefinition Owner

2. Создайте назначения ролей для пользователя.

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Составление списка журналов аудита группы ресурсов.
Чтобы получить журнал аудита для группы ресурсов, выполните команду **Get-AzureRmLog** (или **Get-AzureResourceGroupLog** для версий Azure PowerShell, выпущенных до предварительной версии 1.0).

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## Использование интерфейса командной строки Azure для Mac, Linux и Windows

Если у вас не установлен интерфейс командной строки Azure для Mac, Linux и Windows, или вы не настроили учетную запись организации для использования с интерфейсом командной строки Azure, см. раздел [Установка и настройка интерфейса командной строки Azure](xplat-cli-install.md).

1. Войдите в свою учетную запись Azure с помощью своих учетных данных. Команда возвращает результат вашего входа.

        azure login

        ...
        info:    login command OK

2. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания.

        azure account set <YourSubscriptionNameOrId>

3. Переключитесь на модуль диспетчера ресурсов Azure. Вы получите подтверждение нового режима.

        azure config mode arm
        
        info:     New mode is arm

### Просмотр доступных ролей
Просмотр всех ролей, доступных для вашей подписки. Возвращается список определений ролей.

    azure role list

### Предоставление разрешений для чтения группе для подписки.
1. Получите определение роли для роли **Чтение**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. Получите нужную группу безопасности и ее ObjectId, выполнив поиск группы по имени. Далее показан пример группы ExampleAuditorGroup.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. Создайте назначение ролей для группы безопасности.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### Предоставьте разрешение участника приложению для группы ресурсов.
1. Получите определение роли для роли **Участник**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        azure role show Contributor

2. Получение ObjectId для приложения. Укажите имя приложения, которое нужно получить.

        azure ad sp show --search ExampleApplicationName

2. Создайте назначение ролей для приложения.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###Предоставление разрешения владельца пользователю для определенного веб-сайта.
1. Получите определение роли для роли **Владелец**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        azure role show Owner

2. Создайте назначения роли для пользователя.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###Составление списка журналов аудита группы ресурсов.
Чтобы получить журнал аудита для группы ресурсов, выполните команду **azure group log show** и укажите имя нужной группы ресурсов.

         azure group log show ExampleGroupName


## Как использовать интерфейс REST API
Чтобы настроить управление доступом на основе ролей через REST API диспетчера ресурсов Azure, вы должны задать общие заголовки и параметры (включая маркеры аутентификации) при отправке запросов. Дополнительную информацию см. в разделе [Распространенные параметры и заголовки](https://msdn.microsoft.com/library/azure/dn906885.aspx).
   
Чтобы определить поддерживаемые версии API, запустите следующую команду:

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

Для этого раздела вы можете использовать версию `2014-10-01-preview`.

###Создание назначения роли
Получение доступных определений ролей.

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

Создание назначения ролей.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###Удаление назначения ролей

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## Дальнейшие действия

- Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей на портале Microsoft Azure](role-based-access-control-configure.md).
- Дополнительные сведения о работе с субъектами-службами для управления доступом к приложениям в своей подписке см. в статьях [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md) и [Создание нового субъекта-службы Azure с помощью портала Azure](../resource-group-create-service-principal-portal.md).
- Дополнительные сведения об аудите операций в организации см. в разделе [Операции аудита с помощью диспетчера ресурсов](resource-group-audit.md).
- Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Дополнительные сведения см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).

 

<!---HONumber=AcomDC_1203_2015-->