<properties 
	pageTitle="Непрерывная отправка для облачных служб с помощью TFS в Azure" 
	description="Узнайте, как настраивать непрерывную отправку для облачных приложений Azure. Примеры программного кода для инструкций командной строки MSBuild и сценариев PowerShell." 
	services="cloud-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="kempb"/>

# Непрерывная доставка для облачных служб в Azure

Описанный в этой статье процесс показывает, как настроить непрерывную
доставку для облачных приложений Azure. Этот процесс позволяет вам
автоматически создавать пакеты и выполнять развертывание пакета в Azure
после каждого применения кода. Процесс сборки пакета, описанный в
этой статье, аналогичен принципу действия команды Package в Visual Studio, а
шаги публикации - принципу действия команды Publish в Visual Studio.
В статье описаны методы, которые можно использовать для создания сервера сборки
с помощью операторов командной строки MSBuild и сценариев Windows PowerShell, а
также показано, как дополнительно настроить Visual Studio Team
Foundation Server - определения Team Build для использования команд MSBuild
и сценариев PowerShell. Процесс можно настраивать для среды сборки
и целевых сред Azure.

Для упрощения задачи можно также использовать службу Visual Studio Online - версию TFS, размещенную в Azure. Дополнительные сведения см. в разделе [Непрерывная доставка в Azure с использованием Visual Studio Online][].

Прежде чем начать, следует опубликовать приложение из Visual Studio.
Это обеспечит доступность и инициализацию всех ресурсов при 
при попытке автоматизировать процесс публикации.

Эта задача включает следующие шаги:

-   [Шаг 1. настройка сервера сборки](#step1)
-   [Шаг 2. создание пакета с помощью команд MSBuild](#step2)
-   [Шаг 3. создание пакета с помощью TFS Team Build (дополнительно)](#step3)
-   [Шаг 4. публикация пакета с помощью сценария PowerShell](#step4)
-   [Шаг 5. публикация пакета с помощью TFS Team Build (дополнительно)][]

<h2> <a name="step1"> </a>Шаг 1. Настройка сервера сборки</h2>

Прежде чем создать пакет Azure с помощью MSBuild, вам следует
установить необходимое программное обеспечение и инструменты на сервере сборки.

Visual Studio необязательно должна быть установлена на сервере сборки. Если
вы хотите использовать для управления сервером сборки службу сборок
Team Foundation, следуйте указаниям в документации по [службе сборок Team Foundation][]
документация.

1.  На сервере сборки установите [.NET Framework 4][], [.NET Framework 4.5][] или [.NET Framework 4.5.2][], которые включают в себя MSBuild.
2.  Установите [инструменты создания Azure](http://go.microsoft.com/fwlink/?LinkId=239600) (ищите MicrosoftAzureAuthoringTools-x86.msi или MicrosoftAzureAuthoringTools-x64.msi в зависимости от процессора сервера сборки). Более старые версии файлов могут содержать в имени файла WindowsAzure.
3. Установите [библиотеки Azure](http://go.microsoft.com/fwlink/?LinkId=257862) (ищите MicrosoftAzureLibsForNet-x86.msi или MicrosoftAzureLibsForNet-x64.msi).
4.  Скопируйте файл Microsoft.WebApplication.targets из установленного экземпляра Visual Studio на сервер сборки. На компьютере с установленным экземпляром Visual Studio этот файл находится в каталоге C:\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (версия 12.0 для Visual Studio 2013). Нужно скопировать его в тот же каталог на сервере сборки.
5.  Установите [инструменты Azure для Visual Studio](http://go.microsoft.com/fwlink/?LinkId=257862). Для выполнения сборки проектов Visual Studio 2012 ищите MicrosoftAzureTools.VS110.exe, для проектов Visual Studio 2013 - MicrosoftAzureTools.VS120.exe, а для проектов предварительной версии Visual Studio 2015 - MicrosoftAzureTools.VS140.exe.

<h2><a name="step2"> </a>Шаг 2. Создание пакета с помощью команд MSBuild</h2>

В этом разделе описывается создание команды MSBuild, которая выполняет сборку
пакета Azure. Выполните этот шаг на сервере сборки, чтобы убедиться, что
все настроено правильно и команда MSBuild выполняет
необходимые вам действия. Вы можете добавить эту командную строку в существующие
сценарии сборки на сервере сборки или использовать ее в
определении сборки TFS, как описано в следующем разделе. Дополнительную
информацию о параметрах командной строки и MSBuild см. в разделе [Справочник по командной строке MSBuild][].

1.  Если Visual Studio установлена на сервере сборки, последовательно щелкните
    **Пуск**, **Все программы**, а затем найдите и щелкните 
    **Командная строка Visual Studio** в папке **Инструменты Visual Studio**
    .

    Если Visual Studio установлена на сервере сборки, откройте
    командную строку и убедитесь в том, что файл MSBuild.exe доступен
    путь. MSBuild устанавливается вместе с .NET Framework по пути   
    %WINDIR%\\Microsoft.NET\\Framework\\*версия*. Например, чтобы
    добавить файл MSBuild.exe в переменную среды PATH при наличии платформы .NET
    Framework 4, введите следующую команду в командной
    строке:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  В командной строке перейдите к папке, содержащей
    файл проекта Azure, который вам необходимо создать.

3.  Запустите msbuild с параметром /target:Publish, как показано в следующем
    Пример:

        MSBuild /target:Publish

    Этот параметр можно сократить до /t:Publish. Параметр /t:Publish
    в MSBuild не следует путать с командами Publish,
    которые доступны в Visual Studio при наличии установленного пакета SDK для Azure
    установлено. Параметр /t:Publish собирает только
    пакеты Azure. Он не осуществляет развертывание пакетов, как команды Publish в
    Visual Studio.

    Дополнительно можно указать имя проекта в качестве параметра MSBuild
    параметр. Если не указано, используется текущий каталог. Дополнительную
    информацию о параметрах командной строки MSBuild см. в [справочнике по командной строке MSBuild
    ][1].

4.  Найдите данные вывода. По умолчанию эта команда создает каталог
    относительно корневой папки проекта, например
    *ProjectDir*\\bin\\*Configuration*\\app.publish\\. При
    выполнении сборки проекта Azure вы создаете два файла -
    собственно файл пакета и сопутствующий файл конфигурации:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    По умолчанию каждый проект Azure включает в себя один
    файл конфигурации службы (CSCFG-файл) для локальных
    сборок (отладка) и еще один файл для облачных сборок (промежуточная или рабочая среда). Тем не менее, вы
    можете добавлять или удалять файлы конфигурации службы при необходимости. При
    выполнении сборки пакета в Visual Studio вам будет предложено выбрать
    файл конфигурации службы, который будет добавлен в пакет.

5.  Укажите файл конфигурации службы. При сборке пакета с
    использованием MSBuild по умолчанию добавляется файл конфигурации локальной службы
    по умолчанию. Чтобы включить в пакет другой файл конфигурации службы, задайте
    свойство TargetProfile команды MSBuild, как показано в следующем
    Пример:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Укажите расположение для данных вывода. Задайте путь с помощью
    параметра /p:PublishDir=*Directory*\\, добавив завершающий
    разделитель обратной косой черты, как показано в следующем примере:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    После того, как вы создали и протестировали соответствующую командную строку MSBuild,
    чтобы выполнить сборку проектов и объединить их в пакет Azure,
    вы можете добавить эту командную строку в сценарии сборки. Если ваш сервер сборки
    использует пользовательские сценарии, этот процесс будет зависеть от
    особенностей пользовательского процесса сборки. Если вы используете TFS в качестве
    среды сборки, следуйте указаниям на следующем
    шаге, чтобы добавить сборку пакета Azure в процесс сборки.

<h2> <a name="step3"> </a>Шаг 3. Создание пакета с помощью TFS Team Build (необязательно)</h2>

Если ваш сервер Team Foundation Server (TFS) настроен как контроллер сборки,
а сервер сборки - как компьютер сборки TFS, вы можете настроить
автоматизированную сборку для пакета Azure. Дополнительную информацию о
настройке и использовании сервера Team Foundation в качестве системы сборки см. в разделе
[Общие сведения о системе сборки Team Foundation][]. В частности,
в следующей процедуре подразумевается, что вы настроили сервер сборки,
как описано в разделе [Развертывание и настройка сервера сборки][], а также создали
командный проект, а в нем - проект облачной службы.

Чтобы настроить TFS для выполнения сборки пакетов Azure, выполните
шаги.

1.  В Visual Studio на компьютере разработчика в меню "Вид"
    выберите **Team Explorer** или нажмите Ctrl+\\, Ctrl+M. В
    окне "Командный обозреватель" разверните узел **Сборки** или откройте страницу **Сборки**
    и выберите **Создать определение сборки**.

    ![][0]

2.  Выберите вкладку **Триггер** и укажите необходимые условия
    для времени создания пакета. Например, укажите
    **Непрерывная интеграция** для сборки пакета при каждом
    возврате из системы управления версиями.

3.	Выберите вкладку **Параметры исходного кода** и убедитесь, что папка вашего проекта есть
	в столбце **Папка системы управления версиями**, а для состояния задано значение **Активно**.

4.  Выберите вкладку **Параметры сборки по умолчанию** и в разделе "Контроллер сборки" проверьте
    имя сервера сборки.  Кроме того, выберите параметр **Копировать выходные данные 
    построения в следующую папку для размещения** и укажите нужное
    расположение.

5.  Нажмите вкладку **Процесс**. На вкладке "Процесс" в
    разделе **Сборка** выберите шаблон по умолчанию, а затем выберите проект, если он еще не выбран,
    и разверните раздел **Дополнительно** в разделе сетки **Сборка**. 

6.  Выберите **Аргументы MSBuild** и задайте соответствующие
    аргументы командной строки MSBuild, как описано на шаге 2 выше. Например,
    введите **t:Publish /p:PublishDir=\\\\myserver\\drops\\**, чтобы выполнить сборку
    пакета, и скопируйте файлы пакета в расположение
    \\\\myserver\\drops\\:

    ![][2]

    **Примечание.** Копирование файлов в общую папку упрощает
    развертывание пакетов с компьютера разработчика вручную.

5.  Протестируйте выполнение шага сборки, проверяя изменения
    проекта на входе, или поставьте новую сборку в очередь. Чтобы поставить в очередь новую сборку, в
    командном обозревателе щелкните правой кнопкой мыши **Все определения сборок**, а затем
    выберите **Поставить новую сборку в очередь**.

<h2> <a name="step4"> </a>Шаг 4. Публикация пакета с помощью сценария PowerShell</h2>

В этом разделе описывается, как создать сценарий Windows PowerShell, который
будет публиковать выходные данные пакета облачного приложения в Azure, используя
необязательные параметры. Этот сценарий может быть вызван после шага сборки при
автоматизации пользовательской сборки. Также он может быть вызван из действий рабочего процесса
шаблона процесса в Visual Studio TFS Team Build.

1.  Установите [командлеты Azure PowerShell](http://go.microsoft.com/fwlink/?LinkId=256262) (версии 0.6.1 или более поздней).
    Во время установки командлета выберите установку в качестве оснастки. Примечание.
    Обратите внимание, что эта официально поддерживаемая версия заменяет старую версию,
    предоставляемую через CodePlex, хотя предыдущие версии были пронумерованы как 2.x.x.

2.  Запустите Azure PowerShell из меню "Пуск" или с начальной страницы. При запуске таким способом
    загружаются командлеты Azure PowerShell.

3.  В командной строке PowerShell убедитесь, что командлеты PowerShell загружены
    путем ввода части команды Get-Azure и нажатия клавиши TAB для
    выполнения оператора.

    Если несколько раз нажать клавишу TAB, вы увидите несколько команд Azure PowerShell.

4.  Проверьте возможность подключения к подписке Azure,
    выполнив импорт информации о подписке из PUBLISHSETTINGS-файла.

    Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings

    Затем дайте команду

    Get-AzureSubscription

    Отобразятся сведения о подписке. Убедитесь, что все работает правильно.

4.  Сохраните шаблон сценария, приведенный [в конце этой статьи][] в
    в папке сценариев, например
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Просмотрите раздел параметров скрипта. Добавьте или измените любые
    значения по умолчанию. Эти значения можно всегда переопределить с помощью передачи
    явных параметров.

6.  Убедитесь, что созданы допустимая облачная служба и учетные записи хранения
    в подписке, которая может быть целевой для сценария публикации. 
    Учетная запись хранения (хранилище больших двоичных объектов) будет использоваться для передачи и
    временного хранения файла конфигурации и пакета развертывания во время
    создания развертывания.

    -   Для создания новой облачной службы можно вызвать этот сценарий или использовать
        портал управления Azure. Имя облачной службы
        будет использоваться в качестве префикса в полном доменном имени, поэтому
        оно должно быть уникальным.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Для создания новой учетной записи хранения можно вызвать этот сценарий или использовать
        портал управления Azure. Имя учетной записи хранения
        будет использоваться в качестве префикса в полном доменном имени, поэтому
        оно должно быть уникальным. Попробуйте использовать то же имя, что и в
        облачная служба.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Вызовите сценарий непосредственно из Azure PowerShell или подключите его
    к автоматизации сборки узла, выполняемой после сборки
    сборка.

    **ПРЕДУПРЕЖДЕНИЕ.** Сценарий всегда будет удалять или заменять существующие
    развертывания по умолчанию, если они обнаружены. Это необходимо для
    включения непрерывной доставки из автоматизации, при которой невозможно вмешательство
    пользователя.

    **Пример сценария 1.** Непрерывное развертывание в промежуточной
    среде службы:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Это обычно сопровождается тестовым запуском проверки и замены виртуального IP-адреса
    IP-адреса. Замена виртуального IP-адреса может осуществляться через портал управления
    Azure или с помощью командлета Move-Deployment.

    **Пример сценария 2.** Непрерывное развертывание в рабочей
    среде выделенной службы тестирования.

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Удаленный рабочий стол:**

    Если удаленный рабочий стол включен в проекте Azure, вам потребуется
    выполнить одноразовые дополнительные шаги, чтобы убедиться, что для всех
    облачных служб,являющихся целью этого сценария, загружен правильный
    сертификат облачной службы.

    Найдите значения отпечатка сертификата, который ожидают роли. 
    Значения отпечатков отображаются в разделе Certificates
    файла конфигурации облака (т. е. ServiceConfiguration.Cloud.cscfg). Обычно
    он отображается в диалоговом окне "Конфигурация удаленных рабочих столов" в Visual
    Studio при отображении параметров и просмотре выбранного сертификата.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Передайте сертификаты удаленного рабочего стола, как на этапе одноразовой настройки, с помощью
    следующего сценария командлета:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Например:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Можно также экспортировать PFX-файл сертификата с закрытым
    ключом и отправить сертификаты в каждую целевую облачную службу с помощью
    портала управления Azure. Дополнительную информацию см. в следующей статье:
    в следующем разделе:
    [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx][].

    **Сравнение обновления развертывания и удаления развертывания с созданием нового**

    По умолчанию сценарий будет выполнять развертывание обновления
    ($enableDeploymentUpgrade = 1), если параметры не передаются или
    значение "1" передается явным образом. Для одного экземпляра это является
    преимуществом, поскольку тратится меньше времени, чем на полное развертывание. Для экземпляров,
    которым требуется высокий уровень доступности, преимущество состоит в том,
    что некоторые экземпляры продолжают работать в то время, как другие обновляются (проходя
    домен обновления), и виртуальные IP-адреса не удаляются.

    Развертывание обновления можно отключить в сценарии
    ($enableDeploymentUpgrade = 0) или путем передачи параметра
    enableDeploymentUpgrade 0, который изменяет
    поведение сценария таким образом, чтобы он сначала удалял все существующие развертывания, а затем
    создание нового развертывания.

    **Предупреждение.** Сценарий всегда будет удалять или заменять существующие
    развертывания по умолчанию, если они обнаружены. Это необходимо для
    включение непрерывной отправки из автоматизации, при которой невозможно вмешательство пользователя или оператора
    пользователя или оператора.

<h2><a name="step5"> </a>Шаг 5. Публикация пакета с помощью TFS Team Build (необязательно)</h2>

На этом шаге будет выполнено подключение TFS Team Build к сценарию, созданному на шаге 4,
который обрабатывает публикации сборки пакета в Azure. В этом
влечет за собой изменение шаблона процесса, использованного определением сборки таким образом,
чтобы в конце рабочего процесса выполнялось действие Publish. Действие
выполнит команду PowerShell, передавая параметры из
сборки. Выходные данные целевых объектов MSBuild и сценария публикации будут
направлены в стандартный вывод сборки.

1.  Измените определение сборки, отвечающее за непрерывное развертывание.

2.  Выберите вкладку **Процесс**.

3.	Выполните [следующие указания](http://msdn.microsoft.com/library/dd647551.aspx), чтобы добавить
    проект Activity для шаблона процесса сборки, скачать шаблон по умолчанию, добавить его в
	проект и применить. Присвойте шаблону процесса сборки новое имя, например
	AzureBuildProcessTemplate.

3.  Вернитесь на вкладку **Процесс** и выберите пункт **Показать подробности**, чтобы просмотреть список доступных
	шаблонов процесса сборки. Нажмите кнопку **Создать...** и перейдите к проекту,
	который вы только что добавили и применили. Найдите только что созданный шаблон и нажмите кнопку **ОК**.

4.  Откройте выбранный шаблон процесса для редактирования. Его можно открыть
    непосредственно в конструкторе рабочих процессов или с помощью редактора XML для работы с
    XAML.

5.  Добавьте перечисленные ниже новые аргументы в виде отдельных элементов строк
    на вкладке аргументов конструктора рабочих процессов. Все аргументы должны
    содержать параметры direction=In и type=String. Это необходимо для
    передачи параметров из определения сборки в рабочем процессе, который затем
    используется для вызова сценария публикации.

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

    1.  Начните с добавления действия инструкции If для проверки допустимого
        файла сценария. Задайте условие для этого значения.

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Для варианта Then в инструкции If добавьте новое действие
        последовательности. Задайте в качестве отображаемого имени  'Start publish'.

    3.  Оставив выбранной последовательность начала публикации, добавьте
        перечисленные ниже новые переменные в виде отдельных элементов строк на
        на вкладке переменных конструктора рабочих процессов. Все переменные должны
        должны иметь тип String и Scope=Start publish. Это необходимо
        для передачи параметров из определения сборки в
        рабочем процессе, который затем используется для вызова сценария публикации.

        -   SubscriptionDataFilePath типа String.

        -   PublishScriptFilePath типа String.

            ![][4]

    4.  Если вы используете TFS 2012 или более раннюю версию, добавьте в начало новой последовательности действие ConvertWorkspaceItem
        . Если вы используете TFS 2013 или более позднюю версию, добавьте в начало новой последовательности действие GetLocalPath. Для ConvertWorkspaceItem задайте следующие значения свойств: Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Для действия GetLocalPath задайте свойству IncomingPath значение  'PublishScriptLocation', а свойству Result - значение  'PublishScriptFilePath'. В этом
        действие преобразует путь сценария публикации из расположений сервера TFS
        (если применимо) в стандартный путь локального диска.

    5.  Если вы используете TFS 2012 или более раннюю версию, добавьте в конец новой последовательности еще одно действие ConvertWorkspaceItem
        . Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Если вы используете TFS 2013 или более позднюю версию, добавьте еще одно действие GetLocalPath. IncomingPath='SubscriptionDataFileLocation' и Result='SubscriptionDataFilePath.'

    6.  Добавьте действие InvokeProcess в конце новой последовательности.
        Это действие вызывает PowerShell.exe с аргументами, переданными
        переданными определением сборки.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Выполнение сценария публикации

        3.  FileName = "PowerShell" (включая кавычки)

        4.  OutputEncoding=
            System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  В текстовом поле раздела **Обработать стандартный вывод**
        для InvokeProcess задайте значение  'data'. Эта
        переменная предназначена для хранения данных стандартного вывода.

    8.  Добавьте действие WriteBuildMessage прямо под разделом **Обработка стандартного вывода**
        . Задайте значение Importance =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        и Message='data'. Это обеспечит запись стандартного
        вывода сценария в вывод сборки.

    9.  В поле раздела **Обработка вывода ошибки**
        для InvokeProcess задайте значение  'data'. Эта
        переменная предназначена для хранения данных стандартной ошибки.

    10. Добавьте действие WriteBuildError прямо под разделом **Обработка вывода ошибки**
        . Установите Message='data'. Это обеспечивает запись стандартных
        запись стандартных ошибок сценария в выводе данных об ошибках сборки.

	11. Исправьте все ошибки, отмеченные синими восклицательными знаками. Наведите указатель на
		восклицательный знак, чтобы получить совет об ошибке. Сохраните рабочий процесс, чтобы 
		очистить ошибки.

    Конечный результат действий рабочего процесса публикации будет выглядеть
    в конструкторе следующим образом:

    ![][5]

    Конечный результат действий рабочего процесса публикации будет выглядеть
    в XAML следующим образом:

		<If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
	        <If.Then>
	          <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
	            <Sequence.Variables>
	              <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
	              <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
	            </Sequence.Variables>
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
	            <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
	              <mtbwa:InvokeProcess.ErrorDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.ErrorDataReceived>
	              <mtbwa:InvokeProcess.OutputDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.OutputDataReceived>
	            </mtbwa:InvokeProcess>
	          </Sequence>
	        </If.Then>
	      </If>
	    </Sequence>


7.  Сохраните рабочий процесс шаблона процесса сборки и зарегистрируйте этот файл.

8.  Измените определение сборки (закройте его, если оно уже открыто) и нажмите
	кнопку **Создать**, если новый шаблон еще не отображается в списке шаблонов процесса.

9.  Задайте для значения свойств параметров в разделе "Разное" следующим образом:

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'   
        *This value is derived from:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'   
        *This value is derived from: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'   
        *Use the appropriate cloud service name here*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'   
        *Use the appropriate storage account name here*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. Сохраните изменения в определении сборки.

11. Поставьте в очередь сборку для выполнения пакета сборки и публикации. Если для
    триггера задана непрерывная интеграция, это поведение будет
    происходить при каждом возврате.

### <a name="script"> </a>Шаблон сценария PublishCloudService.ps1

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
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

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

## Дальнейшие действия

Чтобы включить удаленную отладку при использовании непрерывной доставки, см. [эти указания](http://go.microsoft.com/fwlink/p/?LinkID=402354). 

  [Непрерывная доставка в Azure с использованием Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Шаг 1. Настройка сервера сборки]: #step1
  [Шаг 2. Создание пакета с помощью команд MSBuild]: #step2
  [Шаг 3. Создание пакета с помощью TFS Team Build (необязательно)]: #step3
  [Шаг 4. Публикация пакета с помощью сценария PowerShell]: #step4
  [Шаг 5. публикация пакета с помощью TFS Team Build (дополнительно)]: #step5
  [службе сборок Team Foundation]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [.NET Framework 4.5.2]: http://go.microsoft.com/fwlink/?LinkId=521668
  [Инструменты создания Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Библиотеки Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Инструменты Azure для Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Справочник по командной строке MSBuild]: http://msdn.microsoft.com/library/ms164311(v=VS.90).aspx
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Общие сведения о системе сборки Team Foundation]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Развертывание и настройка сервера сборки]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [Командлеты Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [PUBLISHSETTINGS-файл]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [в конце этой статьи]: #script
  
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png

<!--HONumber=45--> 
