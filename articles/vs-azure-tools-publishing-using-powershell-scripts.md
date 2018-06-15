---
title: Публикация решений в среды разработки и тестирования с помощью сценариев Windows PowerShell | Документация Майкрософт
description: Узнайте, как публиковать решения в среды разработки и тестирования с помощью сценариев Windows PowerShell в Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 866575a483e705e1c972a0b56d98f26e9cf0c631
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798127"
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Использование скриптов Windows PowerShell для публикации в средах разработки и тестирования

При создании веб-приложения в Visual Studio вы можете создать сценарий Windows PowerShell, который позволит автоматизировать публикацию решения на виртуальной машине или веб-сайте (как веб-приложения в службе приложений Azure). В редакторе Visual Studio этот сценарий можно изменить и расширить в соответствии со своими потребностями или интегрировать его в существующие сценарии сборки, тестирования и публикации.

С помощью этих сценариев вы можете подготавливать временные пользовательские версии сайта. Эти версии также называют средами разработки и тестирования. Например, на виртуальной машине Azure или в промежуточном слоте веб-сайта можно настроить определенную версию веб-сайта и использовать ее для проведения различных тестов, воспроизведения ошибки, проверки исправления ошибки или исследования предлагаемого изменения. Вы также можете создать специальную среду для демонстраций или презентаций. Создав сценарий публикации проекта, вы можете с его помощью воссоздавать идентичные среды или использовать его в своей сборке веб-приложения для создания отдельной среды тестирования.

## <a name="prerequisites"></a>предварительным требованиям

* Пакет Azure SDK, начиная с версии 2.3. Ознакомьтесь со страницей [скачиваемых компонентов Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384). (Пакет Azure SDK не нужен для создания сценариев для веб-проектов. Он предназначен для веб-проектов, а не веб-ролей облачных служб.)
* Azure PowerShell, начиная с версии 0.7.4. Ознакомьтесь со статьей [Установка и настройка Azure PowerShell](/powershell/azure/overview).
* [Windows PowerShell](http://go.microsoft.com/?linkid=9811175) , начиная с версии 3.0.

## <a name="additional-tools"></a>Дополнительные средства

Если вы занимаетесь разработкой решений для Azure, для работы с PowerShell в Visual Studio доступны дополнительные средства и ресурсы. Ознакомьтесь с разделом [Средства PowerShell для Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Создание сценариев публикации

Чтобы создать сценарии публикации для виртуальной машины, на которой будет размещен веб-сайт, при создании проекта воспользуйтесь [этими инструкциями](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Можно также [создать сценарии публикации для веб-приложений в службе приложений Azure](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Сценарии, создаваемые в Visual Studio

Visual Studio создает в решении папку **PublishScripts** , в которой хранятся два файла Windows PowerShell: сценарий публикации для виртуальной машины или веб-сайта и модуль с функциями, которые можно использовать в сценариях. Visual Studio также создает файл в формате JSON, в котором хранятся сведения о развертываемом проекте.

### <a name="windows-powershell-publish-script"></a>Сценарий публикации Windows PowerShell

Сценарий публикации содержит действия по развертыванию решения на веб-сайте или виртуальной машине. В Visual Studio работает цветовая подсветка синтаксиса Windows PowerShell и доступна справка по функциям. Кроме того, вы можете свободно изменять функции сценария в соответствии со своими потребностями.

### <a name="windows-powershell-module"></a>Модуль Windows PowerShell

Создаваемый в Visual Studio модуль Windows PowerShell содержит функции, которые используются в сценарии публикации. Эти функции Azure PowerShell не нужно изменять. Ознакомьтесь со статьей [Установка и настройка Azure PowerShell](/powershell/azure/overview).

### <a name="json-configuration-file"></a>Файл конфигурации в формате JSON
JSON-файл создается в папке **Конфигурации**. Он содержит данные конфигурации, указывающие, какие именно ресурсы нужно развернуть в Azure. Если вы создали веб-сайт, Visual Studio создает этот файл с именем имя_проекта-WAWS-dev.json. Если вы создали виртуальную машину, Visual Studio создает этот файл с именем имя_проекта-VM-dev.json. Ниже приведен пример файла конфигурации JSON, который создается для веб-сайта. Большая часть значений не нуждается в объяснении. Имя веб-сайта задает служба Azure, поэтому оно может не совпадать с именем вашего проекта.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```

В случае создания виртуальной машины файл конфигурации JSON выглядит так, как показано ниже. Для виртуальной машины создается контейнер в виде облачной службы. Виртуальная машина содержит обычные конечные точки для веб-доступа через HTTP и HTTPS, а также конечные точки для веб-развертывания, что позволяет публиковать решение на веб-сайт с локального компьютера, удаленного рабочего стола или через Windows PowerShell.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Редактируя файл конфигурации JSON, вы можете влиять на результат выполнения сценариев публикации. Разделы `cloudService` и `virtualMachine` обязательны, а вот раздел `databases` можно удалить, если он не нужен. Пустые свойства в стандартном файле конфигурации, который создается в Visual Studio, можно не использовать. Свойства, имеющие значение в стандартном файле конфигурации, — обязательные.

Если у вас есть веб-сайт с несколькими средами развертывания (также известны как слоты), вместо рабочего веб-сайта в Azure в файле конфигурации JSON в имени веб-сайта можно указать имя слота. Например, у вас есть веб-сайт с именем **mysite**, а его слот называется **test**. В этом случае универсальным кодом ресурса (URI) будет `mysite-test.cloudapp.net`, а в файле конфигурации будет использоваться имя mysite(test). Так можно сделать, только если веб-сайт и слоты уже есть в вашей подписке. Если их еще нет, создайте веб-сайт, запустив сценарий без указания слота, затем создайте слот на [портале Azure](https://portal.azure.com/). После этого запустите сценарий с измененным именем веб-сайта. Дополнительные сведения о слотах развертывания для веб-приложений см. в статье [Настройка промежуточных сред для веб-приложений в службе приложений Azure](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Запуск скриптов публикации

Если вы раньше не запускали сценарии Windows PowerShell, вам сначала необходимо настроить политику выполнения. Это позволит сценариям выполняться. Такая политика предусмотрена для того, чтобы защитить пользователей от вредоносных программ и вирусов, которые могут распространяться с помощью выполняемых сценариев, включая сценарии Windows PowerShell.

### <a name="run-the-script"></a>Запуск сценария

1. Создайте пакет веб-развертывания для своего проекта. Пакет веб-развертывания — это сжатый файл (ZIP-архив), содержащий файлы, которые нужно скопировать на веб-сайт или виртуальную машину. В Visual Studio можно создавать пакеты веб-развертывания для любых веб-приложений.

![Создание пакета веб-развертывания](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Дополнительные сведения см. в статье [Как создать пакет веб-развертывания в Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Создание пакета веб-развертывания можно автоматизировать. Это описано в разделе [Настройка и расширение сценариев публикации (#customizing-and-extending-publish-scripts)].

1. В **обозревателе решений** откройте контекстное меню сценария и выберите **Открыть с помощью PowerShell ISE**.
2. Если вы запускаете сценарии Windows PowerShell на этом компьютере впервые, откройте окно командной строки с правами администратора и введите такую команду:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Войдите в Azure, выполнив такую команду:

    ```powershell
    Add-AzureAccount
    ```

    При появлении запроса введите свои имя пользователя и пароль.

    Обратите внимание, что после автоматизации работы сценария этот способ указания учетных данных Azure не будет работать. В таком случае учетные данные нужно указать в файле `.publishsettings`. Один раз скачайте файл из Azure, выполнив команду **Get-AzurePublishSettingsFile**, после чего импортируйте его с помощью команды **Import-AzurePublishSettingsFile**. Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/overview).

4. (Этот этап можно пропустить.) Если вы хотите создать ресурсы Azure, такие как виртуальная машина, база данных и веб-сайт, не публикуя свое веб-приложение, выполните команду **Publish-WebApplication.ps1**, указав в качестве значения параметра **-Configuration** ссылку на JSON-файл конфигурации. Используя файл конфигурации JSON, эта командная строка определяет, какие именно ресурсы нужно создать. Так как для других аргументов командной строки используются параметры по умолчанию, командная строка создает ресурсы, не публикуя веб-приложение. Параметр -Verbose позволяет получить дополнительные сведения о том, что происходит при выполнении командной строки.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. С помощью команды **Publish-WebApplication.ps1** вызовите сценарий и опубликуйте свое веб-приложение (см. пример ниже). Если вам нужно изменить стандартные параметры других аргументов, например имя подписки, имя публикуемого пакета, учетные данные виртуальной машины или сервера базы данных, укажите эти параметры. Параметр **-Verbose** позволяет получить дополнительные сведения о ходе публикации.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Если вы создаете виртуальную машину, команда должна выглядеть так, как показано в примере ниже. В этом примере также показано, как указывать учетные данные для нескольких баз данных. SSL-сертификат виртуальных машин, создаваемых этими сценариями, происходит из ненадежного корневого центра. Поэтому вам нужно использовать параметр **-AllowUntrusted** .

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    Этот сценарий создает базы данных. Он не может создавать серверы баз данных. Если вы хотите создать сервер базы данных, используйте функцию **New-AzureSqlDatabaseServer** в модуле Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Настройка и расширение сценариев публикации
Вы можете настраивать сценарии публикации и изменять файл конфигурации JSON. Функции в модуле Windows PowerShell **AzureWebAppPublishModule.psm1** нельзя изменять. Чтобы выбрать другую базу данных или изменить некоторые свойства виртуальной машины, внесите необходимые изменения в файл конфигурации JSON. Если вы хотите расширить функциональность сценария и автоматизировать сборку и тестирование проекта, добавьте заготовки функций в файл **Publish-WebApplication.ps1**.

Чтобы автоматизировать сборку проекта, добавьте код, вызывающий функцию MSBuild `New-WebDeployPackage` (см. пример кода ниже). Путь к команде MSBuild зависит от установленной версии Visual Studio. Получить правильный путь можно с помощью функции **Get-MSBuildCmd** (см. пример ниже).

### <a name="to-automate-building-your-project"></a>Автоматизация сборки проекта
1. Добавьте параметр `$ProjectFile` в раздел глобальных параметров.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Скопируйте функцию `Get-MSBuildCmd` в файл сценария.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Замените `New-WebDeployPackage` следующим кодом и замените заполнители в строке `$msbuildCmd`. Этот код предназначен для Visual Studio 2017. Если вы используете Visual Studio 2015, замените значение свойства **VisualStudioVersion** на `14.0` (`12.0` для Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Для создания веб-приложения используйте файл MsBuild.exe. Справочник по командной строке MSBuild доступен по адресу [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Запуск команды сборки

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Вызовите функцию `New-WebDeployPackage` перед строкой `$Config = Read-ConfigFile $Configuration` (для веб-приложений) или `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` (для виртуальных машин).

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Вызовите настроенный сценарий из командной строки, передав аргумент `$Project`, как показано в примере ниже.

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Чтобы автоматизировать тестирование приложения, добавьте код в `Test-WebApplication`. Обязательно раскомментируйте строки в файле **Publish-WebApplication.ps1** , в которых вызываются эти функции. Если реализация не указана, проект можно вручную собрать в Visual Studio и опубликовать его в Azure с помощью сценария публикации.

## <a name="publishing-function-summary"></a>Обзор функций публикации
Чтобы получить справку по функциям, выполните в командной строке Windows PowerShell команду `Get-Help function-name`. Справочная информация содержит описание параметров и примеры их использования. Та же справочная информация приведена в исходных файлах сценариев **AzureWebAppPublishModule.psm1** и **Publish-WebApplication.ps1**. Сценарий и справка переведены на язык интерфейса Visual Studio.

**AzureWebAppPublishModule**

| Имя функции | ОПИСАНИЕ |
| --- | --- |
| Add-AzureSQLDatabase |Создает новую базу данных SQL Azure. |
| Add-AzureSQLDatabases |Создает базы данных SQL Azure на основе значений в файле конфигурации JSON, который создается в Visual Studio. |
| Add-AzureVM |Создает виртуальную машину Azure и возвращает URL-адрес развернутой виртуальной машины. Функция настраивает обязательные компоненты и затем вызывает функцию **New-AzureVM** (в модуле Azure), которая создает виртуальную машину. |
| Add-AzureVMEndpoints |Добавляет новые входные конечные точки для виртуальной машины и возвращает виртуальную машину с новой конечной точкой. |
| Add-AzureVMStorage |Создает в текущей подписке новую учетную запись хранения Azure. Имя учетной записи начинается с devtest, после чего следует уникальная строка из букв и цифр. Функция возвращает имя новой учетной записи хранения. Для новой учетной записи хранения укажите расположение или территориальную группу. |
| Add-AzureWebsite |Создает веб-сайт с указанными именем и расположением. Эта функция вызывает функцию **New-AzureWebsite** в модуле Azure. Если в подписке еще нет веб-сайта с указанным именем, функция создает его и возвращает объект веб-сайта. В противном случае она возвращает `$null`. |
| Backup-Subscription |Сохраняет текущую подписку Azure в переменной `$Script:originalSubscription` в области сценария. Эта функция сохраняет текущую подписку Azure (полученную с помощью команды `Get-AzureSubscription -Current`) и ее учетную запись хранения, а также подписку, которую изменяет этот сценарий (хранится в переменной `$UserSpecifiedSubscription`), и ее учетную запись хранения в области сценария. Сохранив эти значения, вы сможете (например, с помощью функции `Restore-Subscription`) восстановить исходное состояние текущей подписки и учетной записи хранения, если оно изменилось. |
| Find-AzureVM |Возвращает указанную виртуальную машину Azure. |
| Format-DevTestMessageWithTime |Добавляет в сообщение дату и время. Эта функция предназначена для сообщений, которые записываются в потоки Error и Verbose. |
| Get-AzureSQLDatabaseConnectionString |Собирает строку подключения к базе данных SQL Azure. |
| Get-AzureVMStorage |Возвращает из указанного расположения или территориальной группы первую учетную запись хранения, в имени которой есть строка devtest *(без учета регистра). Если учетная запись хранения devtest* не соответствует расположению или территориальной группе, функция пропускает ее. Укажите расположение или территориальную группу. |
| Get-MSDeployCmd |Возвращает команду для запуска средства MsDeploy.exe. |
| New-AzureVMEnvironment |Находит или создает в подписке виртуальную машину, которая соответствует значениям в файле конфигурации JSON. |
| Publish-WebPackage |Развертывает ресурсы на веб-сайт с помощью средства MsDeploy.exe и пакета веб-публикации (ZIP-файл). Эта функция не создает никаких выходных данных. Если вызов MSDeploy.exe завершился ошибкой, функция создает исключение. Чтобы получить более подробные выходные данные, используйте параметр **-Verbose** . |
| Publish-WebPackageToVM |Проверяет значения параметров, а затем вызывает функцию **Publish-WebPackage** . |
| Read-ConfigFile |Проверяет файл конфигурации JSON и возвращает хэш-таблицу выбранных значений. |
| Restore-Subscription |Восстанавливает исходное состояние подписки. |
| Test-AzureModule |Возвращает `$true` , если установленный модуль Azure имеет версию 0.7.4 и выше. Возвращает `$false` , если модуль не установлен или имеет более раннюю версию. У этой функции нет параметров. |
| Test-AzureModuleVersion |Возвращает `$true` , если модуль Azure имеет версию 0.7.4 и выше. Возвращает `$false` , если модуль не установлен или имеет более раннюю версию. У этой функции нет параметров. |
| Test-HttpsUrl |Преобразует входной URL-адрес в объект System.Uri. Возвращает `$True` , если URL-адрес является абсолютным адресом и использует схему HTTPS. Возвращает `$false` , если URL-адрес является относительным, не использует схему HTTPS или входную строку невозможно преобразовать в URL-адрес. |
| Test-Member |Возвращает `$true` , если свойство или метод является членом объекта. В противном случае возвращается `$false`. |
| Write-ErrorWithTime |Записывает сообщение об ошибке с текущим временем в префиксе. Эта функция вызывает функцию **Format-DevTestMessageWithTime** , которая добавляет время перед записью сообщения в поток Error. |
| Write-HostWithTime |Записывает в основную программу (**Write-Host**) сообщение с текущим временем в префиксе. Результат записи в основную программу варьируется. Большинство программ, использующих Windows PowerShell, записывают эти сообщения в стандартный вывод. |
| Write-VerboseWithTime |Записывает подробное сообщение с текущим временем в префиксе. Так как функция вызывает функцию **Write-Verbose**, сообщение появляется, только если сценарий запускается с параметром **-Verbose** или для параметра **VerbosePreference** задано значение **Continue**. |

**Publish-WebApplication**

| Имя функции | ОПИСАНИЕ |
| --- | --- |
| New-AzureWebApplicationEnvironment |Создает ресурсы Azure, например веб-сайт или виртуальную машину. |
| New-WebDeployPackage |Эта функция не реализована. В нее можно добавлять команды для сборки проекта. |
| Publish-AzureWebApplication |Публикует веб-приложение в Azure. |
| Publish-WebApplication |Создает и развертывает веб-приложения, виртуальные машины, базы данных SQL и учетные записи хранения для веб-проекта Visual Studio. |
| Test-WebApplication |Эта функция не реализована. В нее можно добавлять команды для тестирования приложения. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о сценариях PowerShell см. в статье [Работа со сценариями в Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx). Рекомендуем также посетить [центр сценариев](https://azure.microsoft.com/documentation/scripts/) и ознакомиться с другими сценариями Azure PowerShell.
