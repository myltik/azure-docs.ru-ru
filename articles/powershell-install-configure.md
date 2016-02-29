<properties
	pageTitle="Установка и настройка Azure PowerShell"
	description="Сведения об установке и настройке Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Установка и настройка Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Интерфейс командной строки Azure">Интерфейс командной строки Azure</a></div>

##Что такое Azure PowerShell?#
Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Эти командлеты можно использовать для создания, тестирования, развертывания решений и служб на платформе Azure и управления ими. В большинстве случаев командлеты можно использовать для выполнения тех же задач, что и портал управления Azure, например, для создания и настройки облачных служб, виртуальных машин, виртуальных сетей и веб-приложений.

<a id="Install"></a>
## Шаг 1. Установка

Azure PowerShell можно установить одним из двух методов — с помощью установщика веб-платформы (WebPI) или коллекции PowerShell.

> [AZURE.NOTE] Чтобы просмотреть все команды в интегрированной среде сценариев Windows PowerShell, после установки может потребоваться перезагрузка.

###Установка Azure PowerShell с помощью установщика веб-платформы

Установка Azure PowerShell 1.0 и более поздних версий с использованием WebPI выполняется также, как и для версий 0.9.x. Скачайте [Azure Powershell](http://aka.ms/webpi-azps) и запустите установку. Если вы установили Azure PowerShell 0.9.x, появится запрос для удаления версии 0.9.x. Если вы установили модули Azure PowerShell из коллекции PowerShell, установщик потребует удалить эти модули перед установкой, чтобы обеспечить целостность среды Azure PowerShell.

> [AZURE.NOTE] Если вы установили коллекцию модулей Azure PowerShell, программа установки автоматически удалит их. Это необходимо для предотвращения путаницы с тем, какие модули установлены и куда. Модули из коллекции PowerShell, как правило, устанавливаются в папку **%ProgramFiles%\\WindowsPowerShell\\Modules**. При использовании установщика веб-платформы модули Azure устанавливаются в папку **%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell**. **PowerShellGet** удалит модули, оставив заблокированные библиотеки DLL и соответствующие папки, если при удалении будут загружены зависимости модулей. Если во время установки возникла ошибка, удалите каталоги Azure* в каталоге **%ProgramFiles%\\WindowsPowerShell\\Modules**.

Если вы установили Azure PowerShell с помощью коллекции PowerShell, но вместо нее хотите использовать установку WebPI, то установка WebPI автоматически удалит установленные командлеты из коллекции.

> [AZURE.NOTE] При установке с использованием WebPI возникает известная проблема, связанная с **$env:PSModulePath** PowerShell. Если из-за установки обновлений системы или других компонентов потребуется перезагрузка, **$env:PSModulePath** может не включать путь установки Azure PowerShell. Чтобы это исправить, перезагрузите компьютер.

###Установка Azure PowerShell с помощью коллекции

Установите Azure PowerShell 1.0 или более поздней версии из коллекции, выполнив следующие команды:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM
    Install-AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

    # Import AzureRM modules for the given version manifest in the AzureRM module
    Import-AzureRM

    # Import Azure Service Management module
    Import-Module Azure

####Дополнительно об этих командах

- **Install-Module AzureRM** устанавливает модуль начальной загрузки для модулей AzureRM. Этот модуль включает в себя командлеты для безопасного обновления, удаления и импорта модулей AzureRM в последовательном порядке. Модуль AzureRM содержит список модулей и диапазон номеров версий (от минимального до максимального), чтобы предотвратить внесение критических изменений в основную версию AzureRM. Дополнительные сведения о семантическом управлении версиями см. на веб-сайте [semver.org](http://semver.org). Это означает, что вы можете создавать командлеты с использованием AzureRM определенной версии и при этом быть уверенными, что все модули, установленные с помощью механизма начальной загрузки, не внесут критических изменений.
- **Install-AzureRM** устанавливает все модули, объявленные в модуле начальной загрузки.
- **Install-Module Azure** устанавливает модуль Azure. Это модуль управления службами Azure PowerShell 0.9.x. Он не должен содержать крупные изменения. Кроме этого, этот модуль и модуль Azure предыдущей версии должны быть взаимозаменяемыми.
- **Import-AzureRM** импортирует все модули в списке модулей и версий модуля AzureRM. Это обеспечивает, что версии загружаемых модулей Azure PowerShell не выходят за рамки диапазона версий, требуемых для модуля AzureRM.
- **Import-Module Azure** импортирует модуль управления службами Azure. Обратите внимание, что модули Azure и AzureRM загружаются в сеанс PowerShell и могут использоваться вместе.


## Шаг 2. Запуск
Командлеты можно выполнять в стандартной консоли Windows PowerShell или в интегрированной среде сценариев PowerShell (ISE). Метод, используемый для открытия консоли, зависит от используемой версии Windows:

- На компьютере под управлением Windows 8, Windows Server 2012 или более поздних версий можно использовать встроенный поиск. На **начальном экране** введите "power". Откроется список приложений, включающий Windows PowerShell. Чтобы открыть консоль, щелкните любое приложение. (Для закрепления приложения на **начальном экране** щелкните значок приложения правой кнопкой мыши.)

- На компьютере под управлением Windows более ранней версии, чем Windows 8 или Windows Server 2012, можно использовать **меню "Пуск"**. В меню **Запустить** щелкните **Все программы**, нажмите кнопку **Стандартные**, выберите папку **Windows PowerShell** и затем щелкните **Windows PowerShell**.

Кроме того, можно запустить **интегрированную среду сценариев Windows PowerShell**, где можно использовать пункты меню и сочетания клавиш для многих задач, которые можно выполнить в консоли Windows PowerShell. Чтобы использовать интегрированную среду сценариев, в консоли Windows PowerShell, запускаемой через cmd.exe, или в поле **Выполнить** введите **powershell\_ise.exe**.

###Команды, которые помогут приступить к работе

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
	# If the Azure PowerShell module is not listed when you run Get-Module, you may need to import it
    Import-Module Azure 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To import the Azure.Storage data plane module (blob, queue, table)
    Import-Module Azure.Storage

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## Шаг 3. Подключение
Командлетам необходима ваша подписка для управления службами. Вы можете купить подписку Azure, если у вас ее еще нет. Указания см. в статье [Как приобрести Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Введите **Login-AzureRmAccount**.

2. Введите электронный адрес и пароль, связанные с вашей учетной записью. Azure выполняет проверку подлинности и сохраняет учетные данные, а затем закрывает окно.

ИЛИ

Войдите с помощью рабочей или учебной учетной записи.

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] При наличии нескольких клиентов, связанных с учетной записью организации, укажите параметр TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Этот неинтерактивный метод входа работает только с рабочей учетной записью. Рабочая учетная запись — это учетная запись, которой управляет соответствующая организация; она задается в Azure Active Directory для вашей организации. Если вы используете для входа в подписку Azure учетную запись Майкрософт, вы можете создать рабочую учетную запись, выполнив следующие действия.

> 1. Войдите на [портал управления Azure](https://manage.windowsazure.com) и щелкните **Active Directory**.

> 2. Если каталог не существует, выберите **Создание каталога** и укажите нужные данные.

> 3. Выберите каталог и добавьте нового пользователя. Этот новый пользователь может войти, используя рабочую учетную запись. При создании пользователя вам будут предоставлены электронной адрес и временный пароль. Эти данные используются на шаге 5. Сохраните их.

> 4. На портале управления выберите **Настройки** и затем **Администраторы**. Щелкните **Добавить**, чтобы добавить нового пользователя в качестве соадминистратора. Это позволит использовать вашу рабочую учетную запись для управления подпиской Azure.

> 5. Выйдите из портала Azure и затем войдите снова с использованием рабочей или учебной учетной записи. При первом входе в учетную запись появится запрос на изменение пароля.

> Дополнительные сведения о регистрации в Microsoft Azure с помощью рабочей или учебной учетной записи см. в разделе [Регистрация организации в Microsoft Azure](sign-up-organization.md).

> Дополнительную информацию об аутентификации и управлении подписками в Azure см. в статье [Управление учетными записями, подписками и административными ролями](http://go.microsoft.com/fwlink/?LinkId=324796).

### Просмотр сведений учетной записи и подписки

У вас может быть несколько учетных записей и подписок, доступных для использования в Azure PowerShell. Вы можете добавить несколько учетных записей, выполнив командлет **Add-AzureRmAccount** несколько раз.

Чтобы отобразить доступные учетные записи Azure, введите **Get-AzureAccount**.

Чтобы отобразить подписки Azure, введите **Get-AzureRmSubscription**.

##<a id="Help"></a>Получение справки##

В этих ресурсах вы найдете справку для определенных командлетов:


-   В консоли можно использовать встроенную справку. Командлет **Get-Help** предоставляет доступ к этой системе. 

- На этих популярных форумах можно получить помощь от сообщества:

 - [Форум Azure на MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##Подробнее


Дополнительные сведения об использовании командлетов см. на следующих ресурсах.

Основные инструкции по использованию Windows PowerShell см. в разделе [Использование Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Справочные сведения о командлетах см. в разделе [Справка по командлетам Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Примеры сценариев и инструкции, которые помогут вам освоить использование сценариев для управления Azure, см. в разделе [Центр скриптов](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0218_2016-->