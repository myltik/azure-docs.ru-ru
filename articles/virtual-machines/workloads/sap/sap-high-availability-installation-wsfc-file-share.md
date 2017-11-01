---
title: "Установка SAP NetWeaver высокого уровня доступности в Azure с использованием отказоустойчивого кластера Windows и общей папки для экземпляра SAP (A)SCS | Документация Майкрософт"
description: "Установка SAP NetWeaver высокого уровня доступности с использованием отказоустойчивого кластера Windows и общей папки для экземпляра SAP (A)SCS"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec7888cfb9b0d288b5b25f580c852ee32306684c
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-file-share-for-sap-ascs-instance-on-azure"></a>Установка SAP NetWeaver высокого уровня доступности с использованием отказоустойчивого кластера Windows и общей папки для экземпляра SAP (A)SCS в Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

В этом документе описываются шаги по установке и настройке в Azure высокодоступной системы SAP с использованием **отказоустойчивого кластера Windows (WSFC)** и **общей папки с возможностью масштабирования** для кластеризации экземпляра SAP (A)SCS.

## <a name="prerequisites"></a>Предварительные требования

Перед началом установки ознакомьтесь со следующими документами:

* [Clustering SAP (A)SCS Instance on **Windows Failover Cluster Using Cluster** **Shared Disk on Azure**][sap-high-availability-guide-wsfc-file-share] (Кластеризация экземпляра SAP (A)SCS в отказоустойчивом кластере Windows с помощью файлового ресурса в Azure)

* [Azure Infrastructure Preparation for SAP HA using **Windows Failover Cluster** and **File Share** for SAP (A)SCS Instance][sap-high-availability-infrastructure-wsfc-file-share] (Подготовка инфраструктуры Azure для SAP высокого уровня доступности с использованием отказоустойчивого кластера Windows и файлового ресурса для экземпляра SAP (A)SCS)


Вам понадобятся следующие исполняемые файлы и библиотеки DLL, доступные в SAP:
* Средство установки SAP **Software Provisioning Manager** (**SWPM**) версии **SPS21 (или более поздней)**.
* Загрузите **последний архив NTCLUST.SAR** с новой библиотеки ресурсов кластера SAP. Новые библиотеки кластера SAP поддерживают высокий уровень доступности SAP (A)SCS с использованием общей папки на отказоустойчивом кластере Windows Server.

  Дополнительные сведения о новой библиотеке ресурсов кластера SAP см. в [этой][sap-blog-new-sap-cluster-resource-dll] записи блога.

Мы не описываем настройку СУБД, так как этот процесс зависит от используемой СУБД. Тем не менее мы предполагаем, что задачи обеспечения высокого уровня доступности СУБД решены благодаря возможностям различных поставщиков СУБД, поддерживающих Azure. Например, AlwaysOn или зеркальное отображение базы данных для SQL Server и Oracle Data Guard для баз данных Oracle. В нашем примере сценария мы не реализуем дополнительную защиту СУБД.

Особые рекомендации по взаимодействию различных СУБД с такой кластеризованной конфигурацией SAP ASCS/SCS в Azure отсутствуют.

> [!NOTE]
> Процедуры установки систем SAP NetWeaver ABAP, SAP Java и SAP ABAP с Java практически идентичны. Главное отличие состоит в том, что в системе SAP ABAP всего один экземпляр ASCS. В системе SAP Java имеется один экземпляр SCS. В системе SAP ABAP с Java — один экземпляр ASCS и один экземпляр SCS, которые работают в одной группе отказоустойчивого кластера Майкрософт. Любые отличия в установке для каждого стека установки SAP NetWeaver будут указаны явным образом. Все остальные составляющие считаются одинаковыми.  
>
>

## <a name="install-ascs-instance-on-ascs-cluster"></a>Установка экземпляра (A)SCS в кластере (A)SCS

> [!IMPORTANT]
>
>В настоящее время настройка высокого уровня доступности с помощью параметров общего файлового ресурса не поддерживается средством установки SAP — Software Provisioning Manager (SWPM). Таким образом, чтобы установить систему SAP, необходимо выполнить некоторые действия вручную. Например, можно установить экземпляр SAP (A)SCS кластера и настроить отдельный глобальный узел SAP.  
>
>Остальные шаги по установке и кластеризации экземпляра DBMS и серверов приложений SAP остаются прежними.
>

### <a name="install-ascs-instance-on-local-drive"></a>Установка экземпляра (A)SCS на локальном диске

Установите экземпляр SAP (A)SCS на **двух** узлах кластера (A)SCS. Установите его на **локальном** диске. В нашем примере используется локальный диск C:\\. Вы можете выбрать любой другой локальный диск.  

Используя средство установки SAP SWPM, перейдите:

&lt;Продукт&gt; -> &lt;СУБД&gt; -> Установка -> Сервер приложений ABAP (или Java) -> Распределенная система -> Экземпляр (A)SCS

> [!IMPORTANT]
>В настоящее время сценарий с файловым ресурсом не поддерживается средством установки SAP SWPM, поэтому вы **не сможете использовать** путь установки:
>
>&lt;Продукт&gt; -> &lt;СУБД&gt; -> Установка -> Сервер приложений ABAP (или Java) -> Система высокой доступности -> …
>

### <a name="remove-sapmnt-and-create-saploc-file-share"></a>Удаление SAPMNT и создание файлового ресурса SAPLOC

С помощью SWMP был создан локальный файловый ресурс SAPMNT в папке C:\\usr\\sap.

Удалите файловые ресурсы SAPMNT на **двух** узлах кластера (A)SCS:

Выполните следующий сценарий PowerShell:

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Если файловый ресурс SAPLOC не существует, создайте его на двух узлах кластера ASCS.

Выполните следующий сценарий PowerShell:

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-sap-global-host-on-sofs-cluster"></a>Подготовка глобального узла SAP в кластере SOFS

На этом шаге создайте в кластере SOFS следующие том и файловый ресурс:

* Структуру файла C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS\ глобального узла SAP в общем томе кластера (CSV) SOFS.

* Создайте файловый ресурс SAPMNT.

* Задайте параметры безопасности для файлового ресурса SAPMNT и папки с расширенными возможностями управления:
    * группой пользователя **&lt;DOMAIN&gt;\SAP_&lt;SID&gt;_GlobalAdmin**
    * и **объектами на компьютере, где расположены узлы кластера SAP (A)SCS &lt;DOMAIN&gt;\ClusterNode1$ и &lt;DOMAIN&gt;\ClusterNode2$**.

Чтобы создать том CSV с зеркальной устойчивостью, описанной в **предварительных требованиях SAP для кластера SOFS в Azure (добавление ссылки)**, выполните следующий командлет PowerShell на одном из узлов кластера SOFS:


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
Чтобы создать файловый ресурс SAPMNT и задать параметры безопасности для папки и этого ресурса, выполните следующий сценарий PowerShell на одном из узлов кластера SOFS:

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP (A)SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascs-instances-and-sap-services"></a>Остановка работы экземпляров (A)SCS и служб SAP

Выполните следующие шаги:
* Остановите экземпляры SAP (A)SCS на двух узлах кластера (A)SCS.
* Остановите службы Windows SAP (A)SCS **SAP&lt;SID&gt;_&lt;номер_экземпляра&gt;** на двух узлах кластера.

## <a name="move-sys-folder-to-sofs-cluster"></a>Перемещение папки \SYS\... в кластер SOFS

Выполните следующие шаги:
* Скопируйте папку SYS (например, C:\usr\sap\\&lt;SID&gt;\SYS) из одного из узлов кластера (A)SCS в кластер SOFS, например сюда: C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS.
* Удалите папку C:\usr\sap\\&lt;SID&gt;\SYS из двух узлов кластера (A)SCS.

## <a name="update-cluster-security-setting-on-sap-ascs-cluster"></a>Обновление параметров безопасности кластера в кластере SAP (A)SCS

Выполните следующий сценарий PowerShell на одном из узлов кластера SAP (A)SCS:

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for <DOMAIn>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascs-instance"></a>Создание имени виртуального узла для кластеризованного экземпляра SAP (A)SCS

Создайте сетевое имя кластера SAP (A)SCS, например **pr1-ascs [10.0.6.7]**, как описано в разделе о [создании имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host].

## <a name="update-default-and-sap-ascs-instance-profile"></a>Обновление профиля экземпляра по умолчанию и SAP (A)SCS

Вам необходимо обновить профиль экземпляра по умолчанию и SAP (A)SCS &lt;SID&gt;_(A)SCS<Nr>_<Host>, чтобы использовать:

* новое имя виртуального узла SAP (A)SCS;

* новое имя глобального узла SAP.


| Прежние значения |  |
| --- | --- |
| Имя узла SAP (A)SCS = глобальный узел SAP | ascs-1 |
| Имя профиля экземпляра SAP (A)SCS | PR1_ASCS00_ascs-1 |

| Новые значения |  |
| --- | --- |
| Имя узла SAP (A)SCS | **pr1-ascs** |
| Глобальный узел SAP | **sapglobal** |
| Имя профиля экземпляра SAP (A)SCS | PR1\_ASCS00\_**pr1-ascs** |

### <a name="update-sap-default-profile"></a>Обновление профиля SAP по умолчанию


| Имя параметра | Значение параметра |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| enque/serverhost | **pr1-ascs** |

### <a name="update-sap-ascs-instance-profile"></a>Обновление имени профиля экземпляра SAP (A)SCS

| Имя параметра | Значение параметра |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\\**sapglobal**\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>Вы можете использовать командлет PowerShell **Update-SAPASCSSCSProfile**, чтобы автоматизировать обновление профиля.
>
>Командлет PowerShell поддерживает экземпляры SAP ABAP ASCS и SAP Java SCS.
>

Скопируйте **SAPScripts.ps1** на локальный диск C:\tmp и запустите следующий командлет PowerShell:

```PowerShell
Import-Module C:\tmp\SAPScripts.ps1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![Рис. 1. Выходные данные SAPScripts.ps1][sap-ha-guide-figure-8012]

_**Рис. 1.** Выходные данные SAPScripts.ps1_

## <a name="update-ltsidgtadm-user-environment-variable"></a>Обновление переменной среды пользователя &lt;sid&gt;adm

Обновите новый UNC-путь глобального узла среды пользователя &lt;sid&gt;adm на двух узлах кластера (A)SCS.
Войдите как пользователь &lt;sid&gt;adm и запустите программу Regedit.exe.
Выберите **HKEY_CURRENT_USER** -> **Среда** и обновите переменные новыми значениями:

| Переменная | Значение |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-new-saprcdll"></a>Установка новой библиотеки SAPRC.DLL

Вам необходимо установить новую версию ресурса кластера SAP, которая поддерживает сценарий с файловым ресурсом.

Загрузите последний пакет **NTCLUST.SAR** из Marketplace службы SAP.

Распакуйте его на одном из узлов кластера (A)SCS и запустите в командной строке следующую команду, чтобы установить новую библиотеку saprc.dll:

```
.\NTCLUST\insaprct.exe -yes -install
```

Она будет установлена на двух узлах кластера (A)SCS.

Дополнительные сведения см. в [примечании к SAP № 1596496 об обновлении библиотек ресурсов SAP для мониторинга кластерных ресурсов][1596496].

## <a name="create-sap-sid-cluster-group-network-name-and-ip"></a>Создание группы кластера <SID> SAP, сетевого имени и IP-адреса

Вам необходимо создать:

* группу кластера SAP &lt;SID&gt;;
* <(A)SCSсетевое_имя>;
* соответствующий IP-адрес.

Запустите приведенный ниже командлет PowerShell:

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create SAP ASCS instance Virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create SAP ASCS Virtual IP Address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set static IP Address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set Network DNS Name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start SAP <SID> Cluster Group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-sap-start-service-on-both-nodes"></a>Регистрация службы SAP START на двух узлах

Вам необходимо повторно зарегистрировать службу запуска SAP (A)SCS, чтобы указать новый профиль и путь к нему.

Запустить эту службу необходимо на двух узлах кластера (A)SCS.

В командной строке с повышенными привилегиями выполните следующую команду:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![Рис. 2. Повторная установка службы SAP][sap-ha-guide-figure-8013]

_**Рис. 2.** Повторная установка службы SAP_

Проверьте, чтобы параметры были введены правильно, а для типа запуска службы выберите **Вручную**.

## <a name="stop-ascs-service"></a>Остановка службы (A)SCS

Остановите службу SAP (A)SCS **SAP&lt;SID&gt;_ &lt;номер_экземпляра&gt;** на двух узлах кластера (A)SCS.

## <a name="create-new-sap-service-and-sap-instance-resources"></a>Создание службы SAP и ресурсов экземпляра SAP

Теперь необходимо завершить создание ресурсов группы кластера SAP&lt;SID&gt;. Например, вам нужно создать ресурсы:

* службу **SAP &lt;SID&gt; &lt;номер_экземпляра&gt;** и
* экземпляр **SAP &lt;SID&gt; &lt;номер_экземпляра&gt;**.

Выполните такой командлет PowerShell:

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP Instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP Instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Добавление порта пробы

На этом шаге настраивается порт пробы SAP-SID-IP кластерного ресурса SAP с помощью PowerShell. Настройку следует выполнять на одном из узлов кластера SAP ASCS/SCS, как описано [здесь][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-ers-instance-on-both-cluster-nodes"></a>Установка экземпляра ERS на двух узлах кластера

На следующем шаге необходимо установить экземпляр ERS на двух узлах кластера (A)SCS.
Параметры установки можно найти в меню SWPM:

&lt;Продукт&gt; -> &lt;СУБД&gt; -> Установка -> Additional SAP System instances (Дополнительные экземпляры системы SAP) -> **Enqueue Replication Server Instance** (Экземпляр сервера постановки репликации в очередь)

## <a name="install-dbms-instance-and-sap-application-servers"></a>Установка экземпляра СУБД и серверов приложений SAP

Завершите установку системы SAP, установив:
* экземпляр СУБД;
* основной сервер приложений SAP;
* дополнительный сервер приложений SAP.

## <a name="next-steps"></a>Дальнейшие действия

* Официальные рекомендации SAP для файлового ресурса высокой доступности: [Installation of an (A)SCS Instance on a Failover Cluster with no Shared Disks][sap-official-ha-file-share-document] (Установка экземпляра (A)SCS в отказоустойчивом кластере без использования общих дисков)

* [Локальные дисковые пространства в Windows Server 2016][s2d-in-win-2016]

* [Обзор масштабируемого файлового сервера для данных приложений][sofs-overview]

* [Новые возможности хранилища в Windows Server 2016][new-in-win-2016-storage]
