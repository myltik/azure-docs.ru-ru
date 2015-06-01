<properties 
	pageTitle="Управление доступом на основе ролей с помощью Windows PowerShell" 
	description="Управление доступом на основе ролей с помощью Windows PowerShell" 
	services="" 
	documentationCenter="" 
	authors="guangyang" 
	manager="terrylan" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2014" 
	ms.author="guayan"/>

# Управление доступом на основе ролей с помощью Windows PowerShell #

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/role-based-access-control-powershell.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/ru-ru/documentation/articles/role-based-access-control-xplat-cli.md" title="Cross-Platform CLI">Межплатформенный интерфейс командной строки</a></div>

Функция управления доступом на основе ролей (RBAC) на портале предварительной версии Azure и в API диспетчера ресурсов Azure позволяет управлять доступом к подписке на детальном уровне. С ее помощью вы можете предоставлять доступ пользователям, группам и субъектам-службам Active Directory, назначая им роли с определенной областью.

Из этого учебника вы узнаете, как использовать Windows PowerShell для управления RBAC. Он представляет собой пошаговое руководство по созданию и проверке назначений ролей.

**Предполагаемое время для выполнения:** 15 минут

## Необходимые условия ##

Чтобы использовать Windows PowerShell для управления RBAC, вам понадобится:

- Windows PowerShell версии 3.0 или 4.0. Чтобы узнать версию используемой оболочки Windows PowerShell, введите $PSVersionTable и убедитесь, что для параметра PSVersion установлено значение 3.0 или 4.0. Чтобы установить совместимую версию, см. статью [Windows Management Framework 3.0 ](http://www.microsoft.com/ru-ru/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/ru-ru/download/details.aspx?id=40855).

- Azure PowerShell версии 0.8.8 или более поздней. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

Этот учебник предназначен для начинающих пользователей Windows PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell см. в статье [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

	Get-Help Add-AzureAccount -Detailed

Чтобы узнать, как настраивать и использовать диспетчер ресурсов Azure в Windows PowerShell, также прочитайте следующие руководства:

- [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)
- [Использование Windows PowerShell с диспетчером ресурсов](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/)

## В этом учебнике ##

* [Подключение к своим подпискам](#connect)
* [Проверка существующих назначений ролей](#check)
* [Создание назначения роли](#create)
* [Проверка разрешений](#verify)
* [Дальнейшие действия](#next)

## <a id="connect"></a>Подключение к подпискам ##

RBAC работает только с диспетчером ресурсов Azure, поэтому в первую очередь необходимо перейти в режим диспетчера ресурсов Azure. Для этого введите:

    PS C:> Switch-AzureMode -Name AzureResourceManager

Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

Чтобы подключиться к своим подпискам Azure, введите:

    PS C:> Add-AzureAccount

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. PowerShell соберет сведения обо всех подписках для этой учетной записи. По умолчанию будет использоваться первая подписка. Обратите внимание: при использовании RBAC вы получите только те подписки, в которых у вас есть какие-либо разрешения, т. е. для которых вы являетесь соадминистратором или вам назначена какая-либо роль. 

Если у вас несколько подписок и вы хотите переключиться на другую, введите:

    # Ваши подписки отобразятся под именем вашей учетной записи.
    PS C:> Get-AzureSubscription
    # Щелкните название подписки, чтобы выбрать ее.
    PS C:> Select-AzureSubscription -SubscriptionName <subscription name>

Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

## <a id="check"></a>Проверка существующих назначений ролей ##

Теперь давайте проверим, какие назначения ролей уже есть в подписке. Введите:

    PS C:> Get-AzureRoleAssignment

Эта команда возвращает все назначения ролей в подписке. Обратите внимание на два момента:

1. Вам потребуется доступ на чтение на уровне подписки.
2. Если в подписке много назначений ролей, получение сведений о них может занять некоторое время.

Можно также проверить существующие назначения ролей для конкретного определения роли с конкретной областью для отдельного пользователя. Введите:

    PS C:> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Эта команда возвращает все назначения ролей для определенного пользователя в клиенте Active Directory, которому назначена роль "Владелец" для группы ресурсов group1. Назначение роли может иметь два разных источника:

1. Назначение роли "Владелец" пользователю для группы ресурсов.
2. Назначение роли "Владелец" пользователю для родительского объекта группы ресурсов (в этом случае - подписки). Если у вас есть какое-либо разрешение на определенном уровне, вы получите аналогичное разрешение на доступ ко всем дочерним объектам.

Все параметры этого командлета необязательны. Вы можете использовать их в различных сочетаниях для проверки назначений ролей с разными фильтрами.

## <a id="create"></a>Создание назначения роли ##

При создании назначения роли необходимо учесть перечисленные ниже моменты.

- Кому вы хотите назначить роль? Для просмотра пользователей, групп и субъектов-служб, имеющихся в клиенте Active Directory, можно использовать следующие командлеты Azure Active Directory:

    `PS C:> Get-AzureADUser
    PS C:> Get-AzureADGroup
    PS C:> Get-AzureADGroupMember
    PS C:> Get-AzureADServicePrincipal` 

- Какую роль вы хотите назначить? Для просмотра поддерживаемых определений ролей можно использовать следующий командлет:

    `PS C:> Get-AzureRoleDefinition`

- Какую область вы хотите назначить? Доступны три уровня:

    - текущая подписка;
    - Группа ресурсов; чтобы получить список групп ресурсов, введите PS C:> Get-AzureResourceGroup
    - Ресурс; чтобы получить список ресурсов, введите PS C:> Get-AzureResource

Затем воспользуйтесь командлетом New-AzureRoleAssignment, чтобы создать назначение роли. Например:

 - Создание назначения роли "Читатель" пользователю на уровне текущей подписки:

    `PS C:> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

- Создание назначения роли на уровне группы ресурсов:

    `PS C:> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

- Создание назначения роли на уровне ресурса:

    `PS C:> $resources = Get-AzureResource
    PS C:> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <a id="verify"></a>Проверка разрешений ##

Если вашей учетной записи назначены какие-либо роли, вы можете просмотреть, какие именно разрешения предоставляют эти назначения, с помощью следующих командлетов:

    PS C:> Get-AzureResourceGroup
    PS C:> Get-AzureResource

Эти два командлета возвращают только группы ресурсов или ресурсы, для которых у вас есть разрешение на чтение. Они также отображают имеющиеся у вас разрешения.

После этого запуск другого командлета, например New-AzureResourceGroup, приведет к ошибке доступа, если у вас нет разрешения.

## <a id="next"></a>Дальнейшие действия ##

Чтобы узнать больше об управлении доступом на основе ролей с помощью Windows PowerShell, воспользуйтесь следующими ресурсами:
 
- [Управление доступом на основе ролей в Microsoft Azure](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): сведения об использовании командлетов в модуле AzureResourceManager.
- [Управление ресурсами Azure с помощью групп ресурсов](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups): узнайте, как создавать группы ресурсов на портале управления Azure и управлять ими.
- [Блог Azure](http://blogs.msdn.com/windowsazure): сведения о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): сведения о новых возможностях в Windows PowerShell.
- ["Эй, сценарист!" Блог ](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества Windows PowerShell.
- [Настройка управления доступом на основе ролей с использованием интерфейса командной строки XPLAT](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
- [Устранение неполадок управления доступом на основе ролей](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)

<!--HONumber=35.2-->

<!--HONumber=46--> 
