<properties 
   pageTitle="Подключение физических и виртуальных машин для управления с помощью Azure Automation DSC | Microsoft Azure" 
   description="Настройка машин для управления с помощью Azure Automation DSC" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="01/11/2016"
   ms.author="coreyp"/>

# Подключение компьютеров для управления с помощью Azure Automation DSC

## В чем преимущества управления компьютерами с помощью службы Azure Automation DSC?

Настройка требуемого состояния (DSC) с помощью службы автоматизации Azure так же проста, как и [настройка требуемого состояния с использованием PowerShell](https://technet.microsoft.com/library/dn249912.aspx). Это мощная служба управления конфигурациями для узлов DSC (физических и виртуальных машин), которую можно использовать в любом облачном или локальном центре обработки данных. Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию. Слой управления Azure Automation DSC используется для настройки требуемого состояния таким же образом, как слой управления службы автоматизации Azure используется в сценариях PowerShell. Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC. Дополнительные сведения о преимуществах использования DSC службы автоматизации Azure см. в разделе [Обзор DSC службы автоматизации Azure](automation-dsc-overview/).

Службу Azure Automation DSC можно использовать для управления разными компьютерами. Их типы перечислены ниже.

*    Виртуальные машины Azure (классические).
*    Виртуальные машины Azure
*    Физические или виртуальные машины под управлением Windows, расположенные локально или в облачной службе, отличной от Azure.
*    Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Кроме того, если вы не готовы управлять конфигурацией компьютера из облака, платформа Azure Automation DSC может также использоваться как конечная точка только для отчетности. Это позволяет задать (отправить) требуемую конфигурацию через DSC локально и просмотреть подробные сведения отчетов о соответствии узла требуемому состоянию в службе автоматизации Azure.

В следующих разделах описываются способы подключения каждого типа компьютеров к службе Azure Automation DSC.

## Виртуальные машины Azure (классические).

Служба Azure Automation DSC позволяет легко подключать виртуальные машины Azure (классические) для управления их настройками с помощью портала Azure или PowerShell. В процессе работы расширение DSC регистрирует виртуальную машину в службе Azure Automation DSC, исключая необходимость выполнения удаленного входа на виртуальную машину администратором. Так как с виртуальными машинами Azure расширение DSC работает асинхронно, можно воспользоваться алгоритмом отслеживания хода выполнения и устранения неполадок, который приведен ниже в разделе [**Устранение неполадок при подключении виртуальной машины Azure**](#troubleshooting-azure-virtual-machine-onboarding).


### Портал Azure

На [портале Azure](http://portal.azure.com/) щелкните **Обзор** -> **Виртуальные машины (классические)**. Выберите виртуальную машину Windows, которую необходимо подключить. В колонке панели мониторинга виртуальной машины последовательно выберите **Все параметры** -> **Расширения** -> **Добавить** -> **Служба автоматизации Azure DSC** -> **Создать**. Введите необходимые [значения локального диспетчера конфигураций DSC PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4), регистрационный ключ вашей учетной записи и URL-адрес регистрации. Также можно ввести конфигурацию узла, которая будет назначена виртуальной машине.


![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


Сведения о поиске URL-адреса регистрации и ключа учетной записи службы автоматизации для подключения компьютера см. ниже в разделе [**Безопасная регистрация**](#secure-registration).

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Add-AzureRmAccount
    
    # fill in correct values for your VM/Automation account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
     Properties = @{
        RegistrationKey = @{
          UserName = 'notused'
          Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = "ApplyAndMonitor"
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = "ContinueConfiguration"
        AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension `
     -VM $vm `
     -Publisher Microsoft.Powershell `
     -ExtensionName DSC `
     -Version 2.13 `
     -PublicConfiguration $PublicConfiguration `
     -PrivateConfiguration $PrivateConfiguration `
     -ForceUpdate

    $VM | Update-AzureVM

## Виртуальные машины Azure

Служба Azure Automation DSC позволяет легко подключать виртуальные машины Azure для управления конфигурацией с помощью портала Azure, шаблонов диспетчера ресурсов Azure или PowerShell. В процессе работы расширение DSC регистрирует виртуальную машину в службе Azure Automation DSC, исключая необходимость выполнения удаленного входа на виртуальную машину администратором. Так как с виртуальными машинами Azure расширение DSC работает асинхронно, можно воспользоваться алгоритмом отслеживания хода выполнения и устранения неполадок, который приведен ниже в разделе [**Устранение неполадок при подключении виртуальной машины Azure**](#troubleshooting-azure-virtual-machine-onboarding).


### Портал Azure

На [портале Azure](https://portal.azure.com/) перейдите к учетной записи службы автоматизации Azure, чтобы подключить виртуальные машины. На панели мониторинга учетной записи службы автоматизации щелкните **Узлы DSC** -> **Добавить виртуальную машину Azure**.

На странице **Выбор виртуальных машин для подключения** выберите одну или несколько виртуальных машин Azure для подключения.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


В разделе **Настройка регистрационных данных** введите необходимые вам [значения локального диспетчера конфигураций DSC PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4). Также можно ввести конфигурацию узла, которая будет назначена виртуальной машине.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Шаблоны диспетчера ресурсов Azure

Виртуальные машины Azure можно разворачивать и подключать к службе Azure Automation DSC с помощью шаблонов диспетчера ресурсов Azure. Пример шаблона, подключающего существующую виртуальную машину к службе автоматизации Azure DSC, см. в разделе [Настройка виртуальной машины с помощью расширения DSC и Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/). Расположение ключа и URL-адреса регистрации, которые шаблон принимает в качестве входных данных, см. ниже в разделе [**Безопасная регистрация**](#secure-registration).

### PowerShell

На портале Azure виртуальные машины можно подключать с помощью командлета [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) в PowerShell.

## Физические или виртуальные машины под управлением Windows, расположенные локально или в облачной службе, отличной от Azure.

Компьютеры под управлением Windows, расположенные локально или в облачных службах, отличных от Azure (например, в веб-службах Amazon), также можно подключить к службе Azure Automation DSC при наличии на них исходящего доступа к Интернету. Для этого требуется выполнить несколько простых шагов.

1. Убедитесь, что на компьютерах, которые будут подключены к службе автоматизации Azure DSC, установлена последняя версия [WMF 5](http://aka.ms/wmf5latest).
2. Создайте папку с необходимыми метаконфигурациями DSC, как указано ниже в разделе [**Создание метаконфигураций DSC**](#generating-dsc-metaconfigurations).
3. Удаленно примените метаконфигурации PowerShell DSC на компьютерах, которые нужно подключить. **Для выполнения этой команды на компьютере должна быть установлена последняя версия [WMF 5](http://aka.ms/wmf5latest)**.

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

4. Если метаконфигурации PowerShell DSC не удалось применить удаленно, скопируйте папку метаконфигураций (шаг 2) на каждый компьютер, который нужно подключить. Затем локально вызовите **Set-DscLocalConfigurationManager** на каждом компьютере, который нужно подключить.
5. С помощью портала Azure или командлетов убедитесь, что все компьютеры, которые нужно подключить, теперь отображаются как узлы DSC, зарегистрированные в вашей учетной записи службы автоматизации Azure.

## Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Компьютеры под управлением Linux, расположенные локально, в Azure или в облачной службе, отличной от Azure, также можно подключить к службе автоматизации Azure DSC при наличии на них исходящего доступа к Интернету. Для этого требуется выполнить несколько простых шагов.

1. Убедитесь, что на компьютерах, которые будут подключены к службе автоматизации Azure DSC, установлена последняя версия [агента DSC Linux](http://www.microsoft.com/download/details.aspx?id=49150).

2. Если [стандартные значения локального диспетчера конфигураций DSC PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) соответствуют требуемым, а подключаемые компьютеры должны извлекать данные из Azure Automation DSC **и** передавать их туда, сделайте следующее.

	*    На каждом компьютере под управлением Linux, который будет подключен к службе Azure Automation DSC, используйте файл Register.py для подключения с помощью значений по умолчанию локального диспетчера конфигураций DSC PowerShell:

		`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

	*    Расположение ключа и URL-адреса регистрации для учетной записи службы автоматизации см. ниже в разделе [**Безопасная регистрация**](#secure-registration).

	Если стандартные значения локального диспетчера конфигураций DSC PowerShell **не** **соответствуют** требуемым или подключаемые компьютеры должны подчиняться службе Azure Automation DSC, но не извлекать из нее параметры конфигурации или модули PowerShell, выполните шаги 3–6. В противном случае сразу перейдите к шагу 6.

3.	Создайте папку с необходимыми метаконфигурациями DSC, как указано ниже в разделе [**Создание метаконфигураций DSC**](#generating-dsc-metaconfigurations).
4.  Удаленно примените метаконфигурации PowerShell DSC на компьютерах, которые нужно подключить:
    	
    	$SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
        $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs
	
Для выполнения этой команды на компьютере должна быть установлена последняя версия [WMF 5](http://aka.ms/wmf5latest).

5.  Если не удалось применить метаконфигурации PowerShell DSC удаленно, скопируйте соответствующую метаконфигурацию из папки (шаг 5) на каждый компьютер под управлением Linux, который нужно подключить. Затем вызовите `SetDscLocalConfigurationManager.py` локально на каждом компьютере под управлением Linux, который нужно подключить к службе автоматизации Azure DSC:

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

6.  С помощью портала Azure или командлетов убедитесь, что все компьютеры, которые нужно подключить, теперь отображаются как узлы DSC, зарегистрированные в вашей учетной записи службы автоматизации Azure.

##Создание метаконфигураций DSC
Для универсального внедрения любого компьютера в службу автоматизации Azure DSC можно создать метаконфигурацию DSC, при использовании которой агент DSC на соответствующем компьютере будет настроен на извлечение данных из службы автоматизации Azure и (или) перечу в эту службу отчетов. Метаконфигурации DSC для службы автоматизации Azure DSC можно создавать, используя либо конфигурацию PowerShell DSC, либо командлеты PowerShell в службе автоматизации Azure.

**Примечание**. Метаконфигурации DSC содержат секретные данные, необходимые при подключении компьютера к учетной записи службы автоматизации для управления. Обеспечьте должную защиту создаваемых метаконфигураций или удаляйте их сразу после использования.

###Использование конфигурации DSC
1.	Запустите на компьютере, входящем в локальную среду, консоль PowerShell ISE от имени администратора. На этом компьютере должна быть установлена последняя версия [WMF 5](http://aka.ms/wmf5latest).

2.	Локально выполните следующий скрипт. Этот сценарий содержит конфигурацию PowerShell DSC для создания метаконфигураций и команду, запускающую этот процесс.
    
        # The DSC configuration that will generate metaconfigurations
        [DscLocalConfigurationManager()]
        Configuration DscMetaConfigs 
        { 
            param 
            ( 
                [Parameter(Mandatory=$True)] 
                [String]$RegistrationUrl,
         
                [Parameter(Mandatory=$True)] 
                [String]$RegistrationKey,

                [Parameter(Mandatory=$True)] 
                [String[]]$ComputerName,

                [Int]$RefreshFrequencyMins = 30, 
            
                [Int]$ConfigurationModeFrequencyMins = 15, 
            
                [String]$ConfigurationMode = "ApplyAndMonitor", 
            
                [String]$NodeConfigurationName,

                [Boolean]$RebootNodeIfNeeded= $False,

                [String]$ActionAfterReboot = "ContinueConfiguration",

                [Boolean]$AllowModuleOverwrite = $False,

                [Boolean]$ReportOnly
            )

    
            if(!$NodeConfigurationName -or $NodeConfigurationName -eq "") 
            { 
                $ConfigurationNames = $null 
            } 
            else 
            { 
                $ConfigurationNames = @($NodeConfigurationName) 
            }

            if($ReportOnly)
            {
               $RefreshMode = "PUSH"
            }
            else
            {
               $RefreshMode = "PULL"
            }

            Node $ComputerName
            {

                Settings 
                { 
                    RefreshFrequencyMins = $RefreshFrequencyMins 
                    RefreshMode = $RefreshMode 
                    ConfigurationMode = $ConfigurationMode 
                    AllowModuleOverwrite  = $AllowModuleOverwrite 
                    RebootNodeIfNeeded = $RebootNodeIfNeeded 
                    ActionAfterReboot = $ActionAfterReboot 
                    ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins 
                }

                if(!$ReportOnly)
                {
                   ConfigurationRepositoryWeb AzureAutomationDSC 
                    { 
                        ServerUrl = $RegistrationUrl 
                        RegistrationKey = $RegistrationKey 
                        ConfigurationNames = $ConfigurationNames 
                    }

                    ResourceRepositoryWeb AzureAutomationDSC 
                    { 
                       ServerUrl = $RegistrationUrl 
                       RegistrationKey = $RegistrationKey 
                    }
                }

                ReportServerWeb AzureAutomationDSC 
                { 
                    ServerUrl = $RegistrationUrl 
                    RegistrationKey = $RegistrationKey 
                }
            } 
        }
        
        # Create the metaconfigurations
        # TODO: edit the below as needed for your use case
        $Params = @{
             RegistrationUrl = '<fill me in>';
             RegistrationKey = '<fill me in>';
             ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
             NodeConfigurationName = 'SimpleConfig.webserver';
             RefreshFrequencyMins = 30;
             ConfigurationModeFrequencyMins = 15;
             RebootNodeIfNeeded = $False;
             AllowModuleOverwrite = $False;
             ConfigurationMode = 'ApplyAndMonitor';
             ActionAfterReboot = 'ContinueConfiguration';
             ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
        }
        
        # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
        # For more info about splatting, run: Get-Help -Name about_Splatting
        DscMetaConfigs @Params

3.	Введите регистрационный ключ и URL-адрес для учетной записи автоматизации, а также имена виртуальных машин, которые необходимо внедрить. Все остальные параметры являются необязательными. Расположение ключа и URL-адреса регистрации для учетной записи службы автоматизации см. ниже в разделе [**Безопасная регистрация**](#secure-registration).

4.	Если вы хотите, чтобы компьютеры передавали сведения о состоянии DSC платформе Azure Automation DSC, не извлекая конфигурацию или модули PowerShell, установите для параметра **ReportOnly** значение Тrue.

5.	Выполните скрипт. В рабочем каталоге появится папка **DscMetaConfigs**, содержащая метаконфигурации PowerShell DSC для подключаемых компьютеров.

###Использование командлетов службы автоматизации Azure
Если значения по умолчанию локального диспетчера конфигураций DSC PowerShell соответствуют требуемым и вы хотите внедрить компьютеры таким образом, чтобы позволить им извлекать данные из службы автоматизации Azure DSC и передавать в эту службу отчеты, легко создать необходимые конфигурации DSC позволят командлеты службы автоматизации Azure:

1.	Запустите на компьютере, входящем в локальную среду, консоль PowerShell или PowerShell ISE от имени администратора.

2.	Подключитесь к диспетчеру ресурсов Azure с помощью командлета **Add-AzureRmAccount**.

3.	Из учетной записи службы автоматизации, к которой будут подключены узлы, загрузите метаконфигурации DSC PowerShell для подключаемых компьютеров:

        # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
        $Params = @{
            ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
            AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
            ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
            OutputFolder = "$env:UserProfile\Desktop";
        }
        
        # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
        # For more info about splatting, run: Get-Help -Name about_Splatting
        Get-AzureRmAutomationDscOnboardingMetaconfig @Params

Будет создана папка ***DscMetaConfigs***, содержащая метаконфигурации PowerShell DSC для подключаемых компьютеров.

##Безопасная регистрация

Компьютеры можно безопасно подключать к учетной записи службы автоматизации Azure с помощью протокола регистрации DSC WMF 5. Этот протокол позволяет узлу DSC проходить проверку подлинности на сервере отчетов или опрашивающем сервере PowerShell DSC V2 (включая Azure Automation DSC). Узел регистрируется на сервере с использованием **URL-адреса регистрации**, проходя проверку подлинности с помощью **Регистрационного ключа**. Во время регистрации узел DSC и сервер отчетов или опрашивающий сервер DSC создают для этого узла уникальный сертификат, который будет использоваться для проверки подлинности после регистрации на сервере. Этот процесс исключает возможность подмены подключенных узлов друг другом, например, если один из узлов взломан и является вредоносным. После регистрации регистрационный ключ повторно не используется для проверки подлинности и удаляется из узла.

Данные, необходимые для протокола регистрации DSC, можно найти в колонке **Управление ключами** на портале предварительной версии Azure. Откройте эту колонку, щелкнув значок ключа на панели **Основные компоненты** в учетной записи службы автоматизации.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    «Регистрационный URL-адрес» — это поле «URL-адрес» в колонке «Управление ключами».
*    Регистрационный ключ — это поле «Первичный ключ доступа» или «Вторичный ключ доступа» в колонке «Управление ключами». Можно использовать любой из этих ключей.

Для повышения безопасности первичный и вторичный ключи доступа учетной записи службы автоматизации можно создавать повторно в любое время (в колонке **Управление ключами**). Это исключает возможность регистрации узла с помощью ранее использованных ключей.

##Устранение неполадок при подключении виртуальной машины Azure

Служба Azure Automation DSC позволяет легко подключать виртуальные машины Microsoft Azure для управления конфигурациями. Расширение DSC для виртуальных машин Azure используется для регистрации виртуальных машин в службе Azure Automation DSC. Так как с виртуальными машинами Azure расширение DSC работает асинхронно, важно отслеживать ход выполнения и устранять неполадки.

>[AZURE.NOTE] Независимо от способа подключения виртуальной машины Microsoft Azure к службе Azure Automation DSC с расширением DSC для виртуальных машин Azure, узел будет отображаться как зарегистрированный в службе автоматизации Azure приблизительно через час. Это связано с тем, что расширение DSC для виртуальных машин Azure устанавливает на виртуальную машину платформу Windows Management Framework 5.0, которая требуется для размещения этой виртуальной машины в DSC службы автоматизации Azure.

Чтобы устранить неполадки или просмотреть состояние расширения DSC для виртуальных машин Azure, на портале Azure перейдите к подключаемой виртуальной машине и щелкните **Все параметры** -> **Расширения** -> **DSC**. Для получения дополнительных сведений щелкните **Просмотреть подробные сведения о состоянии**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## Истечение срока действия сертификата и повторная регистрация

Возможно, после регистрации компьютера в качестве узла DSC на платформе Azure Automation DSC этот узел нужно будет зарегистрировать повторно. Такая необходимость может возникнуть по ряду причин.

* После регистрации каждый узел автоматически согласовывает уникальный сертификат для проверки подлинности, срок действия которого составляет один год. В настоящее время протокол регистрации PowerShell DSC не обновляет сертификаты автоматически, когда приближается дата окончания срока их действия. Поэтому в конце года узлы нужно регистрировать повторно. Перед повторной регистрацией убедитесь, что на каждом узле работает Windows Management Framework 5.0 RTM. Если истек срок действия сертификата проверки подлинности узла и узел не зарегистрирован, то он не сможет взаимодействовать со службой автоматизации Azure и получит пометку "Не отвечает". Повторная регистрация, выполненная не позднее чем через 90 дней с момента истечения срока действия сертификата или в любой момент после истечения срока действия сертификата, приведет к созданию и использованию нового сертификата.

* Чтобы изменить какие-либо [значения локального диспетчера конфигураций PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4), заданные при первоначальной регистрации узла, например ConfigurationMode. Сейчас эти значения агента DSC можно изменить только в ходе повторной регистрации. Единственным исключением является конфигурация узла, назначенная узлу, — ее можно изменить на платформе Azure Automation DSC напрямую.

Повторная регистрация узла выполняется аналогично первоначальной, то есть с помощью любого из средств, описанных в этом документе. Перед повторной регистрацией отменять регистрацию узла на платформе Azure Automation DSC не нужно.


## Связанные статьи
* [Обзор Azure Automation DSC](automation-dsc-overview.md)
* [Командлеты Automation DSC Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Цены на Automation DSC Azure](https://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=AcomDC_0218_2016-->