---
title: Установка и настройка Azure PowerShell
description: Сведения об установке и настройке Azure PowerShell.
editor: tysonn
manager: dongill
documentationcenter: ''
services: ''
author: coreyp-at-msft

ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/22/2016
ms.author: coreyp

---
# Установка и настройка Azure PowerShell
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Интерфейс командной строки Azure">Интерфейс командной строки Azure</a></div>

## Что такое Azure PowerShell?
Azure PowerShell — это набор модулей, предоставляющих командлеты для управления Azure с помощью Windows PowerShell. Эти командлеты можно использовать для создания, тестирования, развертывания решений и служб на платформе Azure и управления ими. В большинстве случаев командлеты можно использовать для выполнения тех же задач, что и портал управления Azure, например, для создания и настройки облачных служб, виртуальных машин, виртуальных сетей и веб-приложений.

<a id="Install"></a>

## Шаг 1. Установка
Azure PowerShell можно установить одним из двух методов — с помощью установщика веб-платформы (WebPI) или коллекции PowerShell.

### Установка Azure PowerShell с помощью установщика веб-платформы
Установка Azure PowerShell 1.0 и более поздних версий с использованием WebPI выполняется также, как и для версий 0.9.x. Скачайте [Azure PowerShell](http://aka.ms/webpi-azps) и начните установку. Если у вас установлен Azure PowerShell 0.9.x, в процессе этого обновления версия 0.9.x будет удалена. Если вы установили модули Azure PowerShell из коллекции PowerShell, установщик автоматически удалит эти модули перед установкой, чтобы обеспечить целостность среды Azure PowerShell.

> [!NOTE]
> Если вы ранее установили модули Azure из коллекции PowerShell, программа установки автоматически удалит их. Это предотвращает путаницу с тем, какие версии модулей установлены и куда. Модули из коллекции PowerShell, как правило, устанавливаются в папку **%ProgramFiles%\WindowsPowerShell\Modules**. При использовании установщика WebPI модули Azure устанавливаются в папку **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell**. Если во время установки возникла ошибка, можно вручную удалить папки Azure* в каталоге **%ProgramFiles%\WindowsPowerShell\Modules** и повторить попытку установки.
> 
> 

По завершении установки ваш параметр ```$env:PSModulePath``` должен включать каталоги, содержащие командлеты Azure PowerShell.

> [!NOTE]
> При установке с помощью WebPI может возникать известная проблема, связанная с **$env:PSModulePath** PowerShell. Если из-за установки обновлений системы или других компонентов потребуется перезагрузка компьютера, это может привести к отсутствию в **$env:PSModulePath** пути установки Azure PowerShell. В этом случае при попытке использовать командлеты Azure PowerShell после установки или обновления может появиться сообщение "командлет не распознан". В таком случае перезагрузка компьютера должна устранить проблему.
> 
> 

При попытке загрузки или выполнения командлетов может появляться примерно следующее сообщение:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Это можно исправить перезагрузкой виртуальной машины или импортом командлетов из расположения C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ (где XXXX — установленная версия PowerShell):

```
import-module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
import-module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

### Установка Azure PowerShell из коллекции PowerShell
Устанавливайте Azure PowerShell 1.3.0 или более поздней версии из коллекции PowerShell, используя строку с повышенными привилегиями Windows PowerShell или интегрированной среды сценариев (ISE) PowerShell, с помощью следующих команд:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### Дополнительно об этих командах
* **Install-Module AzureRM** устанавливает сводный модуль для командлетов Azure Resource Manager. Модуль AzureRM зависит от конкретного диапазона версий для каждого модуля Azure Resource Manager. Включенный диапазон версий гарантирует, что никакие критические изменения модуля не будут включены при установке модулей AzureRM с одной и той же основной версией. При установке модуля AzureRM из коллекции PowerShell будут загружены и установлены все модули Azure Resource Manager, которые ранее не были установлены. Дополнительные сведения о семантическом версионировании, используемом в модулях Azure PowerShell, см. на странице [semver.org](http://semver.org). 
* **Install-Module Azure** устанавливает модуль Azure. Это модуль управления службами Azure PowerShell 0.9.x. Он не должен содержать крупные изменения. Кроме этого, этот модуль и модуль Azure предыдущей версии должны быть взаимозаменяемыми.

## Шаг 2. Запуск
Командлеты можно выполнять в стандартной консоли Windows PowerShell или в интегрированной среде сценариев PowerShell (ISE). Метод, используемый для открытия консоли, зависит от используемой версии Windows:

* На компьютере под управлением Windows 8, Windows Server 2012 или более поздних версий можно использовать встроенный поиск. На **начальном экране** введите power. Откроется список приложений, включающий Windows PowerShell. Чтобы открыть консоль, щелкните любое приложение. (Чтобы закрепить приложение на **начальном экране**, щелкните значок приложения правой кнопкой мыши.)
* Если на компьютере установлена более ранняя версия Windows, чем Windows 8 или Windows Server 2012, используйте меню **Пуск**. В меню **Пуск** щелкните пункт **Все программы**, выберите пункт **Стандартные**, выберите папку **Windows PowerShell** и затем щелкните **Windows PowerShell**.

Кроме того, можно запустить **интегрированную среду сценариев Windows PowerShell**. С помощью меню и сочетаний клавиш в ней можно выполнять многие задачи, которые выполняются в консоли Windows PowerShell. Чтобы воспользоваться интегрированной средой сценариев, в консоли Windows PowerShell, окне Cmd.exe или поле **Выполнить** введите **powershell\_ise.exe**.

### Команды, которые помогут приступить к работе
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

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
> [!NOTE]
> При наличии нескольких клиентов, связанных с учетной записью организации, укажите параметр TenantId:
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> Этот неинтерактивный метод входа работает только с рабочей учетной записью. Рабочая учетная запись — это учетная запись, которой управляет соответствующая организация; она задается в Azure Active Directory для вашей организации. Если вы используете для входа в подписку Azure учетную запись Майкрософт, вы можете создать рабочую учетную запись, выполнив следующие действия.
> 
> 1. Войдите на [классический портал Azure](https://manage.windowsazure.com) и щелкните **Active Directory**.
> 2. Если каталог не существует, выберите **Создание каталога** и укажите нужные данные.
> 3. Выберите каталог и добавьте нового пользователя. Этот новый пользователь может войти, используя рабочую учетную запись. При создании пользователя вам будут предоставлены электронной адрес и временный пароль. Эти данные используются на шаге 5. Сохраните их.
> 4. На классическом портале Azure выберите **Параметры**, а затем — **Администраторы**. Щелкните **Добавить**, чтобы добавить нового пользователя в качестве соадминистратора. Это позволит использовать вашу рабочую учетную запись для управления подпиской Azure.
> 5. Наконец, выйдите из классического портала Azure, а затем войдите снова с использованием рабочей или учебной учетной записи. При первом входе в учетную запись появится запрос на изменение пароля.
> 
> Дополнительные сведения о регистрации в Microsoft Azure с помощью рабочей или учебной учетной записи см. в разделе [Регистрация организации в Microsoft Azure](active-directory/sign-up-organization.md).
> 
> Дополнительную информацию об аутентификации и управлении подписками в Azure см. в статье [Управление учетными записями, подписками и административными ролями](http://go.microsoft.com/fwlink/?LinkId=324796).
> 
> 

### Просмотр сведений учетной записи и подписки
У вас может быть несколько учетных записей и подписок, доступных для использования в Azure PowerShell. Чтобы добавить несколько учетных записей, выполните командлет **Add-AzureRmAccount** несколько раз.

Чтобы отобразить доступные учетные записи Azure, введите **Get-AzureAccount**.

Чтобы просмотреть список подписок Azure, введите **Get-AzureRmSubscription**.

## <a id="Help"></a>Получение справки
В этих ресурсах вы найдете справку для определенных командлетов:

* В консоли можно использовать встроенную справку. Командлет **Get-Help** предоставляет доступ к этой системе. 
* На этих популярных форумах можно получить помощь от сообщества:
  
  * [Форум Azure на MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## Подробнее
Дополнительные сведения об использовании командлетов см. на следующих ресурсах.

Основные инструкции по работе с Windows PowerShell см. в статье [Использование Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Справочные сведения о командлетах см. в [справке по командлетам Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Примеры сценариев и инструкции по управлению Azure с помощью сценариев см. на странице [Центр скриптов](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0518_2016-->