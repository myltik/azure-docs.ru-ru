---
title: "Непрерывная отправка для облачных служб с помощью TFS в Azure | Документация Майкрософт"
description: "Узнайте, как настраивать непрерывную отправку для облачных приложений Azure. Примеры программного кода для инструкций командной строки MSBuild и сценариев PowerShell."
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 0979722b9ec715e91825c7aba74657451df6e83f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Непрерывная доставка для облачных служб в Azure
Описанный в этой статье процесс показывает, как настроить непрерывную доставку для облачных приложений Azure. Этот процесс позволяет автоматически создавать пакеты и развертывать их в Azure после каждого возврата кода. Процесс сборки пакета, представленный в этой статье, эквивалентен команде **Package** в Visual Studio. Этапы публикации эквивалентны команде **Publish** в Visual Studio.
В статье описаны методы, которые можно использовать для создания сервера сборки с помощью инструкций командной строки MSBuild и сценариев Windows PowerShell, а также показано, как дополнительно настроить Visual Studio Team Foundation Server — определения Team Build для использования команд MSBuild и сценариев PowerShell. Процесс можно настраивать для среды сборки и целевых сред Azure.

Для упрощения задачи можно также использовать службу Visual Studio Team Services — версию TFS, размещенную в Azure. 

Прежде чем начать, следует опубликовать приложение из Visual Studio.
Это обеспечит доступность и инициализацию всех ресурсов при попытке автоматизировать процесс публикации.

## <a name="1-configure-the-build-server"></a>1. Настройка сервера сборки
Прежде чем создавать пакет Azure с помощью MSBuild, следует установить на сервере сборки необходимое программное обеспечение и инструменты.

Visual Studio необязательно должна быть установлена на сервере сборки. Если вы хотите использовать для управления сервером сборки службу сборок Team Foundation, следуйте указаниям в документации по [службе сборок Team Foundation][Team Foundation Build Service].

1. На сервере сборки установите платформу [.NET Framework 4.5.2][.NET Framework 4.5.2], которая включает в себя MSBuild.
2. Установите последнюю версию [инструментов разработки Azure Authoring Tools для .NET](https://azure.microsoft.com/develop/net/).
3. Установите [библиотеки Azure для .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Скопируйте файл Microsoft.WebApplication.targets из папки с программой Visual Studio на сервер сборки.

   На компьютере с установленной средой Visual Studio файл располагается в каталоге C:\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Нужно скопировать его в тот же каталог на сервере сборки.
5. Установите [инструменты Azure для Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2. Создание пакета с помощью команд MSBuild
В этом разделе описывается создание команды MSBuild, которая выполняет сборку пакета Azure. Выполните этот шаг на сервере сборки, чтобы убедиться, что все правильно настроено и команда MSBuild делает то, что нужно. Эту командную строку можно добавить в существующие сценарии сборки на сервере сборки или использовать командную строку в определении сборки TFS, как описано в следующем разделе. Дополнительные сведения о параметрах командной строки и MSBuild см. в [справочнике по командной строке MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Если среда Visual Studio установлена на сервере сборки, то в Windows найдите и щелкните **Командная строка Visual Studio** в папке **Visual Studio Tools**.

   Если экземпляр Visual Studio не установлен на сервере сборки, откройте командную строку и убедитесь, что файл MSBuild.exe доступен в пути. MSBuild устанавливается вместе с .NET Framework по пути %WINDIR%\\Microsoft.NET\\Framework\\*версия*. Например, чтобы добавить файл MSBuild.exe в переменную среды PATH при наличии платформы .NET Framework 4, введите следующую команду в командной строке:

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. В командной строке перейдите в папку, содержащую файл проекта Azure, который требуется создать.
3. Запустите MSBuild с параметром /target:Publish, как показано в следующем примере.

       MSBuild /target:Publish

   Этот параметр можно сократить до /t:Publish. Параметр /t:Publish в MSBuild не следует путать с командами Publish, доступными в Visual Studio при наличии установленного пакета SDK для Azure. Параметр /t:Publish собирает только пакеты Azure. Он не осуществляет развертывание пакетов, как команды Publish в Visual Studio.

   Дополнительно можно указать имя проекта в качестве параметра MSBuild. Если не указано, используется текущий каталог. Дополнительные сведения о параметрах командной строки MSBuild см. в [справочнике по командной строке MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Найдите данные вывода. По умолчанию команда создает каталог относительно корневой папки проекта, например *ProjectDir*\\bin\\*Configuration*\\app.publish\\. При сборке проекта Azure создается два файла — собственно файл пакета и сопутствующий файл конфигурации:

   * Project.cspkg
   * ServiceConfiguration.*TargetProfile*.cscfg

   По умолчанию каждый проект Azure включает в себя один файл конфигурации службы (с расширением .cscfg) для локальных сборок (отладка) и еще один файл для облачных сборок (промежуточная или рабочая среда), но вы можете добавлять или удалять файлы конфигурации службы при необходимости. При сборке пакета в Visual Studio вам будет предложено выбрать файл конфигурации службы для включения в пакет.
5. Укажите файл конфигурации службы. При сборке пакета с использованием MSBuild по умолчанию включается файл конфигурации локальной службы. Чтобы включить другой файл конфигурации службы, задайте свойство TargetProfile команды MSBuild, как показано в следующем примере.

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Укажите расположение для данных вывода. Укажите путь с помощью параметра /p:PublishDir=*каталог*\\, включая конечный символ обратной косой черты в качестве разделителя, как показано в следующем примере.

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   После создания и тестирования соответствующей командной строки MSBuild для сборки проектов и объединения их в пакет Azure можно добавить эту команду в сценарии сборки. Если сервер сборки использует пользовательские сценарии, этот процесс будет зависеть от особенностей пользовательского процесса сборки. Если в качестве среды сборки используется TFS, вы можете следовать указаниям на следующем шаге, чтобы добавить пакет сборки Azure в процессе сборки.

## <a name="3-build-a-package-using-tfs-team-build"></a>3. Создание пакета с помощью TFS Team Build
Если Team Foundation Server (TFS) настроен как контроллер сборки, а сервер сборки настроен как компьютер сборки TFS, вы можете дополнительно настроить автоматизированную сборку для пакета Azure. Сведения о том, как настроить и использовать сервер Team Foundation в качестве системы сборки, см. в разделе [Расширение системы сборки][Scale out your build system]. В частности, в следующей процедуре подразумевается, что вы настроили сервер сборки, как описано в разделе [Развертывание и настройка сервера сборки][Deploy and configure a build server], создали командный проект и создали проект облачной службы в командном проекте.

Чтобы настроить TFS для выполнения сборки пакетов Azure, выполните следующее:

1. В Visual Studio на компьютере разработчика в меню "Вид" выберите **Team Explorer** или нажмите клавиши CTRL+\\, CTRL+M. В окне Team Explorer разверните узел **Сборки** или выберите **соответствующую** страницу, а затем — пункт **Создать определение сборки**.

   ![Параметр "Создать определение сборки"][0]
2. Выберите вкладку **Триггер** и укажите нужные условия для сборки пакета. Например, укажите **Непрерывная интеграция** для сборки пакета при каждом возврате из системы управления версиями.
3. Перейдите на вкладку **Параметры исходного кода** и убедитесь, что папка вашего проекта числится в столбце **Папка системы управления версиями**, а значение состояния — **Активно**.
4. Перейдите на вкладку **Параметры сборки по умолчанию** и в разделе «Контроллер сборки» проверьте имя сервера сборки.  Также выберите параметр **Копировать выходные данные в следующую папку сброса** и укажите нужное расположение.
5. Выберите вкладку **Процесс** . На вкладке "Процесс" выберите шаблон по умолчанию, в разделе **Сборка** выберите проект, если он еще не выбран, и разверните раздел **Дополнительно** в разделе сетки **Сборка**.
6. Выберите **Аргументы MSBuild**и задайте соответствующие командной строки MSBuild, как описано в шаге 2 выше. Например, введите **/t:Publish /p:PublishDir=\\\\myserver\\drops\\**, чтобы собрать пакет и скопировать файлы пакета в расположение \\\\myserver\\drops\\.

   ![Аргументы MSBuild][2]

   > [!NOTE]
   > Копирование файлов в общую папку упрощает развертывание пакетов с компьютера разработчика вручную.
7. Протестируйте выполнение шага сборки, проверяя изменения проекта на входе, или поставьте новую сборку в очередь. Чтобы поставить в очередь новую сборку, в Team Explorer щелкните правой кнопкой мыши пункт **Все определения сборки**, а затем выберите **Поставить новую сборку в очередь**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4. Публикация пакета с помощью сценария PowerShell
В этом разделе описывается, как создать сценарий Windows PowerShell, который будет публиковать выходные данные пакета облачного приложения в Azure, используя дополнительные параметры. Этот сценарий может быть вызван после шага сборки при автоматизации пользовательской сборки. Также он может быть вызван из действий рабочего процесса шаблона процесса в Visual Studio TFS Team Build.

1. Установите [командлеты Azure PowerShell][Azure PowerShell cmdlets] (версии 0.6.1 или более поздней).
   Во время установки командлета выберите установку в качестве оснастки. Обратите внимание, что эта официально поддерживаемая версия заменяет старую версию CodePlex, несмотря на то что предыдущие версии были пронумерованы 2.x.x.
2. Запустите Azure PowerShell из меню «Пуск» или с начальной страницы. При запуске таким способом загружаются командлеты Azure PowerShell.
3. В командной строке PowerShell убедитесь, что командлеты PowerShell загружены. Для этого введите часть команды `Get-Azure` и нажмите клавишу TAB для выполнения оператора.

   Если несколько раз нажать клавишу TAB, вы увидите различные команды Azure PowerShell.
4. Проверьте возможность подключения к подписке Azure, выполнив импорт информации о подписке из PUBLISHSETTINGS-файла.

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Введите команду:

   `Get-AzureSubscription`

   Отобразится информация о подписке. Убедитесь, что все работает правильно.
5. Сохраните шаблон скрипта, приведенный в конце этой статьи, в папке скриптов с именем C:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.
6. Просмотрите раздел параметров скрипта. Добавьте или измените любые значения по умолчанию. Эти значения можно всегда переопределить с помощью передачи явных параметров.
7. Убедитесь, что созданы допустимая облачная служба и учетная запись хранения в подписке, которая может быть целевой для сценария публикации. Учетная запись хранения (хранилище больших двоичных объектов) будет использоваться для передачи и временного хранения файла конфигурации и пакета развертывания во время создания развертывания.

   * Чтобы создать облачную службу, можно вызвать этот сценарий или воспользоваться [порталом Azure](https://portal.azure.com). Имя облачной службы будет использоваться в качестве префикса в полном имени домена, поэтому оно должно быть уникальным.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Чтобы создать учетную запись хранения, можно вызвать этот сценарий или воспользоваться [порталом Azure](https://portal.azure.com). Имя учетной записи хранения будет использоваться в качестве префикса в полном имени домена, поэтому должно быть уникальным. Попробуйте использовать то же имя, что и в облачной службе.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. Вызовите сценарий непосредственно из Azure PowerShell или подключите этот сценарий к автоматизации сборки узла, выполняемой после сборки пакета.

   > [!IMPORTANT]
   > Сценарий всегда будет удалять или заменять существующие развертывания по умолчанию, если они обнаружены. Это необходимо для включения непрерывной доставки из автоматизации, где невозможно вмешательство пользователя.
   >
   >

   **Пример сценария 1.** Непрерывное развертывание в промежуточной среде службы

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Это обычно сопровождается тестовым запуском проверки и замены виртуального IP-адреса. Заменить виртуальный IP-адрес можно на [портале Azure](https://portal.azure.com) или с помощью командлета Move-Deployment.

   **Пример сценария 2.** Непрерывное развертывание в рабочей среде выделенной тестовой службы

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Удаленный рабочий стол**

   При включении удаленного рабочего стола в проекте Azure потребуется выполнить одноразовые дополнительные шаги, чтобы убедиться, что все облачные службы, являющиеся целью этого сценария, имеют правильный сертификат облачной службы.

   Найдите значения отпечатка сертификата, который ожидают роли. Значения отпечатков отображаются в разделе Certificates файла конфигурации облака (т.е. ServiceConfiguration.Cloud.cscfg). Кроме того, он отображается в диалоговом окне «Конфигурация удаленных рабочих столов» в Visual Studio при отображении параметров и просмотре выбранного сертификата.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   Передайте сертификаты удаленного рабочего стола, как на этапе одноразовой настройки, с помощью следующего сценария командлета:

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   Например:

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   Кроме того, вы можете экспортировать PFX-файл сертификата с закрытым ключом и отправлять сертификаты в каждую целевую облачную службу с помощью [портала Azure](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Обновление развертывания по сравнению с удалением развертывания и \>созданием развертывания**

   По умолчанию сценарий будет выполнять развертывание обновления ($enableDeploymentUpgrade = 1) при передаче параметра или значения «1» явным образом. Для одного экземпляра это имеет преимущество, поскольку тратится меньше времени, чем на полное развертывание. Для экземпляров, которым требуется высокий уровень доступности, преимущество состоит в сохранении работы некоторых экземпляров в то время, как другие обновляются (проходя домен обновления), а также не удаляются ваши виртуальные IP-адреса.

   Развертывание обновления может быть отключено в сценарии ($enableDeploymentUpgrade = 0) или при передаче параметра *-enableDeploymentUpgrade 0* , который изменяет поведение сценария, сначала удаляя все существующие развертывания, а затем создавая новое развертывание.

   > [!IMPORTANT]
   > Сценарий всегда будет удалять или заменять существующие развертывания по умолчанию, если они обнаружены. Это необходимо для включения непрерывной доставки из автоматизации, где невозможно вмешательство пользователя или оператора.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5. Публикация пакета с помощью TFS Team Build
На этом необязательном шаге будет выполнено подключение TFS Team Build к сценарию, созданному на шаге 4, который обрабатывает публикацию сборки пакета в Azure. Это влечет за собой изменение шаблона процесса, использованного определением сборки таким образом, чтобы в конце процесса выполнялось действие Publish. Действие Publish выполнит команду PowerShell, передавая параметры из сборки. Выходные данные целевых объектов MSBuild и сценария публикации будут направлены в стандартный вывод сборки.

1. Редактирование определение сборки, ответственного за непрерывное развертывание.
2. Выберите вкладку **Процесс** .
3. Выполните [эти инструкции](http://msdn.microsoft.com/library/dd647551.aspx) , чтобы добавить проект действия для шаблона процесса сборки, загрузить шаблон по умолчанию, добавить его в проект и применить. Присвойте шаблону процесса сборки новое имя, например AzureBuildProcessTemplate.
4. Вернитесь на вкладку **Процесс** и выберите пункт **Показать сведения**, чтобы просмотреть список доступных шаблонов процесса сборки. Нажмите кнопку **Создать...** и перейдите к проекту, который был только что добавлен и зарегистрирован. Найдите только что созданный шаблон и нажмите кнопку **ОК**.
5. Откройте выбранный шаблон процесса для редактирования. Его можно открыть непосредственно в конструкторе рабочих процессов или с помощью редактора XML для работы с XAML.
6. Добавьте перечисленные ниже новые аргументы в виде отдельных элементов строк на вкладке аргументов конструктора рабочих процессов. Все аргументы должны иметь параметры direction=In и type=String. Это будет использоваться для параметров из определения сборки в рабочем процессе, который затем используется для вызова сценария публикации.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Список аргументов][3]

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
7. Добавьте новую последовательность в конце агента выполнения.

   1. Начните с добавления действия инструкции If для проверки допустимого файла сценария. Задайте условие для этого значения.

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. Для варианта Then в инструкции If добавьте новое действие последовательности. Задайте в качестве отображаемого имени "Начало публикации"
   3. Оставив выбранной последовательность начала публикации, добавьте перечисленные ниже новые переменные в виде отдельных элементов строк на вкладке «Переменные» конструктора рабочих процессов. Все переменные должны иметь тип String и Scope=Start publish. Это будет использоваться для параметров из определения сборки в рабочем процессе, который затем используется для вызова сценария публикации.

      * SubscriptionDataFilePath типа String.
      * PublishScriptFilePath типа String.

        ![Новые переменные][4]
   4. Если вы используете TFS 2012 или более раннюю версию, добавьте в начало новой последовательности действие ConvertWorkspaceItem. Если вы используете TFS 2013 или более позднюю версию, добавьте в начало новой последовательности действие GetLocalPath. Установите свойства ля ConvertWorkspaceItem следующим образом: Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation', Result='PublishScriptFilePath', Workspace='Workspace'. Для действия GetLocalPath задайте свойству IncomingPath значение 'PublishScriptLocation', а свойству Result — значение 'PublishScriptFilePath'. Это действие преобразует путь сценария публикации от расположений сервера TFS (если применимо) в стандартный локальный дисковый путь.
   5. Если вы используете TFS 2012 или более раннюю версию, добавьте в конец новой последовательности еще одно действие ConvertWorkspaceItem. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Если вы используете TFS 2013 или более позднюю версию, добавьте еще одно действие GetLocalPath. IncomingPath='SubscriptionDataFileLocation' и Result='SubscriptionDataFilePath.'
   6. Добавьте действие InvokeProcess в конце новой последовательности.
      Это действие вызывает PowerShell.exe с аргументами, переданными определением сборки.

      + Arguments = String.Format(" -File ""{0}"" -serviceName {1}  -storageAccountName {2} -packageLocation ""{3}""  -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""  -selectedSubscription {6} -environment ""{7}""",  PublishScriptFilePath, ServiceName, StorageAccountName,  PackageLocation, CloudConfigLocation,  SubscriptionDataFilePath, SubscriptionName, Environment)
      + DisplayName = Выполнение сценария публикации
      + FileName = "PowerShell" (включая кавычки)
      + OutputEncoding=  System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. В текстовом поле раздела **Обработка стандартного вывода** InvokeProcess установите значение data. Эта переменная предназначена для хранения данных стандартного вывода.
   8. Добавьте действие WriteBuildMessage прямо под разделом **Обработка стандартного вывода** . Установите Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' и Message='data'. Это обеспечит запись стандартного вывода сценария в вывод сборки.
   9. В поле раздела **Обработка вывода ошибки** InvokeProcess установите значение data. Эта переменная предназначена для хранения данных стандартной ошибки.
   10. Добавьте действие WriteBuildError прямо под разделом **Обработка вывода ошибки** . Установите Message='data'. Это обеспечит запись стандартных ошибок сценария в выводе данных об ошибках сборки.
   11. Исправьте все ошибки, отмеченные синими восклицательными знаками. Наведите указатель мыши на восклицательный знак, чтобы получить подсказку по ошибке. Сохраните рабочий процесс, чтобы очистить ошибки.

   Конечный результат действия рабочего процесса публикации будет выглядеть в конструкторе следующим образом:

   ![Действия рабочего процесса][5]

   Конечный результат действия рабочего процесса публикации будет выглядеть в XAML следующим образом:

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
8. Сохраните рабочий процесс шаблона процесса сборки и зарегистрируйте этот файл.
9. Измените определение сборки (закройте его, если оно уже открыто) и нажмите кнопку **Создать** , если новый шаблон еще не отображается в списке шаблонов процесса.
10. Задайте для значения свойств параметров в разделе "Разное" следующим образом:

    1. CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Это значение является производным от: ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg' *Это значение является производным от: ($PublishDir)($ProjectName).cspkg*
    3. PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'
    4. ServiceName = "mycloudservicename" *Используйте здесь соответствующее имя облачной службы*
    5. Environment = 'Staging'
    6. StorageAccountName = 'mystorageaccountname' *Используйте здесь соответствующее имя учетной записи хранения*
    7. SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'
    8. SubscriptionName = 'default'

    ![Значения свойств параметров][6]
11. Сохраните изменения в определении сборки.
12. Поставьте в очередь сборку для выполнения пакета сборки и публикации. Если есть триггер для установки непрерывного развертывания, это поведение будет выполняться при каждом возврате.

### <a name="publishcloudserviceps1-script-template"></a>Шаблон сценария PublishCloudService.ps1
```
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
```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы включить удаленную отладку при использовании непрерывной поставки, см. статью [Включение удаленной отладки при использовании непрерывной поставки для публикации на Azure](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
