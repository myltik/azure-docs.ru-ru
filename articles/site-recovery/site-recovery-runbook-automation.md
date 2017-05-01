---
title: "Добавление модулей Runbook службы автоматизации Azure в планы восстановления в Site Recovery | Документация Майкрософт"
description: "В этой статье описывается, как Azure Site Recovery позволяет расширить планы восстановления с помощью службы автоматизации Azure для выполнения сложных задач во время восстановления в Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 198caeea693fbc48b6e0eb1c9c8ee559e0553261
ms.lasthandoff: 03/31/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Добавление модулей Runbook службы автоматизации Azure в планы восстановления
В этом руководстве описывается, как Azure Site Recovery интегрируется со службой автоматизации Azure для обеспечения расширяемости для планов восстановления. Планы восстановления позволяют управлять восстановлением виртуальных машин, защищенных с помощью Azure Site Recovery, как для репликации в дополнительное облако, так и для репликации в сценарии Azure. Они также помогают реализовать **точное**, **воспроизводимое** и **автоматическое** восстановление. Если выполняется отработка отказа с переносом виртуальных машин в Azure, интеграция со службой автоматизации Azure позволяет расширить планы восстановления и предоставляет возможность выполнять Runbook, а это, в свою очередь, позволяет значительно облегчить выполнение задач автоматизации.

Если вы еще не знакомы со службой автоматизации Azure, зарегистрируйтесь [здесь](https://azure.microsoft.com/services/automation/) и скачайте примеры сценариев [здесь](https://azure.microsoft.com/documentation/scripts/). Дополнительные сведения об [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) и о том, как управлять восстановлением в Azure с помощью планов восстановления, см. [здесь](https://azure.microsoft.com/blog/?p=166264).

В этом руководстве мы описываем, как интегрировать модули Runbook службы автоматизации Azure в планы восстановления. Мы автоматизируем выполнение простых задач, которые ранее требовалось выполнять вручную, и узнаем, как преобразовать многоэтапный процесс восстановления в действие восстановления, запускаемое одним щелчком.

## <a name="customize-the-recovery-plan"></a>Настройка плана восстановления
1. Начнем с открытия колонки ресурсов плана восстановления. Как вы видите, в план восстановления добавлено две виртуальные машины для восстановления.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Чтобы добавить модуль Runbook, нажмите кнопку "Настроить". 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Щелкните правой кнопкой мыши начальную группу 1 и выберите "Добавить завершающее действие".
2. Щелкните, чтобы выбрать сценарий в новой колонке.
3. Назовите этот сценарий "Hello, World".
4. Выберите имя учетной записи службы автоматизации Azure. 
    >[!NOTE]
    > Учетная запись службы автоматизации может находиться в любом географическом регионе Azure, но должна быть в той же подписке, что и хранилище Site Recovery.
    
5. Выберите Runbook из учетной записи службы автоматизации. Этот модуль Runbook представляет сценарий, который будет запущен после восстановления первой группы во время выполнения плана восстановления.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Нажмите кнопку "ОК", чтобы сохранить этот сценарий. Сценарий добавляется в группу завершающих действий "Группа 1: запуск".

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Ключевые моменты, связанные с добавлением сценария
1. Можно щелкнуть сценарий правой кнопкой мыши и выбрать "Удалить шаг" или "Изменить скрипт".
2. Сценарий можно запустить в Azure во время отработки отказа из локальной среды в Azure. Кроме того, он может быть запущен в Azure как первичный сценарий перед завершением работы во время восстановления размещения из Azure в локальную среду.
3. При запуске сценарий внедряет контекст плана восстановления.

Ниже приведен пример того, как выглядит переменная контекста.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
                
                "ResourceGroupName":"ContosoRG",
                
                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",
                
                "RecoveryPointId":"TimeStamp"}

                }

        }


В следующей таблице указаны имена и описания всех переменных контекста.

| **Имя переменной** | **Описание** |
| --- | --- |
| RecoveryPlanName |Имя выполняемого плана. Эта переменная позволяет выполнять различные действия на основе имени, повторно используя сценарий |
| FailoverType |Указывает, является ли выполнение тестовым, запланированным или незапланированным. |
| FailoverDirection |Указывает, производится ли восстановление в первичное или вторичное расположение |
| GroupID |Определяет номер группы в плане восстановления при выполнении плана |
| VmMap |Это массив всех виртуальных машин в группе. |
| Ключ VMMap |Уникальный ключ (GUID) для каждой виртуальной машины. Это то же самое, что и VMM ID виртуальной машины, там, где это применимо. |
| SubscriptionId |Идентификатор подписки Azure, в которой создана виртуальная машина. |
| RoleName |Имя виртуальной машины Azure, для которой выполняется восстановление |
| CloudServiceName |Имя облачной службы Azure, под которой создана виртуальная машина. |
| ResourceGroupName|Имя группы ресурсов Azure, в которой создана виртуальная машина. |
| RecoveryPointId|Отметка времени восстановления виртуальной машины. |

Также убедитесь, что к учетной записи службы автоматизации добавлены следующие модули. Все эти модули должны иметь совместимые версии. Чтобы обеспечить это, проще всего брать все модули из последней доступной версии.
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>Доступ ко всем виртуальным машинам VmMap в цикле
Используйте следующий фрагмент кода, чтобы в цикле перебрать все виртуальные машины VmMap.

```
    $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
    $vmMap = $RecoveryPlanContext.VmMap
     foreach($VMID in $VMinfo)
     {
         $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
             #this check is to ensure that we skip when some data is not available else it will fail
         Write-output "Resource group name ", $VM.ResourceGroupName
         Write-output "Rolename " = $VM.RoleName
         }
     }

```

> [!NOTE]
> Когда сценарий используется как предварительное действие для групп загрузки, значения для имени группы ресурсов и имени роли не установлены. Эти значения заполняются, только если виртуальная машина из этой группы успешно прошла отработку отказа, а сценарий выполняется как завершающее действие в группе загрузки.

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>Использование одного модуля Runbook службы автоматизации с несколькими планами восстановления

Вы можете использовать один сценарий в нескольких планах восстановления, передавая ему внешние переменные. [Переменные службы автоматизации Azure](../automation/automation-variables.md) можно использовать для хранения параметров, которые передаются для выполнения плана восстановления. Если перед именем переменной вы укажете имя плана восстановления, для каждого плана восстановления будут созданы собственные переменные, которые можно использовать в качестве параметров. Эти параметры можно изменять, чтобы управлять работой скрипта, не изменяя его содержимое.

### <a name="using-simple-string-variables-in-runbook-script"></a>Использование простых строковых переменных в скрипте Runbook

Давайте рассмотрим скрипт, который принимает входные данные NSG и применяет их к виртуальным машинам в плане восстановления.

Чтобы определить для скрипта выполняемый сейчас план восстановления, используйте контекст плана восстановления.

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Чтобы применить существующую группу NSG, вам нужно знать имя NSG и группу ресурсов NSG. Мы предоставим эти переменные в качестве входных данных для скриптов плана восстановления. Для этого создайте две переменные в ресурсах учетной записи службы автоматизации, указав в качестве префикса имя плана восстановления, для которого вы создаете параметры.

1. Создайте переменную для хранения имени NSG. Перед ней также добавьте имя плана восстановления.
    ![Создание переменной для имени NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Создайте переменную для хранения имени группы ресурсов NSG. Перед ней также добавьте имя плана восстановления.
    ![Создание имени группы ресурсов NSG](media/site-recovery-runbook-automation-new/var2.png)


В скрипте извлеките значения этих переменных, используя следующий код:

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

Теперь вы можете использовать переменные в модуле Runbook, что позволит применить NSG к сетевому интерфейсу виртуальной машины, для которой выполняется отработка отказа.

```
     InlineScript { 
         if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
```

Для каждого плана восстановления создайте независимые переменные, чтобы скрипт можно было использовать повторно. Перед именами переменных добавьте имя плана восстановления. Полный скрипт, описанный в этом примере, доступен [здесь](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="using-complex-variable-to-store-more-information"></a>Использование сложной переменной для хранения дополнительных сведений

Теперь мы рассмотрим ситуацию, когда один сценарий должен включать общедоступный IP-адрес на виртуальных машинах. Этот же подход будет полезен, если нужно применить разные группы безопасности сети для нескольких (но не всех) виртуальных машин. Такой сценарий можно многократно использовать для других планов восстановления. Каждый план восстановления может иметь разное число виртуальных машин (например, точка восстановления SharePoint имеет два интерфейса, а простые бизнес-приложения — только один). В такой ситуации мы не можем просто создать отдельные переменные для каждого плана восстановления. Для этой задачи мы применим другую методику, создав в активах учетной записи службы автоматизации Azure [сложную переменную](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) с несколькими значениями. Для выполнения следующих действий потребуется Azure PowerShell.

1. С помощью Azure PowerShell войдите в вашу подписку.

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. Создайте сложную переменную для хранения параметров, имя которой будет совпадать с именем плана восстановления.

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    В этой сложной переменной параметр **VMDetails* содержит идентификатор защищенной виртуальной машины. Его можно найти в свойствах виртуальной машины на портале. Здесь мы создали переменную для хранения сведений о двух виртуальных машинах.

    ![Идентификаторы виртуальных машин для использования в качестве GUID](media/site-recovery-runbook-automation-new/vmguid.png)

3. Используйте эту переменную в модуле Runbook, чтобы применить группу NSG к виртуальной машине, если в контексте плана восстановления будет обнаружен любой из указанных идентификаторов VMGUID.

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. В модуле Runbook выполните циклический перебор виртуальных машин, входящих в контекст плана восстановления, проверяя наличие этих виртуальных машин в **$VMDetailsObj**. Если встречается совпадение, примените NSG, используя свойства переменной.
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap
           
        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID
            
            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Этот же скрипт можно использовать с разными планами восстановления, сохраняя в разных переменных значения параметров, применимых для соответствующих планов восстановления.

## <a name="sample-scripts"></a>Примеры сценариев
Разверните примеры сценариев в учетной записи службы автоматизации с помощью кнопки "Развертывание в Azure" внизу.

[![Развертывание в Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Можно также просмотреть краткое видео о восстановлении двухуровневого приложения WordPress в Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>дополнительные ресурсы.
[Учетная запись запуска от имени службы автоматизации Azure](../automation/automation-sec-configure-azure-runas-account.md)

[Обзор службы автоматизации Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Обзор службы автоматизации Azure")

[Примеры сценариев службы автоматизации Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Примеры сценариев службы автоматизации Azure")

