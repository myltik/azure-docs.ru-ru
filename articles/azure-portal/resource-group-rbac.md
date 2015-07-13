<properties
   pageTitle="Управление доступом к ресурсам и его аудит"
   description="Использование управления доступом на основе ролей (RBAC) для управления разрешениями пользователей для ресурсов, развернутых в Azure."
   services="azure-portal"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/18/2015"
   ms.author="tomfitz"/>

# Управление доступом к ресурсам и его аудит

Благодаря диспетчеру ресурсов Azure, вы сможете убедиться, что сотрудники вашей организации имеют соответствующие разрешения управлять ресурсами и получать доступ к ресурсам. Диспетчер ресурсов использует управление доступом на основе ролей (RBAC), поэтому вы с легкостью сможете применить политики безопасности к отдельным ресурсам или группам ресурсов. Например вы можете предоставить пользователю доступ к определенной виртуальной машине в подписке, или предоставить пользователю возможность управлять всеми веб-сайтами в подписке, но не другими ресурсами.

## Основные понятия

Вам необходимо разобраться с несколькими основными понятиями управления доступом на основе ролей.

1. Субъект — сущность, которой предоставлено разрешение, например пользователь, группа безопасности или приложение.
2. Роль — набор разрешенных действий
3. Область — уровень, к которому применяется роль, например подписка, группа ресурсов или ресурс.
3. Назначение роли — процесс добавления субъекта к роли и задание области.

## Примеры ролей
Давайте рассмотрим некоторые примеры общих определений ролей, чтобы лучше разобраться с основными понятиями RBAC.

| Роль | Разрешенные действия |
| ------- | ----------------- |
| читатель. | */Чтение (Чтение чего-угодно) || Владелец | * (Чтение и запись чего-угодно) |

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
Если вы еще не установили последнюю версию Azure PowerShell, см. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Откройте консоль Azure PowerShell.

1. Войдите в свою учетную запись Azure с помощью своих учетных данных. Команда возвращает информацию о вашей учетной записи.

        PS C:\> Add-AzureAccount
          
        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Переключитесь на модуль диспетчера ресурсов Azure.

        PS C:\> Switch-AzureMode AzureResourceManager

### Просмотр доступных ролей
Чтобы просмотреть все доступные роли для подписки, выполните команду **Get AzureRoleDefinition**.

    PS C:\> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### Предоставление разрешений для чтения группе для подписки.
1. Просмотрите определение роли **Чтение**, указав имя роли при выполнении команды **Get AzureRoleDefinition**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        PS C:\> Get-AzureRoleDefinition Reader
   
        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. Найдите нужную группу безопасности, выполнив команду **Get AzureADGroup**. Укажите фактическое имя группы в своей подписке. Ниже приведен пример группы ExampleAuditorGroup.

        PS C:\> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. Создайте назначение ролей для группы безопасности аудитора. По завершении выполнения команды возвращается новое назначение ролей.

        PS C:\> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###Предоставьте разрешение участника приложению для группы ресурсов.
1. Просмотрите определение роли **Участник**, указав имя роли при выполнении команды **Get-AzureRoleDefinition**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        PS C:\> Get-AzureRoleDefinition Contributor

2. Получите ObjectId субъекта-службы, выполнив команду **Get AzureADServicePrincipal** и указав имя приложения в вашей подписке. Ниже приведен пример приложения ExampleApplication.

        PS C:\> $service = Get-AzureADServicePrincipal -SearchString ExampleApplicationName

3. Создайте назначения ролей для субъекта-службы, выполнив команду **New-AzureRoleAssignment**.

        PS C:\> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Более подробное описание настройки приложения Azure Active Directory и субъекта-службы см. в разделе [Аутентификация субъекта-службы с помощью диспетчера ресурсов Azure](../resource-group-authenticate-service-principal.md).

###Предоставление разрешения владельца пользователю для ресурса.
1. Просмотрите определение роли **Владелец**, указав имя роли при выполнении команды **Get AzureRoleDefinition**. Убедитесь, что разрешены именно те действия, которые вы хотите назначить.

        PS C:\> Get-AzureRoleDefinition Owner

2. Создайте назначения ролей для пользователя.

        PS C:\> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Составление списка журналов аудита группы ресурсов.
Чтобы получить журнал аудита для группы ресурсов, выполните команду **Get-AzureResourceGroupLog**.

      PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## Использование интерфейса командной строки Azure для Mac, Linux и Windows

Если у вас не установлен интерфейс командной строки Azure для Mac, Linux и Windows, или вы не настроили учетную запись организации для использования с интерфейсом командной строки Azure, см. раздел [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md).

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

- [Контроль доступа на основе ролей на портале Microsoft Azure](../role-based-access-control-configure.md)
- [Создание нового субъекта-службы Azure с помощью классического портала Azure](../resource-group-create-service-principal-portal.md)
- [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](../resource-group-authenticate-service-principal.md)

 

<!---HONumber=62-->