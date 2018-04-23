---
title: Добавление модулей Runbook службы автоматизации Azure в планы восстановления в Azure Site Recovery | Документы Майкрософт
description: Узнайте, как Azure Site Recovery помогает расширить планы восстановления с помощью службы автоматизации Azure. Узнайте, как выполнять сложные задачи во время восстановления в Azure.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: gauravd
editor: ''
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 942c7f13c5cb37017f8907c9f0c94493af53d008
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Добавление модулей Runbook службы автоматизации Azure в планы восстановления
В этой статье описывается, как Azure Site Recovery интегрируется со службой автоматизации Azure для обеспечения расширяемости планов восстановления. Планы восстановления могут управлять восстановлением виртуальных машин, защищенных с помощью Azure Site Recovery. Планы восстановления подходят как для репликации во вторичное облако, так и для репликации в Azure. Они также помогают реализовать **точное**, **воспроизводимое** и **автоматическое** восстановление. Если выполняется отработка отказа с переносом виртуальных машин в Azure, интеграция со службой автоматизации Azure позволяет расширить планы восстановления и предоставляет возможность выполнять Runbook, а это, в свою очередь, позволяет значительно облегчить выполнение задач автоматизации.

Если вы еще не знакомы со службой автоматизации Azure, зарегистрируйтесь [здесь](https://azure.microsoft.com/services/automation/) и скачайте примеры сценариев [здесь](https://azure.microsoft.com/documentation/scripts/). Дополнительные сведения об Azure Site Recovery и о том, как управлять восстановлением в Azure с помощью [планов восстановления](./site-recovery-create-recovery-plans.md), см. [здесь](https://azure.microsoft.com/services/site-recovery/).

В этой статье мы описываем, как интегрировать модули Runbook службы автоматизации Azure в планы восстановления. Мы используем примеры автоматизации простых задач, которые ранее требовалось выполнять вручную, и узнаем, как преобразовать многоэтапный процесс восстановления в действие восстановления, запускаемое одним щелчком мыши.

## <a name="customize-the-recovery-plan"></a>Настройка плана восстановления
1. Перейдите к колонке ресурсов плана восстановления **Azure Site Recovery**. В этом примере в план восстановления добавлено две виртуальные машины для восстановления. Чтобы добавить модуль Runbook, выберите вкладку **Настроить**.

    ![Нажмите кнопку "Настроить"](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Щелкните правой кнопкой мыши элемент **Группа 1: запуск**, а затем выберите **Добавить завершающее действие**.

    ![Щелкните правой кнопкой мыши элемент "Группа 1: запуск" и добавьте завершающее действие.](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Нажмите **Выбрать сценарий**.

4. В колонке **Действие обновления** присвойте сценарию имя **Hello World**.

    ![Колонка "Действие обновления"](media/site-recovery-runbook-automation-new/update-rp.png)

5. Введите имя учетной записи службы автоматизации Azure.
    >[!NOTE]
    > Учетная запись службы автоматизации может находиться в любом географическом регионе Azure, но должна быть в той же подписке, что и хранилище Azure Site Recovery.

6. В учетной записи службы автоматизации Azure выберите Runbook. Этот модуль Runbook представляет сценарий, который запускается после восстановления первой группы во время выполнения плана восстановления.

7. Нажмите кнопку **ОК**, чтобы сохранить этот сценарий. Сценарий будет добавлен в группу **Группа 1: последующие действия**.

    !["Группа 1: запуск", последующие действия](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Рекомендации по добавлению сценария

* Можно щелкнуть сценарий правой кнопкой мыши и выбрать **Удалить шаг** или **Обновить сценарий**.
* Сценарий можно запустить в Azure во время отработки отказа из локальной среды в Azure. Кроме того, он может быть запущен в Azure как первичный сценарий перед завершением работы во время восстановления размещения из Azure в локальную среду.
* При запуске сценарий внедряет контекст плана восстановления. Ниже приведен пример того, как выглядит переменная контекста.

    ```
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
    ```

    В следующей таблице перечислены имена и описания всех переменных контекста.

    | **Имя переменной** | **Описание** |
    | --- | --- |
    | RecoveryPlanName |Имя выполняемого плана. Эта переменная позволяет выполнять различные действия на основе имени плана восстановления, повторно используя сценарий. |
    | FailoverType |Указывает, является ли выполнение тестовым, запланированным или незапланированным. |
    | FailoverDirection |Указывает, производится ли восстановление на первичный или вторичный сайт. |
    | GroupID |Определяет номер группы в плане восстановления при выполнении плана. |
    | VmMap |Массив всех виртуальных машин в группе. |
    | Ключ VMMap |Уникальный ключ (GUID) для каждой виртуальной машины. Это то же самое, что и VMM ID виртуальной машины, там, где это применимо. |
    | SubscriptionId |Идентификатор подписки Azure, в которой создана виртуальная машина. |
    | RoleName |Имя виртуальной машины Azure, для которой выполняется восстановление. |
    | CloudServiceName |Имя облачной службы Azure, в которой создана виртуальная машина. |
    | ResourceGroupName|Имя группы ресурсов Azure, в которой создана виртуальная машина. |
    | RecoveryPointId|Отметка времени восстановления виртуальной машины. |

* Убедитесь, что в учетную запись службы автоматизации добавлены следующие модули.
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Все эти модули должны иметь совместимые версии. Чтобы обеспечить это, проще всего брать все модули из последней доступной версии.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Доступ ко всем виртуальным машинам VMMap в цикле
Используйте следующий фрагмент кода, чтобы в цикле перебрать все виртуальные машины VMMap.

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
> Когда сценарий используется как предварительное действие для групп загрузки, значения для имени группы ресурсов и имени роли не установлены. Эти значения заполняются, только если виртуальная машина из этой группы успешно прошла отработку отказа, а сценарий выполняется как последующее действие в группе загрузки.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Использование одного модуля Runbook службы автоматизации в нескольких планах восстановления

Один и тот же сценарий можно использовать в нескольких планах восстановления, передавая ему внешние переменные. [Переменные службы автоматизации Azure](../automation/automation-variables.md) можно использовать для хранения параметров, которые передаются для выполнения плана восстановления. Если перед именем переменной вы укажете имя плана восстановления, для каждого плана восстановления будут созданы собственные переменные, которые можно использовать в качестве параметров. Эти параметры можно изменять, чтобы управлять работой скрипта, не изменяя его содержимое, однако принцип работы сценария будет изменен.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Использование простых строковых переменных в сценарии Runbook

Рассмотрим сценарий, который принимает входные данные группы безопасности сети (NSG) и применяет их к виртуальным машинам в плане восстановления.

Чтобы определить для сценария текущий выполняемый план восстановления, используйте контекст плана восстановления.

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Чтобы применить существующую группу NSG, нужно знать имя NSG и имя группы ресурсов NSG. Используйте эти переменные в качестве входных данных для сценариев плана восстановления. Для этого создайте две переменные в ресурсах учетной записи службы автоматизации, указав в качестве префикса в имени переменной имя плана восстановления, для которого вы создаете параметры.

1. Создайте переменную для хранения имени NSG. В качестве префикса добавьте перед ней имя плана восстановления.

    ![Создание переменной для имени NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Создайте переменную для хранения имени группы ресурсов NSG. В качестве префикса добавьте перед ней имя плана восстановления.

    ![Создание имени группы ресурсов NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  В сценарии извлеките значения этих переменных, используя следующий код:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Используйте переменные в модуле Runbook, что позволит применить NSG к сетевому интерфейсу виртуальной машины, для которой выполняется отработка отказа.

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

Для каждого плана восстановления создайте независимые переменные, чтобы скрипт можно было использовать повторно. Перед именами переменных добавьте имя плана восстановления. Полный сценарий, описанный в этом примере, см. в разделе [Добавление общедоступного IP-адреса и NSG на виртуальные машины во время тестовой отработки отказа плана восстановления Azure Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Использование сложной переменной для хранения дополнительных сведений

Рассмотрим ситуацию, когда один сценарий должен включать общедоступный IP-адрес на определенных виртуальных машинах. Этот же подход будет полезен, если нужно применить разные группы безопасности сети для нескольких (но не всех) виртуальных машин. Такой сценарий можно многократно использовать для других планов восстановления. Каждый план восстановления может иметь разное число виртуальных машин. Например, точка восстановления SharePoint имеет два внешних интерфейса, а простые бизнес-приложения — только один. В такой ситуации мы не можем просто создать отдельные переменные для каждого плана восстановления.

В следующем примере мы применим другую методику, создав в ресурсах учетной записи службы автоматизации Azure [сложную переменную](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) с несколькими значениями. Для выполнения следующих действий потребуется Azure PowerShell.

1. Используя PowerShell, войдите в свою подписку Azure.

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Создайте сложную переменную для хранения параметров, присвоив ей имя плана восстановления.

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. В этой сложной переменной параметр **VMDetails** содержит идентификатор защищенной виртуальной машины. Этот идентификатор указан в свойствах виртуальной машины на портале Azure. На снимке экрана ниже показано, что мы создали переменную для хранения сведений о двух виртуальных машинах.

    ![Используйте идентификатор виртуальной машины в качестве идентификатора GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Используйте эту переменную в модуле runbook. Если указанный GUID виртуальной машины находится в контексте плана восстановления, примените группу NSG к виртуальной машине.

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. В модуле Runbook выполните циклический перебор виртуальных машин, входящих в контекст плана восстановления, проверяя наличие этих виртуальных машин в **$VMDetailsObj**. Если встречается совпадение, примените группу NSG, используя свойства переменной.

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

Этот же сценарий можно использовать с разными планами восстановления, сохраняя в разных переменных значения параметров, применимых для соответствующих планов восстановления.

## <a name="sample-scripts"></a>Примеры сценариев

Разверните примеры сценариев в учетной записи службы автоматизации с помощью кнопки **Развертывание в Azure**.

[![Развертывание в Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Просмотрите следующее видео, чтобы ознакомиться с другим примером, в котором показано, как выполнить восстановление двухуровневого приложения WordPress в Azure.


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Дополнительные ресурсы
* [Учетная запись запуска от имени службы автоматизации Azure](../automation/automation-create-runas-account.md)
* [Обзор службы автоматизации Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Обзор службы автоматизации Azure")
* [Примеры сценариев службы автоматизации Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Примеры сценариев службы автоматизации Azure")

## <a name="next-steps"></a>Дополнительная информация
[Дополнительные сведения](site-recovery-failover.md) о выполнении отработки отказа.
