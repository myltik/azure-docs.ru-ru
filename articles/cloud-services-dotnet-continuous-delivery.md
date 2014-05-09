<properties linkid="dev-net-common-tasks-continuous-delivery" urlDisplayName="Непрерывная доставка" pageTitle="Непрерывная доставка для облачных служб с помощью TFS в Windows Azure" metaKeywords="Непрерывная доставка Azure, пример кода непрерывной доставки, PowerShell непрерывной доставки" description="Сведения о настройке непрерывной доставки для облачных приложений Windows Azure. Примеры кода инструкций командной строки MSBuild и скрипты PowerShell." metaCanonical="" services="" documentationCenter="" title="Непрерывная доставка для облачных служб в Windows Azure." authors=""  solutions="" writer="" manager="" editor=""  />





# Непрерывная доставка для облачных служб в Windows Azure

Процесс, описанный в этой статье демонстрирует, как настроить непрерывную доставку для облачных приложений Windows Azure. Этот процесс позволяет автоматически создавать пакеты и развертывать пакет в Windows Azure после каждого возврата кода. Процесс сборки пакета, описанный в этой статье, эквивалентен команде Package в Visual Studio. Этапы публикации эквивалентны команде Publish в Visual Studio.
В статье описаны методы, которые можно использовать для создания сервера сборки с инструкциями командной строки MSBuild и скриптами Windows PowerShell, а также показано, как при необходимости настраивать Visual Studio Team Foundation Server – определения Team Build для использования команд MSBuild и скриптов PowerShell. Этот процесс является настраиваемым для среды сборки и целевых сред Windows Azure.

Чтобы проще это сделать, можно также использовать службу Team Foundation версии TFS, размещенной в Windows Azure. Дополнительные сведения см. в разделе [Непрерывная доставка в Windows Azure с помощью службы Team Foundation][].

Прежде чем начать, следует опубликовать приложение из Visual Studio.
Это обеспечит доступность и инициализацию всех ресурсов при попытке автоматизировать процесс публикации.

Эта задача включает следующие шаги:

-   [Шаг 1. Настройка сервера сборки][]
-   [Шаг 2. Создание пакета с помощью команд MSBuild][]
-   [Шаг 3. Создание пакета с помощью TFS Team Build (дополнительно)][]
-   [Шаг 4. Публикация пакета с помощью скрипта PowerShell][]
-   [Шаг 5. Публикация пакета с помощью TFS Team Build (дополнительно)][]

<h2> <a name="step1"> </a><span class="short-header">Настройка сервера сборки</span>Шаг 1. Настройка сервера сборки</h2>

Перед созданием пакета Windows Azure с помощью MSBuild следует установить необходимое программное обеспечение и средства на сервере сборки.

Visual Studio необязательно должна быть установлена на сервере сборки. Если вы хотите использовать для управления сервером сборки службу сборки Team Foundation, следуйте инструкциям в разделе [Служба сборки Team Foundation][].
 

1.  На сервере сборки установите платформу [.NET Framework 4][], которая включает MSBuild.
2.  Установите [средства создания Windows Azure][] (см. WindowsAzureAuthoringTools x86.msi или WindowsAzureAuthoringTools-x64.msi, в зависимости от процессора сервера сборки).
3.  Скопируйте файл Microsoft.WebApplication.targets из установки Visual Studio на сервер сборки. На компьютере с установленной Visual Studio файл располагается в каталоге C:\\Program (x86)\\MSBuild\\Microsoft\\VisualStudio\\v10.0\\WebApplications (v11.0 для Visual Studio 2012). Нужно скопировать его в тот же каталог на сервере сборки.
4.  Установите [Средства Windows Azure для Visual Studio][].
    См. WindowsAzureTools.VS110.exe.

<h2><a name="step2"> </a><span class="short-header">Создание пакета с помощью MSBuild</span>Шаг 2. Создание пакета с помощью команд MSBuild</h2>

В этом разделе описывается создание команды MSBuild, которая выполняет сборку пакета Windows Azure. Выполните этот шаг на сервере сборки, чтобы проверить правильность настройки, и команда MSBuild выполнит все необходимое. Эту команду можно добавить в командную строку либо добавить в существующие скрипты сборки на сервере сборки или из командной строки в определение сборки TFS, как описано в следующем разделе. Дополнительные сведения о параметрах командной строки и MSBuild см. в разделе [MSBuild Справочник по командной строке][].

1.  Если Visual Studio установлена на сервере сборки, щелкните
    **Пуск**, **Все программы**, найдите и щелкните
    **Visual Studio Commmand Prompt** в папке **Visual Studio Tools**.

    Если Visual Studio не установлена на сервере сборки, откройте
    командную строку и убедитесь, что файл MSBuild.exe
    доступен. MSBuild устанавливается с платформой .NET Framework в пути   
    %WINDIR%\\Microsoft.NET\\Framework\\*&lt;Version\&gt;*. Например, чтобы добавить файл MSBuild.exe в переменную среды PATH при наличии платформы .NET Framework 4 (SP4), введите следующую команду в командной строке:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\4.0"

2.  В командной строке перейдите в папку, содержащую файл проекта Windows Azure, которую требуется собрать.

3.  Запустите msbuild с параметром /target:Publish, как показано ниже:

        MSBuild /target:Publish

    Этот параметр можно сократить: /t:Publish. Параметр /t:Publish в MSBuild не следует путать с командами публикации, доступными в Visual Studio, если у вас установлен пакет Windows Azure SDK. Параметр /t:Publish собирает только пакеты Windows Azure. Он не осуществляет развертывание пакетов как команды публикации в Visual Studio.

    Дополнительно можно указать имя проекта как параметр MSBuild. Если не указано, используется текущий каталог. Дополнительные сведения о параметрах командной строки MSBuild см. в разделе [MSBuild Справочник по командной строке][1].

4.  Найдите данные вывода. По умолчанию эта команда создает каталог в зависимости от
     корневой папки для проекта, например
    *&lt;ProjectDir&gt;*\\bin\\*&lt;Configuration&gt;*\\app.publish\\. При сборке проекта Windows Azure создается два файла – файл пакета и сопутствующий файл конфигурации:

    -   Project.cspkg
    -   ServiceConfiguration.*&lt;TargetProfile&gt;*.cscfg

    По умолчанию каждый проект Windows Azure включает один файл конфигурации службы (файл .cscfg) для локальных сборок (отладка) и другой для сборки облака (промежуточной или рабочей), но можно добавить или удалить файлы конфигурации службы при необходимости. При сборке пакета в Visual Studio будет запрос о выборе файла конфигурации службы для включения в пакет.

5.  Укажите файл конфигурации службы. При сборке пакета с помощью MSBuild по умолчанию включается файл конфигурации локальной службы. Чтобы включить файл конфигурации другой службы, установите свойство
    TargetProfile команды MSBuild, как показано ниже:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Укажите расположение для данных вывода. Установите путь с помощью параметра
    /p:PublishDir=*&lt;Directory\&gt;*\\, включив
    разделитель обратной косой черты, как показано ниже:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    После создания и тестирования соответствующей командной строки MSBuild для сборки проектов и объединения их в пакет Windows Azure можно добавить эту команду в скрипты сборки. Если сервер сборки использует пользовательские скрипты, этот процесс будет зависеть от особенностей пользовательского процесса сборки. При использовании TFS в качестве среды сборки необходимо выполнить инструкции на следующем шаге для добавления пакета сборки Windows Azure в процессе сборки.

<h2> <a name="step3"> </a><span class="short-header">Создание пакета с помощью TFS </span>Шаг 3. Создание пакета с помощью TFS Team Build (дополнительно)</h2>

Если Team Foundation Server (TFS) настроен как контроллер сборки и сервер сборки настроен как компьютер сборки TFS, а затем можно настроить автоматическую сборку для пакета Windows Azure. Сведения о том, как настроить и использовать сервер Team Foundation как систему сборки см. в разделе
[Общие сведения о системе сборки Team Foundation][]. В частности, в следующей процедуре предполагается, что сервер сборки настроен, как описано в разделе [Настройка машины сборки][].

Чтобы настроить TFS для сборки пакетов Windows Azure выполните следующие шаги:

1.  В Visual Studio 2010 на компьютере разработчика, в меню "Вид"
    выберите **Team Explorer** или Ctrl+\\, Ctrl+M. В окне
    Team Explorer раскройте узел **Сборки**, щелкните правой кнопкой **Все определения
    сборок** и выберите **Создать определение сборки**:

    ![][0]

2.  Нажмите вкладку **Процесс**. На вкладке "Процесс" выберите шаблон по умолчанию, в группе "Элементы для сборки" выберите проект и разверните раздел **Дополнительно** в сетке. 

3.  Выберите аргументы **MSBuild** и установите соответствующие аргументы командной строки MSBuild, как описано выше в шаге 2. Например,
    введите **/t:Publish /p:PublishDir=\\\\myserver\\drops\\**, чтобы собрать
    пакет и скопируйте файлы пакета в расположение
    \\\\myserver\\drops\\:

    ![][2]

    **Примечание.** Копирование файлов в общую папку упрощает развертывание пакетов с компьютера разработчика вручную.

4.  Щелкните вкладку **Триггер** и укажите необходимые условия для сборки пакетов. Например, укажите
    **Непрерывную интеграцию** для сборки пакета при каждом возврате.

5.  Выберите вкладку **Параметры сборки по умолчанию** вкладке и в параметре "Контроллер сборки" проверьте имя сервера сборки.  Также выберите параметр **Копировать выходные данные в следующую папку для размещения сборки** и укажите нужное расположение.

5.  Проверьте выполнение шага сборки по изменению проекта или поставьте в очередь новую сборку. Чтобы поставить в очередь новую сборку, в Team Explorer щелкните правой кнопкой мыши **Все определения сборок**, а затем выберите **Поставить в очередь новую сборку**.

<h2> <a name="step4"> </a><span class="short-header">Публикация с помощью Powershell</span>Шаг 4. Публикация пакета с помощью скрипта PowerShell</h2>

В этом разделе описывается, как создать скрипт Windows PowerShell, который будет публиковать выходные данные пакета облачного приложения в Windows Azure, используя дополнительные параметры. Этот скрипт может быть вызван после шага сборки при автоматизации пользовательской сборки. Также он может быть вызван из действий рабочего процесса шаблона процесса в Visual Studio TFS Team Build.

1.  Установка [командлетов PowerShell Windows Azure][] (v0.6.1 или более поздней версии).
    Во время установки командлета выберите установку в качестве оснастки. Обратите внимание, что эта официально поддерживаемая версия заменяет старую версию CodePlex, несмотря на то что предыдущие версии были пронумерованы 2.x.x.

2.  Запустите PowerShell Windows Azure с помощью меню "Пуск". При запуске таким образом будут загружены командлеты PowerShell Windows Azure.

3.  В командной строке PowerShell убедитесь, что загружены командлеты PowerShell посредством ввода частичной команды Get-Azure и нажатия клавиши TAB для завершения инструкций.

    Вы должны увидеть различные команды PowerShell в Windows Azure.

4.  Проверьте подключение для подписки Windows Azure путем импорта из сведений о подписке в файле .publishsettings.

    Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings

    Затем дайте команду

    Get-AzureSubscription

    Отобразятся сведения о подписке. Убедитесь, что все работает правильно.

4.  Сохраните шаблон скрипта, приведенный [в конце этой статьи][] в
    папке скриптов, например
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Просмотрите раздел параметров скрипта. Добавьте или измените любые значения по умолчанию. Эти значения можно всегда переопределить с помощью передачи четких параметров.

6.  Убедитесь, что созданы допустимые облачная служба и учетная запись хранения в подписке, которые может сориентировать скрипт публикации. Учетная запись хранения (хранилище BLOB-объектов) будет использоваться для загрузки и временного хранения файла конфигурации и пакета развертывания во время создания развертывания.

    -   Создать и развернуть облачную службу можно с помощью этого скрипта или на портале управления Windows Azure. Имя службы облаков будет использоваться в качестве префикса в полном имени домена и поэтому должно быть уникально.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Создать и развернуть учетную запись хранения можно с помощью этого скрипта или на портале управления Windows Azure. Имя учетной записи хранения будет использоваться в качестве префикса в полном имени домена и поэтому должно быть уникально. Попробуйте использовать то же имя, что и в облачной службе.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Вызовите скрипт непосредственно из PowerShell Windows Azure или подключите этот скрипт к автоматизации сборки узла после сборки пакета.

    **Внимание!** Скрипт всегда будет удалять или заменять существующие развертывания по умолчанию, если они обнаружены. Это необходимо для включения непрерывной доставки из автоматизации, где невозможно вмешательство пользователя.

    **Пример сценария 1.** Непрерывное развертывание в промежуточной среде службы:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Это обычно сопровождается тестовым запуском проверки и замены VIP. Замена VIP может осуществляться через портал управления Windows Azure или при помощи командлета Move-Deployment.

    **Пример сценария 2.** Непрерывное развертывание в рабочей среде выделенной тестовой службы:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Удаленный рабочий стол:**

    При включении удаленного рабочего стола в проекте Windows Azure будет необходимо выполнить одноразовые дополнительные шаги, чтобы убедиться, что все облачные службы, которым этот скрипт передан, имеют правильный сертификат службы облаков.

    Найдите значения отпечатка сертификата, который ожидают роли. Значения отпечатков отображаются в разделе "Сертификаты" файла конфигурации облака (т.е. ServiceConfiguration.Cloud.cscfg). Кроме того, он отображается в диалоговом окне "Конфигурация удаленных рабочих столов" в Visual Studio при отображении параметров и просмотра выбранного сертификата.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Загрузите сертификаты удаленного рабочего стола, как на этапе одноразовой настройки, с помощью следующего скрипта командлета:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Например:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Также можно экспортировать файл сертификата PFX закрытым ключом и загрузить сертификаты в каждую целевая облачную службу с помощью портала управления Windows Azure. См. следующие статьи для получения дополнительных сведений:
    [http://msdn.microsoft.com/ru-ru/library/windowsazure/gg443832.aspx][]

    **Развертывание обновления и развертывания удаления -\> Новое развертывание**

    По умолчанию скрипт будет выполнять развертывание обновления ($enableDeploymentUpgrade = 1) при передаче параметра или значения 1 явным образом. Для одного экземпляра это имеет преимущество, поскольку тратится меньше времени, чем на полное развертывание. Для экземпляров, которым требуется высокий уровень доступности, это также имеет преимущество сохранения работы некоторых экземпляров в то время, как другие обновляются (проходя домен обновления), а также к VIP не будут удалены.

    Развертывание обновления может быть отключено в скрипте ($enableDeploymentUpgrade = 0) или при передаче параметра -enableDeploymentUpgrade 0, который изменяет поведение скрипта, сначала удаляя все существующие развертывания, а затем создавая новое развертывание.

    **Внимание!** Скрипт всегда будет удалять или заменять существующие развертывания по умолчанию, если они обнаружены. Это необходимо для включения непрерывной доставки из автоматизации, где невозможно вмешательство пользователя/оператора.

<h2><a name="step5"> </a><span class="short-header">Публикация с помощью TFS </span>Шаг 5. Публикация пакета с помощью TFS Team Build (дополнительно)</h2>

На этом шаге будет выполнено соединение TFS Team Build со скриптом, созданным на шаге 4, который обрабатывает публикации сборки пакета в Windows Azure. Это влечет за собой изменения в шаблон процесса, использованный определением сборки таким образом, чтобы в конце процесса выполнялись действия публикации. Операция публикации выполнит команду PowerShell, передавая параметры из сборки. Выводы целей MSBuild и скриптов публикации будут направлены в стандартный вывод сборки.

1.  Редактирование определение сборки, ответственного за непрерывное развертывание.

2.  Выберите вкладку **Процесс**.

3.  Создайте новый шаблон процесса, щелкнув кнопку "Создать..." в области выбора шаблона процесса на вкладке. Загрузится диалоговое окно "Создать шаблон процесса сборки". В этом диалоговом окне убедитесь, что выбран параметр "Копировать существующий XAML-файл" и найдите ProcessTemplate для копирования, если хотите изменить основу по умолчанию. Введите имя для нового шаблона, например DefaultTemplateAzure.

4.  Откройте выбранный шаблон процесса для редактирования. Его можно открыть непосредственно в конструкторе рабочих процессов или с помощью редактора XML для работы с XAML.

5.  Добавьте перечисленные ниже новые аргументы в виде отдельных элементов строк на вкладке аргументы конструктора рабочих процессов. Все аргументы должны иметь параметры direction=In и type=String. Это будет использоваться для параметров из определения сборки в рабочем процессе, который затем используется для вызова скрипта публикации.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][3]

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

    1.  Начните с добавления действия оператора If для проверки допустимого файла скрипта. Задайте условие для этого значения.

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  В случае Then оператора If добавьте новую последовательность действий. Задайте отображаемое имя для "Начала публикации"

    3.  С выбранной последовательностью начала публикации добавьте перечисленные ниже новые переменные в виде элементов в отдельных строках на вкладке "Переменные" конструктора рабочих процессов. Все переменные должны иметь тип переменной =String и Scope=Start publish. Это будет использоваться для параметров из определения сборки в рабочем процессе, который затем используется для вызова скрипта публикации.

        -   SubscriptionDataFilePath, of type String

        -   PublishScriptFilePath, of type String

            ![][4]

    4.  Добавьте действие ConvertWorkspaceItem в начале новой последовательности. Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Это действие преобразует путь скрипта публикации от расположений сервера TFS (если применимо) в стандартный локальный дисковый путь.

    5.  Добавьте другое действие ConvertWorkspaceItem в конце новой последовательности. Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Добавьте действие InvokeProcess в конце новой последовательности.
        Это действие вызывает PowerShell.exe с аргументами, переданными по определению сборки.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Выполнение скрипта публикации'

        3.  FileName = "PowerShell"

        4.  OutputEncoding=
            'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  В поле раздела "Обработка стандартного вывода" InvokeProcess установите значение "data" для текстового поля. Эта переменная предназначена для хранения данных стандартного вывода.

    8.  Добавьте действие WriteBuildMessage под раздел стандартного вывода. Установите Importance =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        и Message='data'. Это обеспечит запись стандартного вывода скрипта в вывод сборки.

    9.  В поле раздела "Обработка стандартной ошибки" InvokeProcess установите значение "data" для текстового поля. Эта переменная предназначена для хранения данных стандартной ошибки.

    10. Добавьте действие WriteBuildError под раздел стандартного вывода. Установите Message='data'. Это обеспечит запись стандартных ошибок скрипта в выводе данных об ошибках сборки.

    Конечный результат действия рабочего процесса публикации будет выглядеть следующим образом в конструкторе:

    ![][5]

    Конечный результат действия рабочего процесса публикации будет выглядеть следующим образом в XAML:

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
                        <x:Boolean x:Key="IsExpanded">Истина</x:Boolean>
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

8.  Выберите шаблон процесса DefaultTemplateAzure в определении сборки. Щелкните "Обновить" или кнопку "Создать", если вы все еще не видите этот файл в списке шаблонов процессов.

9.  Задайте для значения свойств параметров в разделе "Разное" следующим образом:

    1.  CloudConfigLocation =
        'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'   
        *Это значение является производным от:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation =
        'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'   
        *Это значение является производным от: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation =
        'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = "mycloudservicename"   
        *Используйте соответствующее имя облачной службы*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'   
        *Используйте имя учетной записи соответствующие хранилища*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. Сохраните изменения в определении сборки.

11. Поставьте в очередь сборку для выполнения пакета сборки и публикации. Если есть триггер для установки непрерывного развертывания, это поведение будет выполняться при каждой регистрации.

### <a name="script"> </a>Шаблон скрипта PublishCloudService.ps1

<pre>
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

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
</pre>

  [Непрерывная доставка в Windows Azure с помощью Team Foundation Service]: ../cloud-services-continuous-delivery-use-vso/
  [Шаг 1. Настройка сервера сборки]: #step1
  [Шаг 2. Создание пакета с помощью команд MSBuild]: #step2
  [Шаг 3. Создание пакета с помощью TFS Team Build (дополнительно)]: #step3
  [Шаг 4. Публикация пакета с помощью скрипта PowerShell]: #step4
  [Шаг 5. Публикация пакета с помощью TFS Team Build (дополнительно)]: #step5
  [Служба Team Foundation Build Service]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [Средства создания Windows Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Пакет Windows Azure Tools для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Справочник по командной строке MSBuild]: http://msdn.microsoft.com/ru-ru/library/ms164311(v=VS.90).aspx
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Общие сведения о системе сборки Team Foundation]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Настройка машины сборки]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [Командлеты Windows Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [файл .publishsettings.]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [конец статьи]: #script
  
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png

