---
title: "Гибридные рабочие роли Runbook в службе автоматизации Azure | Документация Майкрософт"
description: "В этой статье содержатся сведения об установке и использовании гибридной рабочей роли Runbook, которая является компонентом службы автоматизации Azure, позволяющим запускать модули runbook на компьютерах в локальном центре обработки данных или поставщике облачных решений."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 783fb22b0154915f2e3d8574ab95538dbd646705
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---

# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook
Модули runbook в службе автоматизации Azure не могут получить доступ к ресурсам в других облаках или локальной среде, так как они выполняются в облаке Azure.  Гибридная рабочая роль Runbook службы автоматизации Azure позволяет выполнять модули runbook непосредственно на компьютере, размещающем роль, для работы с ресурсами в среде, что позволяет управлять этими локальными ресурсами. Для хранения модулей runbook и управления ими используется служба автоматизации Azure, затем они передаются на один или несколько целевых компьютеров.  

Эта функция проиллюстрирована на рисунке ниже.<br>  

![Обзор гибридного компонента Runbook Worker](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Технический обзор гибридной рабочей роли Runbook и рекомендации по развертыванию приведены в разделе [Общие сведения об архитектуре автоматизации](automation-offering-get-started.md#automation-architecture-overview).    

## <a name="hybrid-runbook-worker-groups"></a>Группы гибридных компонентов Runbook Worker
Каждый гибридный компонент Runbook Worker является членом группы гибридных компонентов Runbook Worker, которая указывается при установке агента.  Группа может включать одного агента, но в нее можно установить несколько агентов для обеспечения высокого уровня доступности.

Когда вы запускаете модуль runbook в гибридной рабочей роли Runbook, вы выбираете группу для его выполнения.  Члены группы самостоятельно определят, какая из служб рабочей роли будет обслуживать этот запрос.  Указание конкретного компонента Worker для выполнения конкретной задачи не предусмотрено.

## <a name="relationship-to-service-management-automation"></a>Связь с автоматизацией управления службами
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) позволяет запускать в локальном центре обработки данных любые модули Runbook, поддерживаемые службой автоматизации Azure. Компонент SMA обычно развертывается вместе с Windows Azure Pack, так как Windows Azure Pack содержит графический интерфейс для управления SMA. В отличие от службы автоматизации Azure, компоненту SMA требуется локальная установка, которая включает в себя веб-серверы для размещения API, базу данных, содержащую модули Runbook и конфигурацию SMA, и рабочие роли runbook для выполнения заданий Runbook. Служба автоматизации Azure предоставляет эти службы в облаке, для чего необходимо поддерживать гибридные компоненты Runbook Worker в локальной среде.

Если вы являетесь пользователем SMA, то вы можете переместить модули runbook в службу автоматизации Azure, чтобы затем использовать их в гибридной рабочей роли Runbook без изменений, если, конечно, они используют собственную аутентификацию для доступа к ресурсам, как это описано в разделе [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).  Модули Runbook в SMA выполняются в контексте учетной записи службы на сервере компонентов Worker, который может обеспечивать проверку подлинности для модулей Runbook.

Вы можете воспользоваться следующими критериями, чтобы определить, что больше подходит под ваши требования: служба автоматизации Azure с гибридным компонентом Runbook Worker или автоматизация управления службами (SMA).

* Если требуется графический интерфейс управления SMA, то нужно локально установить его базовые компоненты, связанные с Windows Azure Pack. По сравнению со службой автоматизации Azure, для которой необходим только агент, установленный в локальных рабочих ролях Runbook, требуется больше локальных ресурсов и более высокие затраты на обслуживание. Агенты управляются при помощи Operations Management Suite, что позволяет дополнительно снизить затраты на обслуживание.
* Служба автоматизации Azure сохраняет свои модули Runbook в облаке и предоставляет их локальным гибридным рабочим ролям Runbook. Если ваша политика безопасности не допускает развертывания такой модели, тогда необходимо использовать SMA.
* Компонент SMA входит в состав System Center. Следовательно, для его использования требуется лицензия System Center 2012 R2. Служба автоматизации Azure основана на модели многоуровневой подписки.
* Служба автоматизации Azure обладает дополнительными функциями, такими как создание графических модулей Runbook, которые недоступны в SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Установка гибридной рабочей роли Runbook Windows 

Ниже описаны два метода установки и настройки гибридной рабочей роли Runbook Windows.  Мы рекомендуем использовать модуль runbook службы автоматизации для полностью автоматической настройки компьютера Windows.  Второй метод заключается в пошаговом выполнении инструкций по ручной установке и настройке роли.  

> [!NOTE]
> Чтобы управлять конфигурацией серверов, поддерживающих гибридную рабочую роль Runbook с настройкой требуемого состояния (DSC), добавьте такие серверы в качестве узлов DSC.  Дополнительные сведения о развертывании этих узлов для управления с помощью DSC см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).           
><br>
>Если вы включите [решение по управлению обновлениями](../operations-management-suite/oms-solution-update-management.md), любой подключенный к рабочей области OMS компьютер Windows будет автоматически настроен в качестве гибридной рабочей роли Runbook для поддержки модулей runbook, которые входят в это решение.  Но он не регистрируется в группах гибридных рабочих ролей, которые уже определены в вашей учетной записи службы автоматизации.  Его можно добавить в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации, чтобы обеспечить поддержку модулей Runbook службы автоматизации при условии, что вы используете одну и ту же учетную запись для решения и для членства в группе гибридных рабочих ролей Runbook.  Эта функция добавлена в версии 7.2.12024.0 гибридной рабочей роли Runbook.  

Перед началом развертывания гибридной рабочей роли Runbook ознакомьтесь со следующими сведениями о [требованиях к оборудованию и программному обеспечению](automation-offering-get-started.md#hybrid-runbook-worker) и [сведениями о подготовке сети](automation-offering-get-started.md#network-planning).  После успешного развертывания рабочей роли Runbook ознакомьтесь с [запуском модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md), чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде.  
 
### <a name="automated-deployment"></a>Автоматизированное развертывание

Выполните следующие действия для автоматизации установки и настройки гибридной рабочей роли Windows.  

1. Скачайте сценарий *New-OnPremiseHybridWorker.ps1* из [коллекции PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) непосредственно на компьютер, на котором выполняется гибридная рабочая роль Runbook, или на другой компьютер в вашей среде, после чего скопируйте этот сценарий в рабочую роль.  

    Для выполнения сценария *New-OnPremiseHybridWorker.ps1* необходимо задать следующие параметры.

  * *AutomationAccountName* (обязательный) — имя учетной записи службы автоматизации;  
  * *ResourceGroupName* (обязательный) — имя группы ресурсов, связанной с вашей учетной записью автоматизации.  
  * *HybridGroupName* (обязательный) — имя группы гибридных рабочих ролей Runbook, которую вы указываете в качестве целевой для модулей runbook, поддерживающих этот сценарий. 
  *  *SubscriptionID* (обязательный) — идентификатор подписки Azure, которая используется для учетной записи автоматизации.
  *  *WorkspaceName* (необязательный) — имя рабочей области OMS.  Если у вас нет рабочей области OMS, скрипт создаст и настроит ее.  

     > [!NOTE]
     > В настоящее время интеграция с OMS поддерживается только для следующих регионов службы автоматизации: **Юго-восточная Австралия**, **Восточная часть США 2**, **Юго-Восточная Азия** и **Западная Европа**.  Если ваша учетная запись службы автоматизации не находится в одном из этих регионов, скрипт все равно создаст рабочую область OMS, но предупредит, что связывание невозможно.
     > 
2. На своем компьютере запустите **Windows PowerShell** на **начальном** экране в режиме администратора.  
3. В оболочке командной строки PowerShell перейдите к папке, которая содержит скачанный сценарий, и выполните его, изменив значения параметров *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* и *-WorkspaceName*.

     > [!NOTE] 
     > После выполнения скрипта вы увидите запрос на аутентификацию в Azure.  Для входа **необходимо** использовать учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Также вы получите предложение подтвердить установку пакета **NuGet** и указать учетные данные Azure для аутентификации.<br><br> ![Выполнение сценария New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. После завершения сценария в колонке "Группы гибридных рабочих ролей" отобразится новая группа и число элементов в ней с учетом только что добавленных.  Вы можете выбрать группу из списка на вкладке **Группы гибридных рабочих ролей** и щелкнуть колонку **Гибридные рабочие роли**.  В колонке **Гибридные рабочие роли** отображается список элементов группы.  

### <a name="manual-deployment"></a>Развертывание вручную 
Выполните первые два шага для среды автоматизации, а затем повторите остальные шаги для каждого компьютера с компонентом Worker.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Создайте рабочую область Operations Management Suite
Если у вас еще нет рабочей области Operations Management Suite, [создайте](../log-analytics/log-analytics-manage-access.md) ее. Если у вас уже есть рабочая область, вы можете использовать ее.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2) Добавьте решение автоматизации в рабочую область Operations Management Suite
Решения расширяют функциональные возможности Operations Management Suite.  Решение автоматизации расширяет функциональные возможности службы автоматизации Azure, включая поддержку гибридных компонентов Runbook Worker.  Когда вы добавляете решение в рабочую область, она автоматически загружает компоненты рабочей роли на компьютер агента (вы установите его на следующем этапе).

Выполните инструкции в статье [Добавление решения с использованием коллекции решений](../log-analytics/log-analytics-add-solutions.md) , чтобы добавить решение **службы автоматизации** в рабочую область Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Установка Microsoft Monitoring Agent
Microsoft Monitoring Agent подключает компьютеры к Operations Management Suite.  Когда агент устанавливается на локальный компьютер и подключается к рабочей области, он автоматически скачивает компоненты, необходимые для гибридной рабочей роли Runbook.

Установите агент на локальный компьютер, следуя инструкциям в статье [Подключение компьютеров Windows к Log Analytics](../log-analytics/log-analytics-windows-agents.md).  Выполните эту процедуру на нескольких компьютерах, чтобы добавить в среду несколько компонентов Worker.

Агент, успешно подключенный к Operations Management Suite, появится на вкладке **Подключенные источники** в области **Параметры** Operations Management Suite.  Если агент скачал решение службы автоматизации правильно, в каталоге C:\Program Files\Microsoft Monitoring Agent\Agent появится папка **AzureAutomationFiles**.  Чтобы проверить версию гибридной рабочей роли Runbook, откройте папку C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ и найдите вложенную папку \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Установите среду модулей Runbook и выполните подключение к службе автоматизации Azure
При добавлении агента в Operational Management Suite решение службы автоматизации устанавливает модуль PowerShell **HybridRegistration**, который содержит командлет **Add-HybridRunbookWorker**.  Этот командлет используется для установки среды модулей Runbook на компьютере и ее регистрации в службе автоматизации Azure.

Откройте сеанс PowerShell с правами администратора и импортируйте модуль, выполнив указанные ниже команды.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

После этого запустите командлет **Add-HybridRunbookWorker** , используя следующий синтаксис:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Данные, необходимые для этого командлета, можно найти в колонке **Управление ключами** на портале Azure.  Откройте эту колонку, выбрав параметр **Ключи** в колонке **Параметры** для учетной записи службы автоматизации.

![Обзор гибридного компонента Runbook Worker](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Имя группы** — это имя группы гибридных рабочих ролей Runbook. Если эта группа уже существует в учетной записи автоматизации, то к ней добавляется текущий компьютер.  Если она еще не существует, то выполняется ее добавление.
* **Конечная точка** — это значение поля **URL-адрес** в колонке **Управление ключами**.
* **Токен** — это **первичный ключ доступа** в колонке **Управление ключами**.  

Для получения подробных сведений об установке используйте параметр **-Verbose** с командлетом **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Установка модулей PowerShell
Модули Runbook могут использовать любые из действий и командлетов, которые определены в модулях, установленных в вашей среде службы автоматизации Azure.  Эти модули не разворачиваются на локальных компьютерах автоматически, поэтому их необходимо устанавливать вручную.  Исключением является модуль Azure, который устанавливается по умолчанию и предоставляет доступ к командлетам для всех служб и действий службы автоматизации Azure.

Так как главной целью функции гибридной рабочей роли Runbook является управление локальными ресурсами, вам могут потребоваться модули, которые поддерживают эти ресурсы.  Сведения об установке модулей Windows PowerShell можно найти в разделе [Установка модулей](http://msdn.microsoft.com/library/dd878350.aspx) .  Устанавливаемые модули должны находиться в расположении, указанном в переменной среды PSModulePath, чтобы гибридная рабочая роль автоматически импортировала их.  Дополнительные сведения см. в статье [об изменении пути установки PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="installing-linux-hybrid-runbook-worker"></a>Установка гибридной рабочей роли Runbook Linux
Установка и настройка гибридной рабочей роли Runbook в Linux — очень простая процедура, позволяющая вручную установить и настроить роли.  Для этого необходимо включить решение **гибридной рабочей роли службы автоматизации** в рабочей области OMS, а затем выполнить несколько команд для регистрации компьютера в качестве рабочей роли и его добавления в новую или имеющуюся группу. 

1.  Включите решение "Гибридная рабочая роль службы автоматизации" в OMS. Для этого нужно:

   1. Из коллекции решений на [портале OMS](https://mms.microsoft.com) включите решение **Гибридная рабочая роль службы автоматизации**.
   2. Выполните следующий командлет:

        ```$null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```
2.  Run the following command with the proper parameters (endpoint and key can be taken from the portal from the automation account linked to the workspace used in the steps above):
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <automationsharedkey> --groupname <hybridgroupname> -e <automationendpoint>


## Removing Hybrid Runbook Worker 
You can remove one or more Hybrid Runbook Workers from a group or you can remove the group, depending on your requirements.  To remove a Hybrid Runbook Worker from an on-premises computer, perform the following steps.

1. In the Azure portal, navigate to your Automation account.  
2. From the **Settings** blade, select **Keys** and note the values for field **URL** and **Primary Access Key**.  You need this information for the next step.
3. Open a PowerShell session in Administrator mode and run the following command - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use the **-Verbose** switch for a detailed log of the removal process.

> [!NOTE]
> This does not remove the Microsoft Monitoring Agent from the computer, only the functionality and configuration of the Hybrid Runbook Worker role.  

## Remove Hybrid Worker groups
To remove a group, you first need to remove the Hybrid Runbook Worker from every computer that is a member of the group using the procedure shown earlier, and then you perform the following steps to remove the group.  

1. Open the Automation account in the Azure portal.
2. Select the **Hybrid Worker Groups** tile and in the **Hybrid Worker Groups** blade, select the group you wish to delete.  After selecting the specific group, the **Hybrid worker group** properties blade is displayed.<br> ![Hybrid Runbook Worker Group Blade](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. On the properties blade for the selected group, click **Delete**.  A message appears asking you to confirm this action, select **Yes** if you are sure you want to proceed.<br> ![Delete Group Confirmation Dialog](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> This process can take several seconds to complete and you can track its progress under **Notifications** from the menu.  

## Troubleshooting 
The Hybrid Runbook Worker depends on the Microsoft Monitoring Agent to communicate with your Automation account to register the worker, receive runbook jobs, and report status. If  registration of the worker fails, here are some possible causes for the error:  

1. The hybrid worker is behind a proxy or firewall.  
    Verify the computer has outbound access to *.azure-automation.net on port 443.  

2. The computer the hybrid worker is running on has less than the minimum hardware [requirements](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computers running the Hybrid Runbook Worker should meet the minimum hardware requirements before designating it to host this feature. Otherwise, depending on the resource utilization of other background processes and contention caused by runbooks during execution, the computer will become over utilized and cause runbook job delays or timeouts.
    Confirm the computer designated to run the Hybrid Runbook Worker feature meets the minimum hardware requirements.  If it does, monitor CPU and memory utilization to determine any correlation between the performance of Hybrid Runbook Worker processes and Windows.  If there is memory or CPU pressure, this may indicate the need to upgrade or add additional processors, or increase memory to address the resource bottleneck and resolve the error. Alternatively, select a different compute resource that can support the minimum requirements and scale when workload demands indicate an increase is necessary.
    
3. The Microsoft Monitoring Agent service is not running.  
    If the Microsoft Monitoring Agent Windows service is not running, this prevents the Hybrid Runbook Worker from communicating with Azure Automation.  Verify the agent is running by entering the following command in PowerShell: `get-service healthservice`.  If the service is stopped, enter the following command in PowerShell to start the service: `start-service healthservice`.  

4. In the **Application and Services Logs\Operations Manager** event log, you see event 4502  and EventMessage containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** with the following description:  *The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. The article KB3126513 has additional troubleshooting information for connectivity issues.*
    This can be caused by your proxy or network firewall blockking communication to Microsoft Azure.  Verify the computer has outbound access to *.azure-automation.net on ports 443.

Logs are stored locally on each hybrid worker at C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  You can check if there are any warning or error events written to the **Application and Services Logs\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** event log that would indicate a connectivity or other issue affecting onboarding of the role to Azure Automation or issue while performing normal operations.  

## Next steps
Review [run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

