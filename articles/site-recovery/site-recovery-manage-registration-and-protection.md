---
title: "Удаление серверов и отключение защиты | Документация Майкрософт"
description: "В этой статье описывается, как отменить регистрацию серверов в хранилище Site Recovery, а также отключить защиту для виртуальных машин и физических серверов."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865
ms.lasthandoff: 01/05/2017


---

# <a name="remove-servers-and-disable-protection"></a>Удаление серверов и отключение защиты

Служба Azure Site Recovery помогает реализовать стратегию непрерывности бизнес-процессов и аварийного восстановления (BCDR). Она обеспечивает репликацию, отработку отказов и восстановление виртуальных машин и физических серверов. Виртуальные машины можно реплицировать в Azure или во вторичный локальный центр обработки данных. Краткий обзор см. в статье [Что такое Azure Site Recovery?](site-recovery-overview.md)

В этой статье описывается, как отменить регистрацию серверов в хранилище служб восстановления на портале Azure и как отключить защиту для виртуальных машин, защищенных с помощью Site Recovery.

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-connected-configuration-server"></a>Отмена регистрации подключенного сервера конфигурации

При репликации виртуальных машин VMware или физических серверов Windows или Linux в Azure можно отменить регистрацию подключенного сервера конфигурации в хранилище, используя следующие действия:

1. Отключите защиту виртуальной машины. Выберите **Защищенные элементы** > **Реплицированные элементы**, щелкните виртуальную машину правой кнопкой мыши и выберите **Удалить**.
2. Отмените связь для всех политик. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For VMware & Physical Machines (Для виртуальных машин VMware и физических компьютеров)** > **Политики репликации** и щелкните дважды соответствующую политику. Щелкните правой кнопкой мыши сервер конфигурации и выберите **Disassociate** (Отменить связь).
3. Удалите дополнительные локальные процессы или главные целевые серверы. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For VMware & Physical Machines (Для виртуальных машин VMware и физических компьютеров)** > **Серверы конфигурации**, а затем щелкните сервер правой кнопкой мыши и выберите **Удалить**.
4. Удалите сервер конфигурации.
5. Вручную удалите службу мобильности, запущенную на главном целевом сервере (это будет отдельный сервер или сервер конфигурации).
6. Удалите все дополнительные серверы обработки.
7. Удалите сервер конфигурации.
8. На сервере конфигурации удалите экземпляр MySQL, установленный службой Site Recovery.
9. В реестре сервера конфигурации удалите раздел ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

## <a name="unregister-a-unconnected-configuration-server"></a>Отмена регистрации неподключенного сервера конфигурации

При репликации виртуальных машин VMware или физических серверов Windows или Linux в Azure можно отменить регистрацию неподключенного сервера конфигурации в хранилище, используя следующие действия:

1. Отключите защиту виртуальной машины. Выберите **Защищенные элементы** > **Реплицированные элементы**, щелкните виртуальную машину правой кнопкой мыши и выберите **Удалить**. Выберите **Остановить управление виртуальной машиной**.
2. Удалите дополнительные локальные процессы или главные целевые серверы. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For VMware & Physical Machines (Для виртуальных машин VMware и физических компьютеров)** > **Серверы конфигурации**, а затем щелкните сервер правой кнопкой мыши и выберите **Удалить**.
3. Удалите сервер конфигурации.
4. Вручную удалите службу мобильности, запущенную на главном целевом сервере (это будет отдельный сервер или сервер конфигурации).
5. Удалите все дополнительные серверы обработки.
6. Удалите сервер конфигурации.
7. На сервере конфигурации удалите экземпляр MySQL, установленный службой Site Recovery.
8. В реестре сервера конфигурации удалите раздел ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

## <a name="unregister-a-connected-vmm-server"></a>Отмена регистрации подключенного сервера VMM

Лучше всего отменить регистрацию сервера VMM, когда он подключен к Azure. В таком случае настройки сервера VMM (и других серверов VMM со связанными облаками) будут удалены надлежащим образом. Удалять неподключенный сервер рекомендуется только в случае постоянных проблем с соединением. Если сервер VMM не подключен, необходимо вручную запустить скрипт, чтобы очистить параметры.

1. Остановите репликацию виртуальных машин в облаках на сервере VMM, который нужно удалить.
2. Удалите все сопоставления сетей, используемые облаками на этом сервере. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** > **Сетевое сопоставление**, а затем щелкните правой кнопкой мыши сетевое сопоставление и выберите **Удалить**.
3. Отмените связь с политиками репликации для облаков на сервере VMM, который нужно удалить.  Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** >  **Политики репликации** и щелкните дважды соответствующую политику. Щелкните облако правой кнопкой мыши и выберите **Disassociate** (Отменить связь).
4. Удалите сервер VMM или его запущенный узел. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** > **Серверы VMM**, а затем щелкните сервер правой кнопкой мыши и выберите **Удалить**.
5. Вручную удалите поставщика на сервере VMM. При наличии кластера удалите его со всех узлов.
6. При репликации в Azure вручную удалите агент служб восстановления Microsoft из узлов Hyper-V в удаленных облаках.



### <a name="unregister-an-unconnected-vmm-server"></a>Отмена регистрации неподключенного сервера VMM

1. Остановите репликацию виртуальных машин в облаках на сервере VMM, который нужно удалить.
2. Удалите все сопоставления сетей, используемые облаками на этом сервере. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** > **Сетевое сопоставление**, а затем щелкните правой кнопкой мыши сетевое сопоставление и выберите **Удалить**.
3. Запишите идентификатор сервера VMM.
4. Отмените связь с политиками репликации для облаков на сервере VMM, который нужно удалить.  Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** >  **Политики репликации** и щелкните дважды соответствующую политику. Щелкните облако правой кнопкой мыши и выберите **Disassociate** (Отменить связь).
5. Удалите сервер VMM или его запущенный узел. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** > **Серверы VMM**, а затем щелкните сервер правой кнопкой мыши и выберите **Удалить**.
6. На сервере VMM cкачайте и запустите [скрипт очистки](http://aka.ms/asr-cleanup-script-vmm). Откройте PowerShell с помощью команды **Запуск от имени администратора**, чтобы изменить политику выполнения для области по умолчанию (LocalMachine). В скрипте укажите идентификатор сервера VMM, который требуется удалить. Скрипт удалит с сервера информацию о регистрации и связывании облаков.
5. Запустите скрипт очистки на любом другом сервере VMM, который содержит облака, сопоставленные с облаками на сервере, который нужно удалить.
6. Запустите скрипт очистки на любом другом пассивном узле кластера VMM, на котором установлен поставщик.
7. Вручную удалите поставщика на сервере VMM. При наличии кластера удалите его со всех узлов.
8. При репликации в Azure можно удалить агент служб восстановления Microsoft из узлов Hyper-V в удаленных облаках.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Отмена регистрации узла Hyper-V на сайте Hyper-V

Узлы Hyper-V, которые не управляются с помощью VMM, расположены на сайте Hyper-V. Удалите узел на сайте Hyper-V следующим образом:

1. Отключите репликацию для виртуальных машин Hyper-V, расположенных на узле.
2. Отмените политики для сайта Hyper-V. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For Hyper-V Sites (Для сайтов Hyper-V)** >  **Политики репликации** и щелкните дважды соответствующую политику. Щелкните сайт правой кнопкой мыши и выберите **Отменить**.
3. Удалите узлы Hyper-V. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** > **Hyper-V Hosts** (Сайты Hyper-V), а затем щелкните сервер правой кнопкой мыши и выберите **Удалить**.
4. После того как все узлы на сайте Hyper-V будут удалены, удалите сайт Hyper-V. Выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **For System Center VMM (Для System Center VMM)** > **Hyper-V Sites** (Сайты Hyper-V), а затем щелкните сайт правой кнопкой мыши и выберите **Удалить**.
5. На каждом удаленном узле Hyper-V запустите приведенный ниже скрипт. Этот скрипт очистит параметры на сервере и отменит его регистрацию в хранилище.


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>Отключение защиты для виртуальной машины VMware или физического сервера

1. Выберите **Защищенные элементы** > **Реплицированные элементы**, щелкните виртуальную машину правой кнопкой мыши и выберите **Удалить**.
2. В разделе **Удалить виртуальную машину** выберите один из следующих параметров.
    - **Отключить защиту виртуальной машины (рекомендуется).** Используйте этот параметр, чтобы остановить репликацию виртуальной машины. Параметры Site Recovery будут очищены автоматически. Этот параметр отображается только в следующих случаях:
        - **Вы изменили размер тома виртуальной машины** — при изменении размера тома виртуальная машина переходит в критическое состояние. Выберите этот параметр, чтобы отключить защиту, сохраняя при этом точки восстановления в Azure. При повторном включении защиты для виртуальной машины данные для измененного тома будут переданы в Azure.
        - **Вы недавно выполнили отработку отказа** — после выполнения отработки отказа для тестирования среды выберите этот параметр, чтобы повторно включить защиту локальных компьютеров. Этот параметр отключает каждую виртуальную машину, затем для них потребуется включить защиту повторно. Отключение виртуальной машины с этим параметром не влияет на реплику виртуальной машины в Azure. Не удаляйте службу мобильности из виртуальной машины.
    - **Остановить управление виртуальной машиной.** При выборе этого параметра виртуальная машина будет удалена только из хранилища. Локальные параметры защиты для виртуальной машины не будут затронуты. Для удаления параметров на виртуальной машине, а также самой виртуальной машины из подписки Azure необходимо очистить параметры, удалив службу мобильности.

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>Отключение защиты для виртуальной машины Hyper-V в облаке VMM

1. Выберите **Защищенные элементы** > **Реплицированные элементы**, щелкните виртуальную машину правой кнопкой мыши и выберите **Удалить**.
2. В разделе **Удалить виртуальную машину** выберите один из следующих параметров.

    - **Отключить защиту виртуальной машины (рекомендуется).** Используйте этот параметр, чтобы остановить репликацию виртуальной машины. Параметры Site Recovery будут очищены автоматически.
    - **Остановить управление виртуальной машиной.** При выборе этого параметра виртуальная машина будет удалена только из хранилища. Локальные параметры защиты для виртуальной машины не будут затронуты. Для удаления параметров на виртуальной машине и удаления виртуальной машины из подписки Azure необходимо вручную очистить параметры, следуя инструкциям ниже. Обратите внимание, что если выбрать удаление виртуальной машины и ее жестких дисков, они будут удалены из целевого расположения.

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>Очистка параметров защиты при репликации на дополнительный сайт VMM

Если вы выбрали параметр **Остановить управление виртуальной машиной** и выполняете репликацию на дополнительный сайт, запустите этот скрипт на основном сервере, чтобы очистить параметры для основной виртуальной машины. В консоли VMM нажмите кнопку PowerShell, чтобы открыть консоль VMM PowerShell. Замените SQLVM1 именем виртуальной машины.

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. На вспомогательном сервере VMM выполните следующий сценарий, чтобы удалить параметры вспомогательной виртуальной машины:

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. На дополнительном сервере VMM обновите виртуальные машины на сервере узлов Hyper-V, чтобы дополнительная виртуальная машина была заново обнаружена в консоли VMM.
4. После выполнения описанных выше действий параметры репликации будут удалены на сервере VMM. Если вы хотите остановить репликацию для виртуальной машины, запустите приведенный ниже скрипт на основной и дополнительной виртуальной машинах. Замените SQLVM1 именем виртуальной машины.

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>Очистка параметров защиты при репликации в Azure

1. Если вы выбрали **Остановить управление виртуальной машиной** и выполняете репликацию в Azure, запустите этот скрипт на исходном сервере VMM, используя PowerShell из консоли VMM.
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. После выполнения описанных выше действий параметры репликации на сервере VMM будут удалены. Чтобы остановить репликацию для виртуальной машины, работающей на сервере узлов Hyper-V, запустите приведенный ниже скрипт. Замените SQLVM1 именем виртуальной машины и host01.contoso.com именем сервера узлов Hyper-V.

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Отключение защиты для виртуальной машины Hyper-V на сайте Hyper-V

Если вы реплицируете виртуальные машины Hyper-V в Azure, не используя сервер VMM, сделайте следующее:

1. Выберите **Защищенные элементы** > **Реплицированные элементы**, щелкните виртуальную машину правой кнопкой мыши и выберите **Удалить**.
2. В разделе **Удалить виртуальную машину** вы сможете выполнить следующие параметры:

   - **Отключить защиту виртуальной машины (рекомендуется).** Используйте этот параметр, чтобы остановить репликацию виртуальной машины. Параметры Site Recovery будут очищены автоматически.
   - **Остановить управление виртуальной машиной.** При выборе этого параметра виртуальная машина будет удалена только из хранилища. Локальные параметры защиты для виртуальной машины не будут затронуты. Для удаления параметров на виртуальной машине и удаления виртуальной машины из подписки Azure необходимо вручную очистить параметры. Если выбрать удаление виртуальной машины и ее жестких дисков, они будут удалены из целевого расположения.
3. Если вы выбрали **Остановить управление виртуальной машиной**, запустите этот скрипт на исходном сервере узлов Hyper-V, чтобы удалить репликацию для виртуальной машины. Замените SQLVM1 именем виртуальной машины.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

