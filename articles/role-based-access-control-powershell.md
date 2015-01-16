<properties pageTitle="Управление доступом на основе ролей с помощью средства Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Управление доступом на основе ролей с помощью Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="guayan" />

# Управление доступом на основе ролей с помощью средства Windows PowerShell #

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Кросс-платформенный интерфейс командной строки средства Windows PowerShell</a><a href="/ru-ru/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI"></a></div>

Функция управления доступом на основе ролей (RBAC) на портале предварительной версии Azure и в API диспетчера ресурсов Azure позволяет управлять доступом к подписке на детальном уровне. С ее помощью вы можете предоставлять доступ пользователям, группам и субъектам-службам Active Directory, назначая им роли с определенной областью.

Из этого учебника вы узнаете, как использовать средство Windows PowerShell для управления доступом на основе ролей (RBAC). Он представляет собой пошаговое руководство по созданию и проверке назначений ролей.

**Предполагаемое время выполнения:** 15 минут

## Предварительные требования ##

Чтобы использовать Windows PowerShell для управления RBAC, вам понадобится:

- Windows PowerShell, версия 3.0 или 4.0. Чтобы узнать, какой версией Windows PowerShell вы пользуетесь, наберите "$PSVersionTable" и убедитесь, что значение параметра PSVersion - 3.0 или 4.0. Чтобы установить совместимую версию, см. [Платформы управления Windows 3.0 ](http://www.microsoft.com/ru-ru/download/details.aspx?id=34595) или [Платформы управления Windows 4.0](http://www.microsoft.com/ru-ru/download/details.aspx?id=40855).

- Azure PowerShell версии 0.8.8 или выше. Чтобы установить последнюю версию и ассоциировать ее с вашей подпиской Azure, см. [Установка и настройка Windows Azure PowerShell](http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/).

Данный учебник разработан для начинающих пользователей Windows PowerShell, но предполагается, что вы уже знакомы с базовыми понятиями, такими как модули, командлеты и сеансы. Для получения дополнительной информации о Windows PowerShell см. [Приступая к работе с Windows PowerShell](http://technet.microsoft.com/ru-ru/library/hh857337.aspx).

Для получения подробной справки о любом командлете, встречающемся в данном учебнике, используйте командлет Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

	Get-Help Add-AzureAccount -Detailed

Чтобы узнать, как настраивать и использовать диспетчер ресурсов Azure в Windows PowerShell, также прочитайте следующие руководства:

- [Установка и настройка Azure PowerShell](http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/)
- [Использование Windows PowerShell с диспетчером ресурсов](http://azure.microsoft.com/ru-ru/documentation/articles/powershell-azure-resource-manager/)

## В этом учебнике рассматриваются следующие темы: ##

* [Подключение к подпискам](#connect)
* [Проверка существующих назначений ролей](#check)
* [Создание назначения роли](#create)
* [Проверка разрешений](#verify)
* [Дальнейшие действия](#next)

## <a id="connect"></a>Подключение к подпискам ##

RBAC работает только с диспетчером ресурсов Azure, поэтому в первую очередь необходимо перейти в режим диспетчера ресурсов Azure. Для этого введите:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Для получения дополнительной информации см. [Использование Windows PowerShell с диспетчером ресурсов](http://azure.microsoft.com/ru-ru/documentation/articles/powershell-azure-resource-manager/).

Чтобы подключиться к своим подпискам Azure, введите:

    PS C:\> Add-AzureAccount

Во всплывающем окне браузера введите ваше имя пользователя Azure и пароль. PowerShell соберет сведения обо всех подписках для этой учетной записи. По умолчанию будет использоваться первая подписка. Обратите внимание: при использовании RBAC вы получите только те подписки, в которых у вас есть какие-либо разрешения, т. е. для которых вы являетесь соадминистратором или вам назначена какая-либо роль. 

Если у вас несколько подписок и вы хотите переключиться на другую, введите:

    # Здесь вы увидите подписки учетной записи.
    PS C:\> Get-AzureSubscription
    # Используя имя подписки, выберите подписку, с которой требуется продолжить работу.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Для получения дополнительной информации см. [Установка и настройка Azure PowerShell](http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/).

## <a id="check"></a>Проверка существующих назначений ролей ##

Теперь давайте проверим, какие назначения ролей уже есть в подписке. Введите:

    PS C:\> Get-AzureRoleAssignment

Эта команда возвращает все назначения ролей в подписке. Обратите внимание на два момента:

1. Вам потребуется доступ на чтение на уровне подписки.
2. Если в подписке много назначений ролей, получение сведений о них может занять некоторое время.

Можно также проверить существующие назначения ролей для конкретного определения роли с конкретной областью для отдельного пользователя. Введите:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Эта команда возвращает все назначения ролей для определенного пользователя в клиенте Active Directory, которому назначена роль "Владелец" для группы ресурсов group1. Назначение роли может иметь два разных источника:

1. Назначение роли "Владелец" пользователю для группы ресурсов.
2. Назначение роли "Владелец" пользователю для родительского объекта группы ресурсов (в этом случае - подписки). Если у вас есть какое-либо разрешение на определенном уровне, вы получите аналогичное разрешение на доступ ко всем дочерним объектам.

Все параметры этого командлета необязательны. Вы можете использовать их в различных сочетаниях для проверки назначений ролей с разными фильтрами.

## <a id="create"></a>Создание назначения роли ##

При создании назначения роли необходимо учесть перечисленные ниже моменты.

- Кому вы хотите назначить роль? Для просмотра пользователей, групп и субъектов-служб, имеющихся в клиенте Active Directory, можно использовать следующие командлеты Azure Active Directory:

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

- Какую роль вы хотите назначить? Для просмотра поддерживаемых определений ролей можно использовать следующий командлет:

    `PS C:\> Get-AzureRoleDefinition`

- Какую область вы хотите назначить? Доступны три уровня:

    - Текущая подписка
    - Группа ресурсов: чтобы получить список групп ресурсов, введите "PS C:\> Get-AzureResourceGroup"
    - Ресурс: чтобы получить список ресурсов, введите "PS C:\> Get-AzureResource"

Затем используйте New-AzureRoleAssignment для создания назначения ролей. Например: 

 - Создание назначения роли "Читатель" пользователю на уровне текущей подписки:

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

- Создание назначения роли на уровне группы ресурсов:

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

- Создание назначения роли на уровне ресурса:

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <a id="verify"></a>Проверка разрешений ##

Если вашей учетной записи назначены какие-либо роли, вы можете просмотреть, какие именно разрешения предоставляют эти назначения, с помощью следующих командлетов:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Эти два командлета возвращают только группы ресурсов или ресурсы, для которых у вас есть разрешение на чтение. Они также отображают имеющиеся у вас разрешения.

Затем при попытке выполнить другой командлет, например New-AzureResourceGroup, вы получите сообщение об отказе в доступе, если у вас нет соответствующего разрешения.

## <a id="next"></a>Дальнейшие действия ##

Чтобы узнать больше об управлении доступом на основе ролей с помощью Windows PowerShell, воспользуйтесь следующими ресурсами:
 
- [Управление доступом на основе ролей в Microsoft Azure](http://azure.microsoft.com/ru-ru/documentation/articles/role-based-access-control-configure/)
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): Сведения об использовании командлетов в модуле AzureResourceManager.
- [Использование групп ресурсов для управления ресурсами Azure](http://azure.microsoft.com/ru-ru/documentation/articles/azure-preview-portal-using-resource-groups): Узнайте, как создать группы ресурсов на портале управления Azure и управлять ими.
- [Блог Azure](http://blogs.msdn.com/windowsazure): Сведения о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): Сведения об использовании Windows PowerSHell.
- [Блог "Hey, Scripting Guy!"](http://blogs.technet.com/b/heyscriptingguy/): Получите реальные советы и полезные рекомендации от участников сообщества Windows PowerShell.
- [Управление доступом на основе ролей с помощью межплатформенного интерфейса командной строки](http://azure.microsoft.com/ru-ru/documentation/articles/role-based-access-control-xplat-cli/)
- [Устранение неполадок при управлении доступом на основе ролей](http://azure.microsoft.com/ru-ru/documentation/articles/role-based-access-control-troubleshooting/)
