<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Управление доступом на основе ролей с помощью Windows PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/ru-ru/documentation/articles/xplat-cli-rbac.md" title="Межплатформенный интерфейс командной строки">Межплатформенный интерфейс командной строки</a></div>

Функция управления доступом на основе ролей (RBAC) на портале предварительной версии Azure и в API диспетчера ресурсов Azure позволяет управлять доступом к подписке на детальном уровне. С ее помощью вы можете предоставлять доступ пользователям, группам и субъектам-службам Active Directory, назначая им роли с определенной областью.

Из этого учебника вы узнаете, как использовать Windows PowerShell для управления RBAC. Он представляет собой пошаговое руководство по созданию и проверке назначений ролей.

**Предполагаемое время выполнения:** 15 минут

## Предварительные требования

Чтобы использовать Windows PowerShell для управления RBAC, вам понадобится:

-   Windows PowerShell 3.0 или 4.0. Чтобы узнать версию Windows PowerShell, введите команду `$PSVersionTable`. Убедитесь в том, что параметр `PSVersion` имеет значение 3.0 или 4.0. Сведения об установке совместимой версии см. в разделе [Windows Management Framework 3.0][Windows Management Framework 3.0] или [Windows Management Framework 4.0][Windows Management Framework 4.0].

-   Azure PowerShell версии 0.8.8 или более поздней. Сведения об установке последней версии и связывании ее с подпиской Azure см. в разделе [Установка и настройка Windows Azure PowerShell][Установка и настройка Windows Azure PowerShell].

Этот учебник предназначен для начинающих пользователей Windows PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell см. в разделе [Начало работы с Windows PowerShell][Начало работы с Windows PowerShell].

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

    Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

    Get-Help Add-AzureAccount -Detailed

Чтобы узнать, как настраивать и использовать диспетчер ресурсов Azure в Windows PowerShell, также прочитайте следующие руководства:

-   [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell]
-   [Использование Windows PowerShell с диспетчером ресурсов][Использование Windows PowerShell с диспетчером ресурсов]

## В этом учебнике рассматриваются следующие темы:

-   [Подключение к подпискам][Подключение к подпискам]
-   [Проверка существующих назначений ролей][Проверка существующих назначений ролей]
-   [Создание назначения роли][Создание назначения роли]
-   [Проверка разрешений][Проверка разрешений]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="connect"></span></a>Подключение к подпискам

RBAC работает только с диспетчером ресурсов Azure, поэтому в первую очередь необходимо перейти в режим диспетчера ресурсов Azure. Для этого введите:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Дополнительные сведения см. в разделе [Использование Windows PowerShell с диспетчером ресурсов][Использование Windows PowerShell с диспетчером ресурсов].

Чтобы подключиться к своим подпискам Azure, введите:

    PS C:\> Add-AzureAccount

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. PowerShell соберет сведения обо всех подписках для этой учетной записи. По умолчанию будет использоваться первая подписка. Обратите внимание: при использовании RBAC вы получите только те подписки, в которых у вас есть какие-либо разрешения, т. е. для которых вы являетесь соадминистратором или вам назначена какая-либо роль.

Если у вас несколько подписок и вы хотите переключиться на другую, введите:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Дополнительные сведения см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

## <span id="check"></span></a>Проверка существующих назначений ролей

Теперь давайте проверим, какие назначения ролей уже есть в подписке. Введите:

    PS C:\> Get-AzureRoleAssignment

Эта команда возвращает все назначения ролей в подписке. Обратите внимание на два момента:

1.  Вам потребуется доступ на чтение на уровне подписки.
2.  Если в подписке много назначений ролей, получение сведений о них может занять некоторое время.

Можно также проверить существующие назначения ролей для конкретного определения роли с конкретной областью для отдельного пользователя. Введите:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Эта команда возвращает все назначения ролей для определенного пользователя в клиенте Active Directory, которому назначена роль "Владелец" для группы ресурсов group1. Назначение роли может иметь два разных источника:

1.  Назначение роли "Владелец" пользователю для группы ресурсов.
2.  Назначение роли "Владелец" пользователю для родительского объекта группы ресурсов (в этом случае — подписки). Если у вас есть какое-либо разрешение на определенном уровне, вы получите аналогичное разрешение на доступ ко всем дочерним объектам.

Все параметры этого командлета необязательны. Вы можете использовать их в различных сочетаниях для проверки назначений ролей с разными фильтрами.

## <span id="create"></span></a>Создание назначения роли

При создании назначения роли необходимо учесть перечисленные ниже моменты.

-   Кому вы хотите назначить роль? Для просмотра пользователей, групп и субъектов-служб, имеющихся в клиенте Active Directory, можно использовать следующие командлеты Azure Active Directory:

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

-   Какую роль вы хотите назначить? Для просмотра поддерживаемых определений ролей можно использовать следующий командлет:

    `PS C:\> Get-AzureRoleDefinition`

-   Какую область вы хотите назначить? Доступны три уровня:

    -   текущая подписка;
    -   группа ресурсов; чтобы получить список групп ресурсов, введите `PS C:\> Get-AzureResourceGroup`
    -   ресурс; чтобы получить список ресурсов, введите `PS C:\> Get-AzureResource`

Затем используйте командлет `New-AzureRoleAssignment`, чтобы создать назначение роли. Например:

-   Создание назначения роли "Читатель" пользователю на уровне текущей подписки:

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

-   Создание назначения роли на уровне группы ресурсов:

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

-   Создание назначения роли на уровне ресурса:

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <span id="verify"></span></a>Проверка разрешений

Если вашей учетной записи назначены какие-либо роли, вы можете просмотреть, какие именно разрешения предоставляют эти назначения, с помощью следующих командлетов:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Эти два командлета возвращают только группы ресурсов или ресурсы, для которых у вас есть разрешение на чтение. Они также отображают имеющиеся у вас разрешения.

Попытавшись затем выполнить другой командлет, например `New-AzureResourceGroup`, вы получите сообщение об отказе в доступе, если у вас нет соответствующего разрешения.

## <span id="next"></span></a>Дальнейшие действия

Чтобы узнать больше об управлении доступом на основе ролей с помощью Windows PowerShell, воспользуйтесь следующими ресурсами:

-   [Управление доступом на основе ролей в Microsoft Azure][Управление доступом на основе ролей в Microsoft Azure]
-   [Командлеты диспетчера ресурсов Azure][Командлеты диспетчера ресурсов Azure]: сведения об использовании командлетов в модуле AzureResourceManager
-   [Использование групп ресурсов для управления ресурсами Azure][Использование групп ресурсов для управления ресурсами Azure]: сведения о создании групп ресурсов и управлении ими на портале управления Azure
-   [Блог Azure][Блог Azure]: сведения о новых возможностях Azure
-   [Блог Windows PowerShell][Блог Windows PowerShell]: сведения о новых возможностях Windows PowerShell
-   [Блог "Hey, Scripting Guy!"][Блог "Hey, Scripting Guy!"]: реальные советы и рекомендации от сообщества Windows PowerShell
-   [Управление доступом на основе ролей с помощью межплатформенного интерфейса командной строки][Управление доступом на основе ролей с помощью межплатформенного интерфейса командной строки]
-   [Устранение неполадок при управлении доступом на основе ролей][Устранение неполадок при управлении доступом на основе ролей]

  [Windows PowerShell]: /ru-ru/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [Межплатформенный интерфейс командной строки]: /ru-ru/documentation/articles/xplat-cli-rbac.md "Межплатформенный интерфейс командной строки"
  [Windows Management Framework 3.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=40855
  [Установка и настройка Windows Azure PowerShell]: http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/
  [Начало работы с Windows PowerShell]: http://technet.microsoft.com/ru-ru/library/hh857337.aspx
  [Установка и настройка Azure PowerShell]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/
  [Использование Windows PowerShell с диспетчером ресурсов]: http://azure.microsoft.com/ru-ru/documentation/articles/powershell-azure-resource-manager/
  [Подключение к подпискам]: #connect
  [Проверка существующих назначений ролей]: #check
  [Создание назначения роли]: #create
  [Проверка разрешений]: #verify
  [Дальнейшие действия]: #next
  [Управление доступом на основе ролей в Microsoft Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/role-based-access-control-configure/
  [Командлеты диспетчера ресурсов Azure]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [Использование групп ресурсов для управления ресурсами Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/azure-preview-portal-using-resource-groups
  [Блог Azure]: http://blogs.msdn.com/windowsazure
  [Блог Windows PowerShell]: http://blogs.msdn.com/powershell
  [Блог "Hey, Scripting Guy!"]: http://blogs.technet.com/b/heyscriptingguy/
  [Управление доступом на основе ролей с помощью межплатформенного интерфейса командной строки]: http://azure.microsoft.com/ru-ru/documentation/articles/role-based-access-control-xplat-cli/
  [Устранение неполадок при управлении доступом на основе ролей]: http://azure.microsoft.com/ru-ru/documentation/articles/role-based-access-control-troubleshooting/
