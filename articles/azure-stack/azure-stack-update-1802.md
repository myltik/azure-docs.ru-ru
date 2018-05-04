---
title: Обновление 1802 Azure Stack | Документация Майкрософт
description: Узнайте, что находится в обновлении 1802 для интегрированных систем Azure Stack, об известных проблемах и где можно скачать обновление.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: c5237f8e97f76e5dc348322abeb16682aee62f3b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-stack-1802-update"></a>Обновление 1802 Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье представлены улучшения и исправления в пакете обновлений 1802, известные проблемы выпуска и сведения о том, где можно скачать обновление. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу обновления и проблемы со сборкой (после установки).

> [!IMPORTANT]        
> Этот пакет обновления предназначен только для интегрированных систем Azure Stack. Не применяйте этот пакет обновления к Пакету средств разработки Azure Stack.

## <a name="build-reference"></a>Указание сборки    
Номер сборки обновления 1802 Azure Stack — **20180302.1**.  


## <a name="before-you-begin"></a>Перед началом работы    
> [!IMPORTANT]    
> Не пытайтесь создавать виртуальные машины во время установки этого обновления. Дополнительные сведения об управлении обновлениями в Azure Stack см. в [этой статье](/azure-stack-updates#plan-for-updates).


### <a name="prerequisites"></a>предварительным требованиям
- Прежде чем применить обновление 1802 Azure Stack, установите [обновление 1712](azure-stack-update-1712.md) Azure Stack.    

- Прежде чем применить обновление 1802 Azure Stack, установите **исправление AzS — 1.0.180312.1, сборка 20180222.2**. Это исправление обновляет Защитник Windows. Оно доступно при загрузке обновлений для Azure Stack.

  Чтобы установить исправление, выполните обычные процедуры [установки обновлений Azure Stack](azure-stack-apply-updates.md). Имя обновления отображается как **Исправление AzS — 1.0.180312.1**. в обновлении содержатся следующие файлы: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  После отправки этих файлов в учетную запись хранения и контейнер запустите установку из плитки обновления на портале администрирования. 
  
  В отличие от обновлений Azure Stack, установка этого обновления не изменяет версию Azure Stack.  Чтобы подтвердить установку этого обновления, просмотрите список **установленных обновлений**.
 


### <a name="post-update-steps"></a>Действия после обновления
После установки 1802 установите все применимые исправления. Дополнительные сведения см. в статьях базы знаний по ссылке ниже, а также в статье о нашей [политике обслуживания](azure-stack-servicing-policy.md). 
- Исправление Azure Stack **1.0.180302.4**. [Статья базы знаний KB 4131152 о непригодности к использованию существующих масштабируемых наборов виртуальных машин]( https://support.microsoft.com/help/4131152). 

  Кроме того, это исправление позволяет устранить проблемы, описанные в статьей базы знаний [KB 4103348 об аварийном завершении работы службы API сетевого контроллера при попытке установить обновление Azure Stack](https://support.microsoft.com/help/4103348).


### <a name="new-features-and-fixes"></a>Новые функции и исправления
Это обновление включает следующие улучшения и исправления для Azure Stack.

- **Добавлена поддержка следующих версий API службы хранилища Azure**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Дополнительные сведения см. в статье [Azure Stack Storage: Differences and considerations](/azure/azure-stack/user/azure-stack-acs-differences) (Хранилище Azure Stack. Отличия и рекомендации).

- **Поддержка больших [блочных BLOB-объектов](azure-stack-acs-differences.md)**.
    - Максимально допустимый размер блока увеличен с 4 МБ до 100 МБ.
    - Максимальный размер большого двоичного объекта увеличен с 195 ГБ до 4,75 ТБ.  

- **Резервное копирование инфраструктуры** теперь отображается на плитке поставщиков ресурсов, и для резервного копирования включены оповещения. Дополнительные сведения о службе резервного копирования инфраструктуры см. в статье [Резервное копирование и восстановление данных для Azure Stack с помощью службы резервного копирования инфраструктуры](azure-stack-backup-infrastructure-backup.md).

- **Обновлен командлет *Test-AzureStack***, чтобы улучшить диагностику для хранения. Дополнительные сведения об этом командлете см. в статье [Запуск проверочного теста в Azure Stack](azure-stack-diagnostic-test.md).

- **Усовершенствования управления доступом на основе ролей (RBAC)**. Теперь с помощью RBAC можно делегировать разрешения для универсальных групп пользователей при развертывании Azure Stack с AD FS. Дополнительные сведения о RBAC см. в статье [Управление доступом на основе ролей](azure-stack-manage-permissions.md).

- **Добавлена поддержка нескольких доменов сбоя**.  Дополнительные сведения см. в статье [Основные возможности и концепции Azure Stack](azure-stack-key-features.md#high-availability-for-azure-stack).

- **Различные исправления** производительности, стабильности, безопасности и операционной системы, используемой службой Azure Stack.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления    
*Известные проблемы с установкой обновления 1802 отсутствуют.*


### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)
Ниже перечислены известные проблемы после установки для сборки **20180302.1**.

#### <a name="portal"></a>Microsoft Azure
- Возможность [открыть новый запрос на поддержку из раскрывающегося списка](azure-stack-manage-portals.md#quick-access-to-help-and-support) на портале администрирования недоступна. Вместо этого перейдите по следующей ссылке:     
    - Для интегрированных систем Azure Stack используйте https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Просмотр вычислений или ресурсов хранилища на портале администрирования может оказаться невозможным. Причиной является ошибка, которая произошла во время установки обновления. Из-за нее об обновлении было неверно сообщено (как об успешном). При возникновении этой ошибки обратитесь за помощью в службу поддержки корпорации Майкрософт.

- На портале может появиться пустая панель мониторинга. Чтобы восстановить панель мониторинга, щелкните значок шестеренки в правом верхнем углу портала и выберите **Восстановление параметров по умолчанию**.

- При просмотре свойств ресурсов или группы ресурсов кнопка **Перемещение** отключена. Это ожидаемое поведение. Перемещение ресурсов или групп ресурсов между группами ресурсов или подписками в настоящее время не поддерживается.

- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.

- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. Используйте PowerShell, чтобы проверить разрешения.

- На панели мониторинга на портале администрирования плитка "Обновление" не отображает информацию об обновлениях. Для устранения этой проблемы щелкните плитку, чтобы обновить ее.

-   На портале администрирования может появиться критическое оповещение о компоненте Microsoft.Update.Admin. В полях "Имя оповещения", "Описание" и "Исправление" отображается следующее сообщение:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (Ошибка. Отсутствует шаблон FaultType ResourceProviderTimeout).

    Это оповещение можно проигнорировать. 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- Если на портале администрирования и пользовательском портале выбрать колонку "Обзор" для учетных записей хранения, созданных в более ранней версии API (например, 2015-06-15), колонка не загрузится. К таким записям относятся такие системные учетные записи хранения, как **updateadminaccount**, которая используется во время исправления и обновления. 

  Чтобы избежать этой проблемы, выполните в PowerShell скрипт **Start-ResourceSynchronization.ps1**. Это позволит восстановить доступ к сведениям об учетной записи хранения. [Скрипт можно найти в GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts). Его нужно выполнить с учетными данными администратора для привилегированной конечной точки. 

- Не удается загрузить колонку **Работоспособность службы**. При открытии колонки "Работоспособность службы" на портале администрирования или пользовательском портале в Azure Stack отображается ошибка и не загружается информация. Это ожидаемое поведение. Хотя вы можете выбрать и открыть колонку "Работоспособность службы", эта функция еще недоступна. Она будет реализована в будущей версии Azure Stack.


#### <a name="health-and-monitoring"></a>Работоспособность и мониторинг
Известные проблемы после обновления до версии 1802 отсутствуют.

#### <a name="marketplace"></a>Marketplace
- Пользователи могут просматривать весь Marketplace без подписки и видеть некоторые административные элементы, такие как планы и предложения. Эти элементы бесполезны для пользователей.

#### <a name="compute"></a>Среда выполнения приложений
- Параметры масштабирования для масштабируемых наборов виртуальной машины на портале недоступны. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Из-за различий между версиями PowerShell используйте параметр `-Name` вместо параметра `-VMScaleSetName`.

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1802. This is due to the change in support for using availability sets with virtual machine scale sets. This support was added with version 1802.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message *Provisioning state failed*. 

  В версии 1803 эта проблема устранена. Чтобы устранить эту проблему для версии 1802, установите исправление Azure Stack **1.0.180302.4**. Дополнительные сведения см. в статье базы знаний [KB 4131152 о непригодности к использованию существующих масштабируемых наборов виртуальных машин]( https://support.microsoft.com/help/4131152). 

- Azure Stack поддерживает использование фиксированных VHD. Некоторые образы, предлагаемые в Marketplace для Azure Stack, используют динамические VHD, но они были удалены. Если изменить размер виртуальной машины, к которой подключен динамический диск, это приведет к сбою работы виртуальной машины.

  Чтобы устранить эту проблему, удалите виртуальную машину, не удаляя ее диск — большой двоичный объект VHD в учетной записи хранения. Затем преобразуйте VHD из динамического диска в диск с фиксированным размером и повторно создайте виртуальную машину.

- При создании группы доступности на портале (**Создать** > **Вычисления** > **Группа доступности**) вы можете создать ее только с одним доменом сбоя и одним доменом обновления. В качестве обходного решения при создании виртуальной машины создайте группу доступности с помощью PowerShell, CLI или на портале.

- При создании виртуальных машин на пользовательском портале Azure Stack отображается неверное число дисков данных, которые можно подключить к виртуальной машине серии DS. К виртуальным машинам серии DS можно подключить такое же количество дисков данных, которое доступно в конфигурации Azure.

- При сбое создания образа виртуальной машины неисправный элемент, который вы не смогли удалить, может быть добавлен в колонку вычислений образов виртуальной машины.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

-  Если подготовка расширения для развертывания виртуальной машины занимает слишком много времени, следует дождаться истечения времени ожидания, а не пытаться остановить процесс освобождения или удаления виртуальной машины.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>Сеть
- После создания виртуальной машины и ее связывания с общедоступным IP-адресом вы не можете отменить эту связь. Будет выглядеть так, будто вам удалось его отменить, но ранее присвоенный общедоступный IP-адрес останется связанным с исходной виртуальной машиной.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан.

- Служба внутренней балансировки нагрузки (ILB) неправильно обрабатывает MAC-адреса внутренних виртуальных машин, что нарушает работу ILB при использовании экземпляров Linux во внутренней сети.  Во внутренней сети ILB хорошо работает с экземплярами Windows.

-   Функция IP-пересылки видна на портале, однако ее включение ни на что не влияет. Пока такая возможность не поддерживается.

- Azure Stack поддерживает один *локальный сетевой шлюз* на IP-адрес. Это относится ко всем подпискам клиентов. После создания первого подключения к локальному сетевому шлюзу последующие попытки создать ресурс локального сетевого шлюза с тем же IP-адресом блокируются.

- В виртуальной сети, созданной с параметром DNS-сервера *Автоматический*, попытка изменить на пользовательский DNS-сервер завершается ошибкой. Обновленные параметры не передаются на виртуальные машины в этой виртуальной сети.

- Azure Stack не поддерживает добавление дополнительных сетевых интерфейсов к экземпляру виртуальной машины после ее развертывания. Если для виртуальной машины требуется несколько сетевых интерфейсов, их нужно определить во время развертывания.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Обходной путь для службы приложений. Если вам нужно подключение с помощью удаленного рабочего стола к экземплярам контроллера, измените правила безопасности в группах безопасности сети с помощью PowerShell.  Ниже приведены примеры того, как *разрешить* конфигурацию, а затем восстановить для нее состояние *запрета*:  
    
    - *Разрешить:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Запретить:*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL и MySQL
 - Прежде чем продолжить, просмотрите важную заметку в разделе [Перед началом работы](#before-you-begin).
- После создания развертывания SQL или MySQL может пройти до одного часа, прежде чем пользователи смогут создавать в нем базы данных.

- Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается и может привести к несоответствию состояний.  


> [!NOTE]  
> После обновления до Azure Stack 1802 вы можете продолжить использование ранее развернутых поставщиков ресурсов SQL и MySQL.  Рекомендуем обновлять SQL и MySQL при выходе нового выпуска. Применяйте обновления для поставщиков ресурсов SQL и MySQL последовательно (как и для Azure Stack).  Например, если вы используете версию 1710, сначала примените версию 1711, затем — 1712, а затем обновите до версии 1802.      
>   
> Установка обновления 1802 не влияет на текущее использование поставщиков ресурсов SQL или MySQL пользователями.
> Независимо от версии используемых поставщиков ресурсов, данные пользователей в базах данных не затрагиваются и остаются доступными.    


#### <a name="app-service"></a>Служба приложений
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.

- Чтобы масштабировать инфраструктуру (рабочие роли, роли управления, внешние роли), используйте PowerShell, как описано в заметках о выпуске служб вычислений.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Загрузка средств Azure Stack с сайта GitHub
- При использовании командлета PowerShell *invoke-webrequest* для загрузки средств Azure Stack с Github вы получите следующее сообщение об ошибке:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest. Запрос был прерван. Не удалось создать защищенный канал SSL/TLS).     

  Эта ошибка возникает из-за недавнего прекращения поддержки стандартов шифрования Tlsv1 и Tlsv1.1 в GitHub (по умолчанию для PowerShell). Дополнительные сведения см. в разделе [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Уведомление об удалении слабых стандартов шифрования).

  Чтобы устранить эту проблему, добавьте `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` в начало скрипта, чтобы консоль PowerShell использовала TLSv1.2 при загрузке из репозиториев GitHub.


## <a name="download-the-update"></a>Скачивание обновления
Пакет обновления 1802 Azure Stack можно загрузить [здесь](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Дополнительные сведения
Корпорация Майкрософт предоставляет способ отслеживания и возобновления обновлений с помощью привилегированной конечной точки, которая устанавливается вместе с обновлением 1710.

- Ознакомьтесь со статьей [Мониторинг обновлений в Azure Stack с помощью привилегированной конечной точки](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>См. также

- [Общие сведения об управлении обновлениями в Azure Stack](azure-stack-updates.md).
- [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).
