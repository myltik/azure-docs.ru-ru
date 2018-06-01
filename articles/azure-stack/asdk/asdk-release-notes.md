---
title: Заметки к выпуску Пакета средств разработки Azure Stack | Документация Майкрософт
description: Улучшения, исправления и известные проблемы Пакета средств разработки Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 9a396c51e8eb6262726b7590498500bfb4528924
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258410"
---
# <a name="azure-stack-development-kit-release-notes"></a>Заметки к выпуску Пакета средств разработки Azure Stack
В этой статье содержатся сведения об улучшениях, исправлениях и известных проблемах Пакета средств разработки Azure Stack. Если вы не знаете, какая версия используется, проверьте ее [с помощью портала](.\.\azure-stack-updates.md#determine-the-current-version).

> Будьте в курсе новых возможностей ASDK, подписавшись на этот [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).


## <a name="build-201805131"></a>Сборка 20180513.1

### <a name="new-features"></a>новые функции; 
Эта сборка включает в себя следующие улучшения и исправления для Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available:
  - *Подписка поставщика по умолчанию*. Используйте эту подписку только для базовой инфраструктуры. В ней не следует развертывать ресурсы или поставщики ресурсов.
  - *Подписка для контроля использования*. Используйте эту подписку для развертывания поставщика ресурсов. За ресурсы, развертываемые в этой подписке, плата не взимается.
  - *Подписка для контроля потребления*. Используйте эту подписку для прочих рабочих нагрузок, которые требуется развернуть. За ресурсы, развертываемые в ней, взимается плата в соответствии с обычными ценами.


### <a name="fixed-issues"></a>Исправленные проблемы
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Различные исправления** производительности, стабильности, безопасности и операционной системы, которую использует служба Azure Stack.

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Дополнительные выпуски, приуроченные к этому обновлению  
Ниже приведены компоненты, которые теперь доступны, но не требуют установки обновления 1804 для Azure Stack.
- **Обновление для пакета мониторинга System Center Operations Manager для Microsoft Azure Stack**. Новая версия (1.0.3.0) пакета мониторинга System Center Operations Manager для Microsoft Azure Stack доступна для [скачивания](https://www.microsoft.com/download/details.aspx?id=55184). С этой версией можно использовать субъекты-службы при добавлении подключенного к сети развертывания Azure Stack. Эта версия также включает в себя функции управления обновлениями, которые дают возможность выполнять действия по исправлению непосредственно из Operations Manager. В нее добавлены новые панели мониторинга, отображающие поставщики ресурсов, единицы масштабирования и узлы единиц масштабирования.

- **Новая версия модуля PowerShell для ресурсов администратора Azure Stack (1.3.0)**.  Модуль PowerShell для Azure Stack версии 1.3.0 теперь доступен для установки. Эта версия включает в себя команды для всех поставщиков ресурсов администратора, предназначенных для управления Azure Stack.  В этом выпуске будет считаться устаревшим некоторое содержимое [репозитория](https://github.com/Azure/AzureStack-Tools) инструментов GitHub для Azure Stack. 

   Сведения об установке приведены в [указаниях](.\.\azure-stack-powershell-install.md) или [справке](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) для модуля Azure Stack 1.3.0. 

- **Первоначальный выпуск справочника по REST API Azure Stack**. Опубликован [справочник по API для всех поставщиков ресурсов администратора Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/). 

### <a name="known-issues"></a>Известные проблемы
 
#### <a name="portal"></a>Microsoft Azure
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Для Пакета средств разработки используйте ссылку https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Элемент навигации](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (Ошибка. Отсутствует шаблон FaultType ResourceProviderTimeout).

    Это оповещение можно проигнорировать. 

#### <a name="compute"></a>Службы вычислений
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Сеть
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Чтобы обойти эту проблему, используйте план надстройки для повышения квоты сети, если план уже связан с подпиской. Чтобы получить дополнительные сведения, узнайте, как [сделать доступным план надстройки](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL и MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>Служба приложений Azure
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Использование  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->






## <a name="build-201803291"></a>Сборка 20180329.1

### <a name="new-features-and-fixes"></a>Новые функции и исправления
Новые возможности и исправления для интегрированных систем Azure Stack версии 1803 применяются к Пакету средств разработки Azure Stack. Ознакомьтесь с разделами [Новые функции](.\.\azure-stack-update-1803.md#new-features) и [Исправленные проблемы](.\.\azure-stack-update-1803.md#fixed-issues), в которых содержатся заметки о выпуске обновлений 1803 Azure Stack.  
> [!IMPORTANT]    
> Некоторые из элементов, перечисленные в разделах **Новые функции** и **Исправленные проблемы**, применимы только к интегрированным системам Azure Stack.

### <a name="changes"></a>изменения
- Изменился способ изменения состояния созданного предложения с *частного* на *общедоступное* или *резервное*. Дополнительные сведения см. в статье [Создание предложения в Azure Stack](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Известные проблемы
 
#### <a name="portal"></a>Microsoft Azure
- Возможность [открыть новый запрос на поддержку из раскрывающегося списка](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) на портале администрирования недоступна. Вместо этого перейдите по следующей ссылке:     
    - Для Пакета средств разработки используйте ссылку https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Вы увидите предупреждение **Требуется активация**. Это значит, что вам необходимо зарегистрировать Пакет средств разработки Azure Stack. Это ожидаемое поведение.

- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.

- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. Используйте PowerShell, чтобы проверить разрешения.

- На панели мониторинга на портале администрирования плитка "Обновление" не отображает информацию об обновлениях. Для устранения этой проблемы щелкните плитку, чтобы обновить ее.

-   На портале администрирования может появиться критическое оповещение о компоненте Microsoft.Update.Admin. В полях "Имя оповещения", "Описание" и "Исправление" отображается следующее сообщение:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (Ошибка. Отсутствует шаблон FaultType ResourceProviderTimeout).

    Это оповещение можно проигнорировать. 



#### <a name="marketplace"></a>Marketplace
- Пользователи могут просматривать весь Marketplace без подписки и видеть некоторые административные элементы, такие как планы и предложения. Эти элементы бесполезны для пользователей.
 
#### <a name="compute"></a>Службы вычислений
- Параметры масштабирования для масштабируемых наборов виртуальной машины на портале недоступны. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Из-за различий между версиями PowerShell используйте параметр `-Name` вместо параметра `-VMScaleSetName`.

- При создании виртуальных машин на пользовательском портале Azure Stack отображается неверное число дисков данных, которые можно подключить к виртуальной машине серии DS. К виртуальным машинам серии DS можно подключить такое же количество дисков данных, которое доступно в конфигурации Azure.

- При сбое создания образа виртуальной машины неисправный элемент, который вы не смогли удалить, может быть добавлен в колонку вычислений образов виртуальной машины.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

-  Если подготовка расширения для развертывания виртуальной машины занимает слишком много времени, следует дождаться истечения времени ожидания, а не пытаться остановить процесс освобождения или удаления виртуальной машины.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Сеть
- Если в разделе **Сеть** вы щелкнете **Подключение** для настройки подключения VPN, в списке возможных подключений появится **Виртуальная сеть — виртуальная сеть**. Не выбирайте это подключение. В настоящее время поддерживается только подключение **Сеть — сеть (IPsec)**.

- После создания виртуальной машины и ее связывания с общедоступным IP-адресом вы не можете отменить эту связь. Будет выглядеть так, будто вам удалось его отменить, но ранее присвоенный общедоступный IP-адрес останется связанным с исходной виртуальной машиной.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан.



- Azure Stack поддерживает один *локальный сетевой шлюз* на IP-адрес. Это относится ко всем подпискам клиентов. После создания первого подключения к локальному сетевому шлюзу последующие попытки создать ресурс локального сетевого шлюза с тем же IP-адресом блокируются.

- В виртуальной сети, созданной с параметром DNS-сервера *Автоматический*, попытка изменить на пользовательский DNS-сервер завершается ошибкой. Обновленные параметры не передаются на виртуальные машины в этой виртуальной сети.
 
- Azure Stack не поддерживает добавление дополнительных сетевых интерфейсов к экземпляру виртуальной машины после ее развертывания. Если для виртуальной машины требуется несколько сетевых интерфейсов, их нужно определить во время развертывания.



#### <a name="sql-and-mysql"></a>SQL и MySQL 
- После создания новых номеров SKU для SQL или MySQL может пройти до одного часа, прежде чем пользователи смогут создавать в них базы данных.

- Необходимо определить серверы размещения баз данных, чтобы их могли использовать поставщик ресурсов и пользовательские рабочие нагрузки. Нельзя использовать экземпляр, который используется любым другим объектом-получателем, включая службы приложений.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>Служба приложений Azure
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.

- Чтобы масштабировать инфраструктуру (рабочие роли, роли управления, внешние роли), используйте PowerShell, как описано в заметках о выпуске служб вычислений.
 
#### <a name="usage"></a>Использование  
- Вместо метки *TimeDate*, которая показывает время создания записи, в данных инструмента для отслеживания использования общедоступного IP-адреса отображается одно значение *EventDateTime* для каждой записи. В настоящее время эти данные непригодны для выполнения точного расчета и получения данных об использовании общедоступного IP-адреса.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Загрузка средств Azure Stack с сайта GitHub
- При использовании командлета PowerShell *invoke-webrequest* для загрузки средств Azure Stack с Github вы получите следующее сообщение об ошибке:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest. Запрос был прерван. Не удалось создать защищенный канал SSL/TLS).     

  Эта ошибка возникает из-за недавнего прекращения поддержки стандартов шифрования Tlsv1 и Tlsv1.1 в GitHub (по умолчанию для PowerShell). Дополнительные сведения см. в разделе [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Уведомление об удалении слабых стандартов шифрования).

  Чтобы устранить эту проблему, добавьте `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` в начало скрипта, чтобы консоль PowerShell использовала TLSv1.2 при загрузке из репозиториев GitHub.






## <a name="build-201803021"></a>Сборка 20180302.1

### <a name="new-features-and-fixes"></a>Новые функции и исправления
Ознакомьтесь с разделом [Новые функции и исправления](.\.\azure-stack-update-1802.md#new-features-and-fixes), в котором содержатся заметки о выпуске обновлений 1802 Azure Stack для интегрированных систем Azure Stack.

> [!IMPORTANT]    
> Некоторые из элементов, перечисленные в разделе **Новые функции и исправления**, применимы только к интегрированным системам Azure Stack.


### <a name="known-issues"></a>Известные проблемы
 
#### <a name="portal"></a>Microsoft Azure
- Возможность [открыть новый запрос на поддержку из раскрывающегося списка](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) на портале администрирования недоступна. Вместо этого перейдите по следующей ссылке:     
    - Для Пакета средств разработки используйте ссылку https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Вы увидите предупреждение **Требуется активация**. Это значит, что вам необходимо зарегистрировать Пакет средств разработки Azure Stack. Это ожидаемое поведение.

- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.

- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. Используйте PowerShell, чтобы проверить разрешения.

- На панели мониторинга на портале администрирования плитка "Обновление" не отображает информацию об обновлениях. Для устранения этой проблемы щелкните плитку, чтобы обновить ее.

-   На портале администрирования может появиться критическое оповещение о компоненте Microsoft.Update.Admin. В полях "Имя оповещения", "Описание" и "Исправление" отображается следующее сообщение:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (Ошибка. Отсутствует шаблон FaultType ResourceProviderTimeout).

    Это оповещение можно проигнорировать. 

- Если на портале администрирования и пользовательском портале выбрать колонку "Обзор" для учетных записей хранения, созданных в более ранней версии API (например, 2015-06-15), колонка не загрузится. 

  Чтобы избежать этой проблемы, выполните в PowerShell скрипт **Start-ResourceSynchronization.ps1**. Это позволит восстановить доступ к сведениям об учетной записи хранения. [Скрипт можно найти в GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts). Если вы используете ASDK, этот скрипт нужно выполнить с учетными данными администратора на узле комплекта разработки.  

- Не удается загрузить колонку **Работоспособность службы**. При открытии колонки "Работоспособность службы" на портале администрирования или пользовательском портале в Azure Stack отображается ошибка и не загружается информация. Это ожидаемое поведение. Хотя вы можете выбрать и открыть колонку "Работоспособность службы", эта функция еще недоступна. Она будет реализована в будущей версии Azure Stack.

#### <a name="health-and-monitoring"></a>Работоспособность и мониторинг
На портале администрирования Azure Stack может появиться критическое оповещение **Pending external certificate expiration** (Ожидание истечения срока действия внешнего сертификата).  Это предупреждение можно проигнорировать — оно никак не влияет на работу пакета средств разработки Azure Stack. 


#### <a name="marketplace"></a>Marketplace
- Пользователи могут просматривать весь Marketplace без подписки и видеть некоторые административные элементы, такие как планы и предложения. Эти элементы бесполезны для пользователей.
 
#### <a name="compute"></a>Службы вычислений
- Параметры масштабирования для масштабируемых наборов виртуальной машины на портале недоступны. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Из-за различий между версиями PowerShell используйте параметр `-Name` вместо параметра `-VMScaleSetName`.

- Azure Stack поддерживает использование фиксированных VHD. Некоторые образы, предлагаемые в Marketplace для Azure Stack, используют динамические VHD, но они были удалены. Если изменить размер виртуальной машины, к которой подключен динамический диск, это приведет к сбою работы виртуальной машины.

  Чтобы устранить эту проблему, удалите виртуальную машину, не удаляя ее диск — большой двоичный объект VHD в учетной записи хранения. Затем преобразуйте VHD из динамического диска в диск с фиксированным размером и повторно создайте виртуальную машину.

- При создании виртуальных машин на пользовательском портале Azure Stack на портале отображается неверное число дисков данных, которые можно вложить в виртуальную машину серии DS. К виртуальным машинам серии DS можно подключить такое же количество дисков данных, которое доступно в конфигурации Azure.

- При сбое создания образа виртуальной машины неисправный элемент, который вы не смогли удалить, может быть добавлен в колонку вычислений образов виртуальной машины.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

-  Если подготовка расширения для развертывания виртуальной машины занимает слишком много времени, следует дождаться истечения времени ожидания, а не пытаться остановить процесс освобождения или удаления виртуальной машины.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Сеть
- Если в разделе **Сеть** вы щелкнете **Подключение** для настройки подключения VPN, в списке возможных подключений появится **Виртуальная сеть — виртуальная сеть**. Не выбирайте это подключение. В настоящее время поддерживается только подключение **Сеть — сеть (IPsec)**.

- После создания виртуальной машины и ее связывания с общедоступным IP-адресом вы не можете отменить эту связь. Будет выглядеть так, будто вам удалось его отменить, но ранее присвоенный общедоступный IP-адрес останется связанным с исходной виртуальной машиной.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан.

-   Функция IP-пересылки видна на портале, однако ее включение ни на что не влияет. Пока такая возможность не поддерживается.

- Azure Stack поддерживает один *локальный сетевой шлюз* на IP-адрес. Это относится ко всем подпискам клиентов. После создания первого подключения к локальному сетевому шлюзу последующие попытки создать ресурс локального сетевого шлюза с тем же IP-адресом блокируются.

- В виртуальной сети, созданной с параметром DNS-сервера *Автоматический*, попытка изменить на пользовательский DNS-сервер завершается ошибкой. Обновленные параметры не передаются на виртуальные машины в этой виртуальной сети.
 
- Azure Stack не поддерживает добавление дополнительных сетевых интерфейсов к экземпляру виртуальной машины после ее развертывания. Если для виртуальной машины требуется несколько сетевых интерфейсов, их нужно определить во время развертывания.



#### <a name="sql-and-mysql"></a>SQL и MySQL 
- После создания новых номеров SKU для SQL или MySQL может пройти до одного часа, прежде чем пользователи смогут создавать в них базы данных.

- Необходимо определить серверы размещения баз данных, чтобы их могли использовать поставщик ресурсов и пользовательские рабочие нагрузки. Нельзя использовать экземпляр, который используется любым другим объектом-получателем, включая службы приложений.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>Служба приложений Azure
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.

- Чтобы масштабировать инфраструктуру (рабочие роли, роли управления, внешние роли), используйте PowerShell, как описано в заметках о выпуске служб вычислений.
 
#### <a name="usage"></a>Использование  
- Вместо метки *TimeDate*, которая показывает время создания записи, в данных инструмента для отслеживания использования общедоступного IP-адреса отображается одно значение *EventDateTime* для каждой записи. В настоящее время эти данные непригодны для выполнения точного расчета и получения данных об использовании общедоступного IP-адреса.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Загрузка средств Azure Stack с сайта GitHub
- При использовании командлета PowerShell *invoke-webrequest* для загрузки средств Azure Stack с Github вы получите следующее сообщение об ошибке:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest. Запрос был прерван. Не удалось создать защищенный канал SSL/TLS).     

  Эта ошибка возникает из-за недавнего прекращения поддержки стандартов шифрования Tlsv1 и Tlsv1.1 в GitHub (по умолчанию для PowerShell). Дополнительные сведения см. в разделе [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Уведомление об удалении слабых стандартов шифрования).

  Чтобы устранить эту проблему, добавьте `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` в начало скрипта, чтобы заставить консоль PowerShell использовать TLSv1.2 при загрузке из репозиториев GitHub.

