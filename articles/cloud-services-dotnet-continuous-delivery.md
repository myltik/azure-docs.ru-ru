<properties linkid="dev-net-common-tasks-continuous-delivery" urlDisplayName="Continuous Delivery" pageTitle="Continuous delivery for cloud services with TFS in Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Learn how to set up continuous delivery for Azure cloud apps. Code samples for MSBuild command-line statements and PowerShell scripts." metaCanonical="" services="" documentationCenter="" title="Continuous Delivery for Cloud Services in Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Непрерывная доставка для облачных служб в Azure

Описанный в этой статье процесс показывает, как настроить непрерывную доставку для облачных приложений Azure. Этот процесс позволяет автоматически создавать пакеты и развертывать пакет в Azure после каждого возврата кода. Процесс сборки пакета, описанный в этой статье, эквивалентен команде Package в Visual Studio. Этапы публикации эквивалентны команде Publish в Visual Studio. В статье описаны методы, которые можно использовать для создания сервера сборки с помощью инструкций командной строки MSBuild и сценариев Windows PowerShell, а также показано, как дополнительно настроить Visual Studio Team Foundation Server — определения Team Build для использования команд MSBuild и сценариев PowerShell. Процесс можно настраивать для среды сборки и целевых сред Azure.

Для упрощения задачи можно также использовать службу Visual Studio Online — версию TFS, размещенную в Azure. Дополнительные сведения см. в разделе [Непрерывная доставка в Azure с использованием Visual Studio Online][Непрерывная доставка в Azure с использованием Visual Studio Online].

Прежде чем начать, следует опубликовать приложение из Visual Studio. Это обеспечит доступность и инициализацию всех ресурсов при попытке автоматизировать процесс публикации.

Эта задача включает следующие шаги:

-   [Шаг 1: настройка сервера сборки][Шаг 1: настройка сервера сборки]
-   [Шаг 2: создание пакета с помощью команд MSBuild][Шаг 2: создание пакета с помощью команд MSBuild]
-   [Шаг 3: создание пакета с помощью TFS Team Build (дополнительно)][Шаг 3: создание пакета с помощью TFS Team Build (дополнительно)]
-   [Шаг 4: публикация пакета с помощью сценария PowerShell][Шаг 4: публикация пакета с помощью сценария PowerShell]
-   [Шаг 5: публикация пакета с помощью TFS Team Build (дополнительно)][Шаг 5: публикация пакета с помощью TFS Team Build (дополнительно)]

## <a name="step1"> </a><span class="short-header">Настройка сервера сборки</span>Шаг 1. Настройка сервера сборки

Прежде чем создавать пакет Azure с помощью MSBuild, следует установить на сервере сборки необходимое программное обеспечение и инструменты.

Visual Studio необязательно должна быть установлена на сервере сборки. Если вы хотите использовать для управления сервером сборки службу сборок Team Foundation, следуйте указаниям в документации [Настройка службы построения Team Foundation][Настройка службы построения Team Foundation]
.
1.  На сервере сборки установите платформу [.NET Framework 4][.NET Framework 4] или [.NET Framework 4.5][.NET Framework 4.5], которая включает в себя MSBuild.
2.  Установите [Azure Authoring Tools][Azure Authoring Tools] (ищите файл WindowsAzureAuthoringTools-x86.msi или WindowsAzureAuthoringTools-x64.msi, в зависимости от процессора сервера сборки).
3.  Установите [библиотеки Microsoft Azure][библиотеки Microsoft Azure] (ищите файл WindowsAzureLibsForNet-x86.msi или WindowsAzureLibsForNet-x64.msi).
4.  Скопируйте файл Microsoft.WebApplication.targets из установленного экземпляра Visual Studio на сервер сборки. На компьютере с установленным экземпляром Visual Studio этот файл находится в каталоге C:\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (версия 12.0 для Visual Studio 2013). Нужно скопировать его в тот же каталог на сервере сборки.
5.  Установите [инструменты Azure для Visual Studio][библиотеки Microsoft Azure]. Ищите файл WindowsAzureTools.VS110.exe для создания проектов Visual Studio 2012 и WindowsAzureTools.VS120.exe для создания проектов Visual Studio 2013.

## <a name="step2"> </a><span class="short-header">Создание пакета с помощью MSBuild</span>Шаг 2. Создание пакета с помощью команд MSBuild

В этом разделе описывается создание команды MSBuild, которая выполняет сборку пакета Azure. Выполните этот шаг на сервере сборки, чтобы убедиться в том, что все правильно настроено и команда MSBuild делает то, что нужно. Эту командную строку можно добавить в
существующие сценарии сборки на сервере сборки или использовать командную строку в определении сборки TFS, как описано в следующем разделе. Дополнительную информацию о параметрах командной строки и MSBuild см. в [справочнике по командной строке MSBuild][справочнике по командной строке MSBuild].

1.  Если Visual Studio установлена на сервере сборки, щелкните **Пуск**, **Все программы**, найдите и щелкните **Visual Studio Commmand Prompt** в папке **Visual Studio Tools**
    
    Если экземпляр Visual Studio не установлен на сервере сборки, откройте командную строку и убедитесь, что файл MSBuild.exe
    доступен по пути. MSBuild устанавливается вместе с .NET Framework по пути

    %WINDIR%\\Microsoft.NET\\Framework\\*версия*. Например, чтобы добавить файл MSBuild.exe в переменную среды PATH при наличии платформы .NET Framework 4, введите следующую команду в командной строке:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  В командной строке перейдите в папку, содержащую файл проекта Microsoft Azure, который требуется создать.

3.  Запустите msbuild с параметром /target:Publish, как показано в следующем примере:

        MSBuild /target:Publish

    Этот параметр можно сократить до /t:Publish. Параметр /t:Publish в MSBuild не следует путать с командами Publish, доступными в Visual Studio при наличии установленного пакета SDK для Azure. Параметр /t:Publish собирает только пакеты Azure. Он не осуществляет развертывание пакетов, как команды Publish в Visual Studio.

    Дополнительно можно указать имя проекта в качестве параметра MSBuild. Если не указано, используется текущий каталог. Дополнительную информацию о параметрах командной строки MSBuild см. в [справочнике по командной строке MSBuild][справочнике по командной строке MSBuild].

4.  Найдите данные вывода. По умолчанию эта команда создает каталог относительно корневой папки проекта, например *ProjectDir*\\bin\\*Configuration*\\app.publish\\. При сборке проекта Azure создается два файла — собственно файл пакета и сопутствующий файл конфигурации:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    По умолчанию каждый проект Azure включает в себя один файл конфигурации службы (с расширением .cscfg) для локальных сборок (отладка) и еще один файл для облачных сборок (промежуточная или рабочая среда), но вы можете добавлять или удалять файлы конфигурации службы по необходимости. При сборке пакета в Visual Studio вам будет предложено выбрать файл конфигурации службы для включения в пакет.

5.  Укажите файл конфигурации службы. При сборке пакета с использованием MSBuild по умолчанию включается файл конфигурации локальной службы. Чтобы включить другой файл конфигурации службы, задайте свойство TargetProfile команды MSBuild, как показано в следующем примере.

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Укажите расположение для данных вывода. Задайте путь с помощью параметра /p:PublishDir=*Directory*\\, включая конечный символ обратной косой черты в качестве разделителя, как показано в следующем примере.

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    После создания и тестирования соответствующей командной строки MSBuild для сборки проектов и объединения их в пакет Azure можно добавить эту команду в сценарии сборки. Если сервер сборки использует пользовательские сценарии, этот процесс будет зависеть от особенностей пользовательского процесса сборки. Если в качестве среды сборки используется TFS, вы можете следовать указаниям на следующем шаге, чтобы добавить пакет сборки Azure в процессе сборки.

## <a name="step3"> </a><span class="short-header">Создание пакета с помощью TFS </span>Шаг 3. Создание пакета с помощью TFS Team Build (дополнительно)

Если Team Foundation Server (TFS) настроен как контроллер сборки, а сервер сборки настроен как компьютер сборки TFS, вы можете настроить автоматизированную сборку для пакета Azure. Информацию о том, как настроить и использовать сервер Team Foundation
как систему сборки, см. в разделе [Общие сведения о системе сборок Team Foundation][Общие сведения о системе сборок Team Foundation]. В частности, в следующей процедуре предполагается, что сервер сборки настроен, как описано в разделе [Развертывание и настройка сервера сборки][Развертывание и настройка сервера сборки].

Чтобы настроить TFS для выполнения сборки пакетов Azure, выполните следующее:

1.  В Visual Studio на компьютере разработчика в меню «Вид» выберите **Team Explorer** или нажмите клавиши Ctrl+\\, Ctrl+M. В окне Team Explorer разверните узел **Сборки**, щелкните правой кнопкой мыши **Все определения сборок** и выберите **Создать определение сборки**:

    ![][0]

2.  Нажмите вкладку **Процесс**. На вкладке «Процесс» выберите шаблон по умолчанию, в группе «Элементы для сборки» выберите проект и разверните раздел **Дополнительно** в сетке.

3.  Выберите **Аргументы MSBuild** и задайте подходящие аргументы командной строки MSBuild в соответствии с описанием шага 2 выше. Например, введите **/t:Publish /p:PublishDir=\\\\myserver\\drops\\**, чтобы собрать пакет, и скопируйте файлы пакета в расположение
    \\\\myserver\\drops\\:

    ![][1]

    **Примечание.**Копирование файлов в общую папку упрощает развертывание пакетов с компьютера разработчика вручную.

4.  Щелкните вкладку **Триггер** и укажите необходимые условия для сборки пакетов. Например, укажите **Непрерывную интеграцию** для сборки пакета при каждом возврате из системы управления версиями.

5.  Выберите вкладку **Параметры сборки по умолчанию** и в разделе «Контроллер сборки» проверьте имя сервера сборки. Также выберите параметр **Копировать выходные данные в следующую папку для размещения сборки** и укажите нужное расположение.

6.  Протестируйте выполнение шага сборки, проверяя изменения проекта на входе, или поставьте новую сборку в очередь. Чтобы поставить в очередь новую сборку, в Team Explorer щелкните правой кнопкой мыши **Все определения сборок**, а затем выберите **Поставить новую сборку в очередь**.

## <a name="step4"> </a><span class="short-header">Публикация с помощью Powershell</span>Шаг 4. Публикация пакета с помощью сценария PowerShell

В этом разделе описывается, как создать сценарий Windows PowerShell, который будет публиковать выходные данные пакета облачного приложения в Azure, используя дополнительные параметры. Этот сценарий может быть вызван после шага сборки при автоматизации пользовательской сборки. Также он может быть вызван из действий рабочего процесса шаблона процесса в Visual Studio TFS Team Build.

1.  Установите [командлеты Azure PowerShell][командлеты Azure PowerShell] (версии 0.6.1 или более поздней).Во время установки командлета выберите установку в качестве оснастки. Обратите внимание,что эта официально поддерживаемая версия заменяет старую версию CodePlex, несмотря на то, что предыдущие версии были пронумерованы как 2.x.x.

2.  Запустите Azure PowerShell из меню «Пуск». При запуске таким способом загружаются командлеты Azure PowerShell.

3.  В командной строке PowerShell убедитесь, что командлеты PowerShell загружены с помощью ввода части команды Get-Azure и нажатия клавиши TAB для выполнения инструкции.

    Вы должны увидеть различные команды Azure PowerShell.

4.  Проверьте возможность подключения к подписке Azure, выполнив импорт информации о подписке из PUBLISHSETTINGS-файла.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings Затем дайте команду Get-AzureSubscription

    Отобразятся сведения о подписке. Убедитесь, что все работает правильно.

5.  Сохраните шаблон сценария, приведенный [в конце этой статьи][в конце этой статьи], в папке сценариев, например c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

6.  Просмотрите раздел параметров скрипта. Добавьте или измените любые значения по умолчанию. Эти значения можно всегда переопределить с помощью передачи явных параметров.

7.  Убедитесь, что созданы допустимая облачная служба и учетная запись хранения в подписке, которая может быть целевой для сценария публикации. Учетная запись хранения (хранилище больших двоичных объектов) будет использоваться для передачи и временного хранения файла конфигурации и пакета развертывания во время создания развертывания.

    -   Для создания новой облачной службы можно вызвать этот сценарий или использовать портал управления Azure. Имя облачной службы будет использоваться в качестве префикса в полном имени домена, поэтому оно должно быть уникальным.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Для создания новой учетной записи хранения можно вызвать этот сценарий или использовать портал управления Azure. Имя учетной записи хранения будет использоваться в качестве префикса в полном имени домена, поэтому оно должно быть уникальным. Попробуйте использовать то же имя, что и в облачной службе.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  Вызовите сценарий непосредственно из Azure PowerShell или подключите этот сценарий к автоматизации сборки узла, выполняемой после сборки пакета.

    **ВНИМАНИЕ!**Сценарий всегда будет удалять или заменять существующие развертывания по умолчанию, если они обнаружены. Это необходимо для включения непрерывной доставки из автоматизации, где невозможно вмешательство пользователя.

    **Пример сценария 1.** Непрерывное развертывание в промежуточной среде службы:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Это обычно сопровождается тестовым запуском проверки и замены виртуального IP-адреса. Замена виртуального IP-адреса может осуществляться через портал управления Azure или с помощью командлета Move-Deployment.

    **Пример сценария 2.** Непрерывное развертывание в рабочей среде выделенной тестовой службы:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Удаленный рабочий стол:**

    При включении удаленного рабочего стола в проекте Azure потребуется выполнить одноразовые дополнительные шаги, чтобы убедиться, что все облачные службы, являющиеся целью этого сценария, имеют правильный сертификат облачной службы.

    Найдите значения отпечатка сертификата, который ожидают роли. Значения отпечатков отображаются в разделе «Сертификаты» файла конфигурации облака (т. е. ServiceConfiguration.Cloud.cscfg). Кроме того,он отображается в диалоговом окне «Конфигурация удаленных рабочих столов» в Visual Studio при отображении параметров и просмотре выбранного сертификата.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Передайте сертификаты удаленного рабочего стола, как на этапе одноразовой настройки, с помощью следующего сценария командлета:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)lt;THUMBPRINT>)

    Например:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Можно также экспортировать PFX-файл сертификата с закрытым ключом и отправить сертификаты в каждую целевую облачную службу с помощью портала управления Azure. Дополнительную информацию см. в следующей статье:
    [<http://msdn.microsoft.com/ru-ru/library/windowsazure/gg443832.aspx>][<http://msdn.microsoft.com/ru-ru/library/windowsazure/gg443832.aspx>].

    **Сравнение развертывания обновления и удаления развертывания с созданием нового**

    По умолчанию сценарий будет выполнять развертывание обновления ($enableDeploymentUpgrade = 1) при передаче параметра или значения «1» явным образом. Для одного экземпляра это имеет преимущество, поскольку тратится меньше времени, чем на полное развертывание. Для экземпляров, которым требуется высокий уровень доступности, преимущество состоит в сохранении работы некоторых экземпляров в то время, как другие обновляются (проходя домен обновления), а также не удаляются ваши виртуальные IP-адреса.

    Развертывание обновления может быть отключено в сценарии ($enableDeploymentUpgrade = 0) или при передаче параметра -enableDeploymentUpgrade 0, который изменяет поведение сценария, сначала удаляя все существующие развертывания, а затем создавая новое развертывание.

    **Предупреждение:**Сценарий всегда будет удалять или заменять существующие
    развертывания по умолчанию, если они обнаружены. Это необходимо для включения непрерывной доставки из автоматизации, где невозможно вмешательство пользователя или оператора.

## <a name="step5"> </a><span class="short-header">Публикация с помощью TFS </span>Шаг 5. Публикация пакета с помощью TFS Team Build (дополнительно)

На этом шаге будет выполнено подключение TFS Team Build к сценарию, созданному на шаге 4, который обрабатывает публикацию сборки пакета в Azure. Это влечет за собой изменение шаблона процесса, использованного определением сборки таким образом, чтобы в конце процесса выполнялось действие Publish. Операция публикации выполнит команду PowerShell, передавая параметры из сборки. Выходные данных целевых объектов MSBuild и сценария публикации будут направлены в стандартный вывод сборки.

1.  Редактирование определение сборки, ответственного за непрерывное развертывание.

2.  Выберите вкладку **Процесс**.

3.  Создайте новый шаблон процесса, щелкнув кнопку «Создать...» в области выбора шаблона процесса на вкладке. Загрузится диалоговое окно «Создать шаблон процесса сборки». В этом диалоговом окне убедитесь, что выбран параметр «Скопировать существующий файл XAML», и найдите ProcessTemplate для копирования, если хотите изменить основу по умолчанию. Введите имя для нового шаблона, например DefaultTemplateAzure.

4.  Откройте выбранный шаблон процесса для редактирования. Его можно открыть непосредственно в конструкторе рабочих процессов или с помощью редактора XML для работы с XAML.

5.  Добавьте перечисленные ниже новые аргументы в виде отдельных элементов строк на вкладке аргументов конструктора рабочих процессов. Все аргументы должны иметь параметры direction=In и type=String. Они будут использоваться для параметров из определения сборки в рабочем процессе, который затем используется для вызова сценария публикации.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][2]

    Соответствующий код XAML выглядит следующим образом:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Добавьте новую последовательность в конце агента выполнения.

    1.  Начните с добавления действия инструкции If для проверки допустимого файла сценария. Задайте условие для этого значения.

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Для варианта Then в инструкции If добавьте новое действие последовательности. Задайте в качестве отображаемого имени "Начало публикации"

    3.  Оставив выбранной последовательность начала публикации, добавьте перечисленные ниже новые переменные в виде отдельных элементов строк на вкладке «Переменные» конструктора рабочих процессов. Все переменные должны иметь тип String и Scope=Start publish. Они будут использоваться для параметров из определения сборки в рабочем процессе, который затем используется для вызова сценария публикации.

        -   SubscriptionDataFilePath типа String.

        -   PublishScriptFilePath типа String.

            ![][3]

    4.  Добавьте действие ConvertWorkspaceItem в начале новой последовательности. Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation',+ Result='PublishScriptFilePath', Workspace='Workspace'. Это действие преобразует путь сценария публикации из расположений сервера TFS (если применимо) в стандартный локальный дисковый путь.

    5.  Добавьте другое действие ConvertWorkspaceItem в конце новой последовательности. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Добавьте действие InvokeProcess в конце новой последовательности. Это действие вызывает PowerShell.exe с аргументами, переданными определением сборки.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Выполнение сценария публикации'

        3.  FileName = "PowerShell"

        4.  OutputEncoding= 'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  В поле раздела «Обработка стандартного вывода» InvokeProcess установите значение «data» для текстового поля. Эта переменнаяпредназначена для хранения данных стандартного вывода.

    8.  Добавьте действие WriteBuildMessage прямо под разделом стандартного вывода. Задайте Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' и Message='data'. Это обеспечит запись стандартного вывода сценария в вывод сборки.

    9.  В поле раздела «Обработка стандартной ошибки» InvokeProcess установите значение «data» для текстового поля. Эта переменная предназначена для хранения данных стандартной ошибки.

    10. Добавьте действие WriteBuildError прямо под разделом стандартного вывода. Установите Message='data'. Это обеспечит запись стандартных ошибок сценария в выводе данных об ошибках сборки.

    Конечный результат действия рабочего процесса публикации будет выглядеть в конструкторе следующим образом:

    ![][4]

    Конечный результат действия рабочего процесса публикации будет выглядеть в XAML следующим образом:

            </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If.Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence.Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap:WorkflowViewStateService.ViewState>
                      <scg:Dictionary x:TypeArguments="x:String, x:Object">
                        <x:Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa:InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa:InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa:InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa:InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa:CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  Сохраните рабочий процесс DefaultTemplateAzure и зарегистрируйте этот файл.

8.  Выберите шаблон процесса DefaultTemplateAzure в определении сборки. Щелкните «Обновить» или нажмите кнопку «Создать», если вы все еще не видите этот файл в списке шаблонов процессов.

9.  Задайте для значения свойств параметров в разделе "Разное" следующим образом:

    1.  CloudConfigLocation = 'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'

        *Это значение является производным от: ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
 
        *Это значение является производным от: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = "mycloudservicename"
        *Используйте соответствующее имя облачной службы*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *Используйте имя учетной записи соответствующие хранилища*

    7.  SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][5]

10. Сохраните изменения в определении сборки.

11. Поставьте в очередь сборку для выполнения пакета сборки и публикации. Если есть триггер для установки непрерывного развертывания, это поведение будет выполняться при каждом возврате.

### <a name="script"> </a>Шаблон сценария PublishCloudService.ps1

    Param(  $serviceName = "",
            $storageAccountName = "",
            $packageLocation = "",
            $cloudConfigLocation = "",
            $environment = "Staging",
            $deploymentLabel = "ContinuousDeploy to $servicename",
            $timeStampFormat = "g",
            $alwaysDeleteExistingDeployments = 1,
            $enableDeploymentUpgrade = 1,
            $selectedsubscription = "default",
            $subscriptionDataFile = ""
         )
          

    function Publish()
    {
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
        if ($a[0] -ne $null)
        {
            Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
        }
        #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
        if ($deployment.Name -ne $null)
        {
            switch ($alwaysDeleteExistingDeployments)
            {
                1 
                {
                    switch ($enableDeploymentUpgrade)
                    {
                        1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                            UpgradeDeployment
                        }
                        0  #Delete then create new deployment
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                            DeleteDeployment
                            CreateNewDeployment
                            
                        }
                    } # switch ($enableDeploymentUpgrade)
                }
                0
                {
                    Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                    exit
                }
            } #switch ($alwaysDeleteExistingDeployments)
        } else {
                CreateNewDeployment
        }
    }

    function CreateNewDeployment()
    {
        write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

        $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
            
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid

        write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
        
        StartInstances
    }

    function UpgradeDeployment()
    {
        write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

        # perform Update-Deployment
        $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
        
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid
        
        write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
    }

    function DeleteDeployment()
    {

        write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

        #WARNING - always deletes with force
        $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

        write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
        
    }

    function StartInstances()
    {
        write-progress -id 4 -activity "Starting Instances" -status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $runstatus = $deployment.Status

        if ($runstatus -ne 'Running') 
        {
            $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
        }
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $oldStatusStr = @("") * $deployment.RoleInstanceList.Count
        
        while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
        {
            $i = 1
            foreach ($roleInstance in $deployment.RoleInstanceList)
            {
                $instanceName = $roleInstance.InstanceName
                $instanceStatus = $roleInstance.InstanceStatus

                if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
                {
                    $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
                }

                write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
                $i = $i + 1
            }

            sleep -Seconds 1

            $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        }

        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            $i = $i + 1
        }
        
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $opstat = $deployment.Status 
        
        write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
    }

    function AllInstancesRunning($roleInstanceList)
    {
        foreach ($roleInstance in $roleInstanceList)
        {
            if ($roleInstance.InstanceStatus -ne "ReadyRole")
            {
                return $false
            }
        }
        
        return $true
    }

    #configure powershell with Azure 1.7 modules
    Import-Module Azure

    #configure powershell with publishsettings for your subscription
    $pubsettings = $subscriptionDataFile
    Import-AzurePublishSettingsFile $pubsettings
    Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

    #set remaining environment variables for Azure cmdlets
    $subscription = Get-AzureSubscription $selectedsubscription
    $subscriptionname = $subscription.subscriptionname
    $subscriptionid = $subscription.subscriptionid
    $slot = $environment

    #main driver - publish & write progress to activity log
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
    Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

    Опубликовать

    $deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
    $deploymentUrl = $deployment.Url

    Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."

## Дальнейшие действия

Чтобы включить удаленную отладку при использовании непрерывной доставки, см. [эти указания][эти указания].

  [Непрерывная доставка в Azure с использованием Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Шаг 1: настройка сервера сборки]: #step1
  [Шаг 2: создание пакета с помощью команд MSBuild]: #step2
  [Шаг 4: публикация пакета с помощью сценария PowerShell]: #step4
  [Настройка службы построения Team Foundation]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [Azure Authoring Tools]: http://go.microsoft.com/fwlink/?LinkId=239600
  [библиотеки Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [справочнике по командной строке MSBuild]: http://msdn.microsoft.com/ru-ru/library/ms164311(v=VS.90).aspx
  [справочнике по командной строке MSBuild]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Общие сведения о системе сборок Team Foundation]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Развертывание и настройка сервера сборки]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [1]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [командлеты Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [в конце этой статьи]: #script
  [2]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
  [эти указания]: http://go.microsoft.com/fwlink/p/?LinkID=402354
