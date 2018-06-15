---
title: Подготовка инфраструктуры Azure для SAP высокого уровня доступности с помощью отказоустойчивого кластера Windows и общего диска для SAP ASCS/SCS | Документация Майкрософт
description: Узнайте, как подготовить инфраструктуру Azure для SAP высокого уровня доступности с помощью отказоустойчивого кластера Windows и общего диска для экземпляра SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 223c038155d16f41f1599aa76081560739cd7095
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657382"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Подготовка инфраструктуры Azure для SAP высокого уровня доступности с помощью отказоустойчивого кластера Windows и общего диска для SAP ASCS/SC

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Конфигурации высокой доступности SAP с несколькими SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

В этой статье описаны действия для подготовки инфраструктуры Azure к установке и настройке системы SAP высокого уровня доступности в отказоустойчивом кластере Windows, используя *общий диск кластера* для кластеризации экземпляра SAP ASCS.

## <a name="prerequisites"></a>предварительным требованиям

Перед началом установки ознакомьтесь со статьей:

* [Кластеризация экземпляра SAP (A)SCS в отказоустойчивом кластере Windows с помощью общего диска кластера в Azure][sap-high-availability-guide-wsfc-shared-disk].

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Подготовка инфраструктуры для шаблона 1 архитектуры
Шаблоны Azure Resource Manager для SAP помогают упростить развертывание необходимых ресурсов.

Эти трехуровневые шаблоны в Azure Resource Manager также поддерживают сценарии обеспечения высокого уровня доступности. Например, шаблон 1 архитектуры имеет два кластера. Каждый кластер является единой точкой отказа SAP для SAP ASCS/SCS и СУБД.

Шаблоны Azure Resource Manager, описанные в этой статье, доступны здесь:

* [Образ Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Образ Azure Marketplace с использованием управляемых дисков Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Пользовательский образ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Пользовательский образ с использованием управляемых дисков](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Чтобы подготовить инфраструктуру для использования шаблона 1 архитектуры, сделайте следующее:

- На портале Azure в области **Параметры** в поле **SYSTEMAVAILABILITY** выберите **HA**.

  ![Рис. 1. Настройка параметров Azure Resource Manager для SAP с высоким уровнем доступности][sap-ha-guide-figure-3000]

_**Рис. 1.** Настройка параметров Azure Resource Manager для SAP с высоким уровнем доступности_


  Этот шаблон создаст:

  * **Виртуальные машины**:
    * виртуальные машины сервера приложений SAP: \<SID_системы_SAP\>-di-\<Номер\>;
    * виртуальные машины кластера SCS/SCS \<SID_системы_SAP\>-ascs-\<Номер\>;
    * виртуальные машины кластера СУБД: \<SID_системы_SAP\>-db-\<Номер\>.

  * **Сетевые карты для всех виртуальных машин с соответствующими IP-адресами**:
    * \<SID_системы_SAP\>-nic-di-\<Номер\>
    * \<SID_системы_SAP\>-nic-ascs-\<Номер\>
    * \<SID_системы_SAP\>-nic-db-\<Номер\>

  * **Учетные записи хранения (только неуправляемые диски)**.

  * **Группы доступности** для:
    * виртуальных машин сервера приложений SAP: \<SID_системы_SAP\>-avset-di;
    * виртуальных машин кластера SAP ASCS/SCS: \<SID_системы_SAP\>-avset-ascs;
    * виртуальных машин кластера СУБД: \<SID_системы_SAP\>-avset-db.

  * **Внутренний балансировщик нагрузки Azure**:
    * со всеми портами для экземпляра ASCS/SCS и IP-адресом: \<SID_системы_SAP\>-lb-ascs;
    * со всеми портами для СУБД SQL Server и IP-адресом: \<SID_системы_SAP\>-lb-db.

  * **Группа безопасности сети**: \<SID_системы_SAP\>-nsg-ascs-0.  
    * С открытым внешним портом RDP для виртуальной машины: \<SID_системы_SAP\>-ascs-0.

> [!NOTE]
> Все IP-адреса сетевых карт и внутренних балансировщиков нагрузки Azure по умолчанию создаются как динамические. Их необходимо изменить на статические IP-адреса. Процедура будет описана далее.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Развертывание виртуальных машин с подключением к корпоративной сети (распределенное развертывание) для использования в рабочей среде
Для рабочих систем SAP виртуальные машины Azure развертываются с [подключением к корпоративной сети (распределенное развертывание)][planning-guide-2.2] с помощью VPN-шлюза Azure или канала Azure ExpressRoute.

> [!NOTE]
> Можно использовать имеющийся экземпляр виртуальной сети Azure. Виртуальная сеть и подсеть уже созданы и подготовлены.
>
>

1.  На портале Azure в области **Параметры** в поле **NEWOREXISTINGSUBNET** выберите **existing**.
2.  В поле **SUBNETID** введите полную строку идентификатора подсети подготовленной сети Azure, в которой планируется развертывание виртуальных машин Azure.
3.  Чтобы получить список всех подсетей в сети Azure, выполните эту команду PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  В поле **ID** отображается значение для идентификатора подсети.
4. Чтобы получить список всех значений идентификатора подсети, выполните эту команду PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Идентификатор подсети выглядит следующим образом:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Развертывание облачных экземпляров SAP для тестирования и демонстрации
Систему SAP с высоким уровнем доступности можно развернуть в модели полностью облачного развертывания. Этот тип развертывания больше подходит для демонстрации и тестирования использования. Он не поддерживается для использования в рабочей среде.

- На портале Azure в области **Параметры** в поле **NEWOREXISTINGSUBNET** выберите **new**. Оставьте поле **SUBNETID** пустым.

  Виртуальная сеть и подсеть Azure создадутся автоматически с помощью шаблона Azure Resource Manager для SAP.

> [!NOTE]
> Кроме того, необходимо развернуть по меньшей мере одну выделенную виртуальную машину для служб AD и DNS в этой же виртуальной сети Azure. Эти виртуальные машины не создаются с помощью шаблона.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Подготовка инфраструктуры для шаблона 2 архитектуры

Эти шаблоны Azure Resource Manager для SAP можно использовать для упрощения развертывания ресурсов инфраструктуры, необходимых для шаблона 2 архитектуры SAP.

Шаблоны Azure Resource Manager для сценария развертывания доступны здесь:

* [Образ Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Образ Azure Marketplace с использованием управляемых дисков](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Пользовательский образ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Пользовательский образ с использованием управляемых дисков](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Подготовка инфраструктуры для шаблона 3 архитектуры

Вы можете подготовить инфраструктуру и настроить SAP для поддержки сценария с несколькими идентификаторами безопасности. Например, вы можете добавить дополнительный экземпляр SAP ASCS/SCS в *имеющуюся* конфигурацию кластера. Дополнительные сведения см. в статье [Создание конфигурации с несколькими идентификаторами безопасности SAP NetWeaver][sap-ha-multi-sid-guide].

Если вы хотите создать кластер с несколькими идентификаторами безопасности, можно воспользоваться [шаблонами быстрого запуска в GitHub](https://github.com/Azure/azure-quickstart-templates).

Чтобы создать кластер с несколькими идентификаторами безопасности, разверните следующие три шаблона:

* [шаблон ASCS/SCS](#ASCS-SCS-template);
* [шаблон базы данных](#database-template);
* [шаблон серверов приложений](#application-servers-template).

В следующих разделах приведены более подробные сведения о шаблонах и параметрах, которые нужно указать в них.

### <a name="ASCS-SCS-template"></a> Шаблон ASCS/SCS

Шаблон ASCS/SCS развертывает две виртуальные машины, используемые для создания отказоустойчивого кластера Windows Server, содержащего несколько экземпляров ASCS/SCS.

Чтобы настроить [шаблон ASCS/SCS с несколькими ИД безопасности][sap-templates-3-tier-multisid-xscs-marketplace-image] или [шаблон ASCS/SCS с несколькими ИД безопасности с использованием управляемых дисков][sap-templates-3-tier-multisid-xscs-marketplace-image-md], введите следующие значения параметров:

  - **Resource Prefix** (Префикс ресурса). Используется в качестве префикса для всех ресурсов, созданных во время развертывания. Так как ресурсы принадлежат к нескольким системам SAP, префикс ресурса не является идентификатором безопасности одной системы SAP.  Длина префикса должна составлять от 3 до 6 символов.
  - **Stack Type** (Тип стека). Выберите тип стека системы SAP. В зависимости от типа стека в службе Azure Load Balancer предоставляется один (только ABAP или Java) или два (ABAP и Java) частных IP-адреса на систему SAP.
  -  **Тип ОС**. Выберите операционную систему виртуальных машин.
  -  **SAP System Count** (Количество систем SAP). Выберите количество систем SAP, которое требуется установить в этом кластере.
  -  **System Availability** (Доступность системы). Выберите значение **HA**.
  -  **Admin Username and Admin Password** (Имя пользователя и пароль администратора). Создайте пользователя, которого можно использовать для входа на компьютер.
  -  **New Or Existing Subnet** (Новая или имеющаяся подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите **ее**.
  -  **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Чтобы подключить виртуальную машину к локальной сети, выберите подсеть виртуальной частной сети или виртуальной сети ExpressRoute. Идентификатор обычно выглядит следующим образом:

   /subscriptions/\<идентификатор_подписки\>/resourceGroups/\<имя_группы_ресурсов\>/providers/Microsoft.Network/virtualNetworks/\<имя_виртуальной_сети\>/subnets/\<имя_подсети\>

Шаблон развертывает один экземпляр Azure Load Balancer, который поддерживает несколько систем SAP:

- экземпляры ASCS настроены для номеров экземпляров 00, 10, 20...;
- экземпляры ASCS настроены для номеров экземпляров 01, 11, 21...;
- экземпляры ASCS ERS (только Linux) настроены для номеров экземпляров 02, 12, 22...;
- экземпляры SCS ERS (только Linux) настроены для номеров экземпляров 03, 13, 23...

Балансировщик нагрузки содержит 1 виртуальный IP-адрес (2 для Linux): 1 виртуальный IP-адрес для ASCS/SCS и 1 один для ERS (только Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Порты SAP ASCS/SCS
В следующем списке содержатся все правила балансировки нагрузки (где x — номер системы SAP, например 1, 2, 3 и т. д.):
- порты, связанные с Windows, для каждой системы SAP: 445, 5985;
- порты ASCS (номер экземпляра x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016;
- порты SCS (номер экземпляра x 1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116;
- порты ASCS ERS на Linux (номер экземпляра x 2): 33x2, 5x213, 5x214, 5x216;
- порты SCS ERS на Linux (номер экземпляра x 3): 33x3, 5x313, 5x314, 5x316.

Балансировщик нагрузки настроен для использования следующих портов пробы (где x — номер системы SAP, например 1, 2, 3 и т. д.):
- порт пробы внутреннего балансировщика нагрузки ASCS/SCS: 620x0;
- порт пробы внутреннего балансировщика нагрузки ERS (только Linux): 621x2.

### <a name="database-template"></a> Шаблон базы данных

Шаблон базы данных развертывает одну или две виртуальные машины, используемые для установки системы управления реляционной базой данных (реляционной СУБД) для одной системы SAP. Например, при развертывании шаблона ASCS/SCS для 5 систем SAP его необходимо развернуть пять раз.

Чтобы настроить [шаблон базы данных с несколькими ИД безопасности][sap-templates-3-tier-multisid-db-marketplace-image] или [шаблон базы данных с несколькими ИД безопасности с использованием управляемых дисков][sap-templates-3-tier-multisid-db-marketplace-image-md], введите следующие значения параметров:

  -  **Sap System Id** (Идентификатор системы SAP). Введите идентификатор системы SAP, которую требуется установить. Идентификатор будет использоваться в качестве префикса для развертываемых ресурсов.
  -  **Тип ОС**. Выберите операционную систему виртуальных машин.
  -  **Dbtype** (Тип базы данных). Выберите тип базы данных, которую необходимо установить в кластере. Если вы хотите установить Microsoft SQL Server, выберите **SQL**. Чтобы установить SAP HANA на виртуальных машинах, выберите **HANA**. Убедитесь, что выбран правильный тип операционной системы. Выберите **Windows** для SQL, а затем выберите дистрибутив Linux для HANA. Azure Load Balancer, подключенный к виртуальным машинам, будет настроен для поддержки выбранного типа базы данных:
    * **SQL**. Балансировщик нагрузки будет выполнять балансировку нагрузки порта 1433. Используйте этот порт для настройки SQL Server AlwaysOn.
    * **HANA**. Балансировщик нагрузки будет выполнять балансировку нагрузки портов 35015 и 35017. Установите SAP HANA с номером экземпляра **50**.
    Балансировщик нагрузки использует порт пробы 62550.
  -  **Sap System Size** (Размер системы SAP). Задайте количество систем SAP, которые будет содержать новая система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям или системному интегратору SAP.
  -  **System Availability** (Доступность системы). Выберите значение **HA**.
  -  **Admin Username and Admin Password** (Имя пользователя и пароль администратора). Создайте пользователя, которого можно использовать для входа на компьютер.
  -  **Subnet Id** (Идентификатор подсети). Введите идентификатор подсети, используемый во время развертывания шаблона ASCS/SCS, или идентификатор подсети, созданный в процессе развертывания шаблона ASCS/SCS.

### <a name="application-servers-template"></a> Шаблон серверов приложений

Этот шаблон серверов приложений развертывает две или более виртуальные машины, используемые в качестве экземпляров сервера приложений SAP для одной системы SAP. Например, при развертывании шаблона ASCS/SCS для 5 систем SAP его необходимо развернуть пять раз.

Чтобы настроить [шаблон серверов приложений с несколькими ИД безопасности][sap-templates-3-tier-multisid-apps-marketplace-image] или [шаблон серверов приложений с несколькими ИД безопасности с использованием управляемых дисков][sap-templates-3-tier-multisid-apps-marketplace-image-md], введите следующие значения параметров:

  -  **Sap System Id** (Идентификатор системы SAP). Введите идентификатор системы SAP, которую требуется установить. Идентификатор будет использоваться в качестве префикса для развертываемых ресурсов.
  -  **Тип ОС**. Выберите операционную систему виртуальных машин.
  -  **Sap System Size** (Размер системы SAP). Задайте количество систем SAP, которые будет содержать новая система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям или системному интегратору SAP.
  -  **System Availability** (Доступность системы). Выберите значение **HA**.
  -  **Admin Username and Admin Password** (Имя пользователя и пароль администратора). Создайте пользователя, которого можно использовать для входа на компьютер.
  -  **Subnet Id** (Идентификатор подсети). Введите идентификатор подсети, используемый во время развертывания шаблона ASCS/SCS, или идентификатор подсети, созданный в процессе развертывания шаблона ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Виртуальная сеть Azure
В нашем примере адресное пространство экземпляра виртуальной сети Azure — 10.0.0.0/16. Существует одна подсеть Subnet с диапазоном адресов 10.0.0.0/24. Все виртуальные машины и внутренние балансировщики нагрузки развертываются в этой виртуальной сети.

> [!IMPORTANT]
> Не изменяйте параметры сети в гостевой ОС. Например, IP-адреса, DNS-серверы и подсети. Все параметры сети настраиваются с помощью Azure. Они распространяются через службу DHCP.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> IP-адреса DNS

Чтобы задать необходимые IP-адреса DNS, выполните следующие действия:

1.  На портале Azure в области **DNS-серверы** для параметра **DNS-серверы** виртуальной сети задайте значение **Пользовательская служба DNS**.
2.  Выберите параметры в зависимости от типа сети. Для получения дополнительных сведений см. следующие ресурсы:
    * [Подключения к корпоративной сети (распределенного развертывания):][planning-guide-2.2] добавьте IP-адреса локальных DNS-серверов.  
    Локальные DNS-серверы могут быть расширены до виртуальных машин в Azure. В этом случае можно добавить IP-адреса этих виртуальных машин Azure, на которых запущена служба DNS.
    * [Полностью облачное развертывание:][planning-guide-2.1] разверните дополнительную виртуальную машину в том же экземпляре виртуальной сети, который будет выполнять функции DNS-серверов. Добавьте IP-адреса этих виртуальных машин Azure, настроенных для запуска службы DNS.

    ![Рис. 2. Настройка DNS-серверов для виртуальной сети Azure][sap-ha-guide-figure-3001]

    _**Рис. 2.** Настройка DNS-серверов для виртуальной сети Azure_

  > [!NOTE]
  > В случае изменения IP-адресов DNS-серверов необходимо перезагрузить виртуальные машины Azure, чтобы применить изменения и распространить новые адреса DNS-серверов.
  >
  >

В нашем примере служба DNS установлена и настроена на следующих виртуальных машинах Windows.

| Роль виртуальной машины | Имя узла виртуальной машины | Имя сетевой карты | Статический IP-адрес |
| --- | --- | --- | --- |
| 1-й DNS-сервер |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 2-й DNS-сервер |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Имена узлов и статические IP-адреса для кластеризованного экземпляра SAP ASCS/SCS и кластеризованного экземпляра СУБД

Для локального развертывания необходимы следующие зарезервированные имена узлов и IP-адреса.

| Роль имени виртуального узла | Имя виртуального узла | Виртуальный статический IP-адрес |
| --- | --- | --- |
| Имя 1-го виртуального узла кластера SAP ASCS/SCS (используется для управления кластером) |pr1-ascs-vir |10.0.0.42 |
| Имя виртуального узла экземпляра SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Имя 2-го виртуального узла кластера SAP ASCS/SCS (используется для управления кластером) |pr1-dbms-vir |10.0.0.32 |

Имена виртуальных узлов pr1-ascs-vir и pr1-dbms-vir и связанные IP-адреса, используемые для управления кластером, создаются вместе с кластером. Дополнительные сведения о том, как это сделать, см. в разделе [Сбор узлов кластера в конфигурации кластера][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Два других имени виртуальных узлов — pr1-ascs-sap и pr1-dbms-sap — и связанные IP-адреса можно вручную создать на DNS-сервере. Эти ресурсы используются кластеризованным экземпляром SAP ASCS/SCS и кластеризованным экземпляром СУБД. Дополнительные сведения о том, как это сделать, см. в разделе [Создание имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Настройка статических IP-адресов для виртуальных машин SAP
После развертывания виртуальных машин для использования в кластере необходимо настроить статические IP-адреса для всех виртуальных машин. Это невозможно сделать в операционной системе на виртуальной машине. Задача выполняется в конфигурации виртуальной сети Azure.

1.  На портале Azure выберите **Группа ресурсов** > **Сетевая карта** > **Параметры** > **IP-адрес**.
2.  В области **IP-адреса** в разделе **Назначение** выберите **Статический**. В поле **IP-адрес** введите нужный IP-адрес.

  > [!NOTE]
  > В случае изменения IP-адреса сетевой карты необходимо перезапустить виртуальные машины Azure, чтобы применить изменение.  
  >
  >

  ![Рис. 3. Настройка статического IP-адреса для сетевой карты каждой виртуальной машины][sap-ha-guide-figure-3002]

  _**Рис. 3.** Настройка статического IP-адреса для сетевой карты каждой виртуальной машины_

  Повторите этот шаг для всех сетевых интерфейсов, т. е. для всех виртуальных машин, включая те, которые вы хотите использовать для службы Active Directory или DNS.

В нашем примере используются следующие виртуальные машины и статические IP-адреса.

| Роль виртуальной машины | Имя узла виртуальной машины | Имя сетевой карты | Статический IP-адрес |
| --- | --- | --- | --- |
| Первый экземпляр сервера приложений SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Второй экземпляр сервера приложений SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Последний экземпляр сервера приложений SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| 1-й узел кластера для экземпляра ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| 2-й узел кластера для экземпляра ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| 1-й узел кластера для экземпляра СУБД |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| 2-й узел кластера для экземпляра СУБД |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Настройка статического IP-адреса для внутреннего балансировщика нагрузки Azure

Шаблон Azure Resource Manager для SAP создает внутренний балансировщик нагрузки Azure, используемый для кластера экземпляров SAP ASCS/SCS и кластера СУБД.

> [!IMPORTANT]
> IP-адрес имени виртуального узла для SAP ASCS/SCS совпадает с IP-адресом внутреннего балансировщика нагрузки SAP ASCS/SCS: pr1-lb-ascs.
> IP-адрес виртуального имени СУБД совпадает с IP-адресом внутреннего балансировщика нагрузки СУБД: pr1-lb-dbms.
>
>

Чтобы настроить статические IP-адреса для внутреннего балансировщика нагрузки Azure, выполните следующие действия:

1.  При первоначальном развертывании для внутреннего балансировщика нагрузки задается **динамический** IP-адрес. На портале Azure в области **IP-адреса** в разделе **Назначение** выберите **Статический**.
2.  Задайте для внутреннего балансировщика нагрузки **pr1-lb-ascs** IP-адрес имени виртуального узла экземпляра SAP ASCS/SCS.
3.  Задайте для внутреннего балансировщика нагрузки **pr1-lb-dbms** IP-адрес имени виртуального узла экземпляра СУБД.

  ![Рис. 4. Настройка статических IP-адресов внутреннего балансировщика нагрузки для экземпляра SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Рис. 4.** Настройка статических IP-адресов внутреннего балансировщика нагрузки для экземпляра SAP ASCS/SCS_

В нашем примере есть два внутренних балансировщика нагрузки Azure со следующими статическими IP-адресами.

| Роль внутреннего балансировщика нагрузки Azure | Имя внутреннего балансировщика нагрузки Azure | Статический IP-адрес |
| --- | --- | --- |
| Внутренний балансировщик нагрузки экземпляра SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Внутренний балансировщик нагрузки экземпляра СУБД SAP |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure

По умолчанию шаблон Azure Resource Manager для SAP создает все необходимые порты для следующих экземпляров:
* экземпляра ABAP ASCS с номером экземпляра по умолчанию 00;
* экземпляра Java SCS с номером экземпляра по умолчанию 01.

При установке экземпляра SAP ASCS/SCS необходимо использовать номер экземпляра по умолчанию 00 для экземпляра ABAP ASCS и номер экземпляра по умолчанию 01 для экземпляра Java SCS.

Затем создайте необходимые конечные точки внутренней балансировки нагрузки для портов SAP NetWeaver.

Чтобы создать необходимые конечные точки внутренней балансировки нагрузки, сначала их необходимо создать для портов SAP NetWeaver ABAP ASCS.

| Служба, имя правила балансировки нагрузки | Номера портов по умолчанию | Определенные порты для (экземпляра ASCS с номером экземпляра 00) (ERS с номером 10) |
| --- | --- | --- |
| Сервер постановки в очередь, *lbrule3200* |32\<номер_экземпляра\> |3200 |
| Сервер сообщений ABAP, *lbrule3600* |36\<номер_экземпляра\> |3600 |
| Внутренняя служба сообщений ABAP, *lbrule3900* |39\<номер_экземпляра\> |3900 |
| Сервер сообщений (HTTP), *Lbrule8100* |81\<номер_экземпляра\> |8100 |
| Служба запуска SAP ASCS (HTTP), *Lbrule50013* |5\<номер_экземпляра\>13 |50013 |
| Служба запуска SAP ASCS (HTTPS), *Lbrule50014* |5\<номер_экземпляра\>14 |50014 |
| Служба постановки в очередь для репликации, *Lbrule50016* |5\<номер_экземпляра\>16 |50016 |
| Служба запуска SAP ERS (HTTP), *Lbrule51013* |5\<номер_экземпляра\>13 |51013 |
| Служба запуска SAP ERS (HTTPS), *Lbrule51014* |5\<номер_экземпляра\>14 |51014 |
| Служба удаленного управления Windows (WinRM) *Lbrule5985* | |5985 |
| Общий доступ к файлам, *Lbrule445* | |445 |

**Таблица 1.** Номера портов экземпляров SAP NetWeaver ABAP ASCS

Затем создайте эти конечные точки балансировки нагрузки для портов SAP NetWeaver Java SCS.

| Служба, имя правила балансировки нагрузки | Номера портов по умолчанию | Определенные порты для (экземпляра SCS с номером экземпляра 01) (ERS с номером 11) |
| --- | --- | --- |
| Сервер постановки в очередь, *lbrule3201* |32\<номер_экземпляра\> |3201 |
| Сервер шлюза, *lbrule3301* |33\<номер_экземпляра\> |3301 |
| Сервер сообщений Java, *lbrule3900* |39\<номер_экземпляра\> |3901 |
| Сервер сообщений (HTTP), *Lbrule8101* |81\<номер_экземпляра\> |8101 |
| Служба запуска SAP SCS (HTTP), *Lbrule50113* |5\<номер_экземпляра\>13 |50113 |
| Служба запуска SAP SCS (HTTPS), *Lbrule50114* |5\<номер_экземпляра\>14 |50114 |
| Служба постановки в очередь для репликации, *Lbrule50116* |5\<номер_экземпляра\>16 |50116 |
| Служба запуска SAP ERS (HTTP), *Lbrule51113* |5\<номер_экземпляра\>13 |51113 |
| Служба запуска SAP ERS (HTTPS), *Lbrule51114* |5\<номер_экземпляра\>14 |51114 |
| WinRM, *Lbrule5985* | |5985 |
| Общий доступ к файлам, *Lbrule445* | |445 |

**Таблица 2.** Номера портов экземпляров SAP NetWeaver Java SCS

![Рис. 5. Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure][sap-ha-guide-figure-3004]

_**Рис. 5.** Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure_

Задайте для балансировщика нагрузки pr1-lb-dbms IP-адрес имени виртуального узла экземпляра СУБД.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure

Если вы хотите использовать другие номера для экземпляров SAP ASCS или SCS, необходимо изменить имена и значения по умолчанию их портов.

1.  На портале Azure выберите **\<SID\> балансировщик нагрузки -lb-ascs** > **Правила балансировки нагрузки**.
2.  Для всех правил балансировки нагрузки, относящихся к экземпляру SAP ASCS или SCS, измените следующие значения.

  * ИМЯ
  * Порт
  * Внутренний порт

  Например, если требуется изменить номер экземпляра ASCS по умолчанию с 00 на 31, то необходимо внести изменения для всех портов, указанных в таблице 1.

  Ниже приведен пример обновления *lbrule3200*для порта.

  ![Рис. 6. Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure][sap-ha-guide-figure-3005]

  _**Рис. 6.** Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Присоединение виртуальных машин Windows к домену

После назначения виртуальным машинам статических IP-адресов добавьте эти машины в домен.

![Рис. 7. Добавление виртуальной машины в домен][sap-ha-guide-figure-3006]

_**Рис. 7.** Добавление виртуальной машины в домен_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Добавление записей реестра для обоих узлов кластера экземпляра SAP ASCS/SCS

Azure Load Balancer имеет внутренний балансировщик нагрузки, который закрывает подключения, если они простаивают определенный период времени (время простоя). Рабочие процессы SAP в диалоговых экземплярах открывают подключения к процессу постановки в очередь SAP при первой же необходимости отправить первый запрос на постановку в очередь или вывод из очереди. Эти подключения обычно сохраняются до перезапуска рабочего процесса или процесса постановки в очередь. Однако если подключение бездействует в течение некоторого времени, внутренний балансировщик нагрузки Azure закроет его. Это не является проблемой, так как рабочие процессы SAP повторно установят подключение к процессу постановки в очередь, если обнаружат его отсутствие. Эти действия будут описаны в трассировках разработчика для процессов SAP, но это приведет к созданию большого объема содержимого в этих трассировках. Поэтому рекомендуется изменить параметры TCP/IP `KeepAliveTime` и `KeepAliveInterval` на обоих узлах кластера. Необходимо также изменить параметры профиля SAP, которые будут описаны далее в этом документе.

Чтобы добавить записи реестра на обоих узлах кластера экземпляра SAP ASCS/SCS, сначала добавьте эти записи реестра Windows на обоих узлах кластера Windows для SAP ASCS/SCS.

| Путь | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Имя переменной |`KeepAliveTime` |
| Тип переменной |REG_DWORD (десятичное) |
| Значение |120000 |
| Ссылка на документацию |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Таблица 3.** Изменение первого параметра TCP/IP

Затем добавьте следующую запись реестра Windows на обоих узлах кластера Windows для SAP ASCS/SCS:

| Путь | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Имя переменной |`KeepAliveInterval` |
| Тип переменной |REG_DWORD (десятичное) |
| Значение |120000 |
| Ссылка на документацию |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Таблица 4.** Изменение второго параметра TCP/IP

Чтобы применить изменения, перезапустите оба узла кластера.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Установка отказоустойчивого кластера Windows Server для экземпляра SAP ASCS/SCS

Настройка отказоустойчивого кластера Windows Server для экземпляра SAP ASCS/SCS состоит из следующих заданий:

- сбор узлов кластера в конфигурации кластера;
- настройка файлового ресурса-свидетеля кластера.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Сбор узлов кластера в конфигурации кластера

1.  В мастере добавления ролей и компонентов добавьте компонент отказоустойчивой кластеризации на оба узла кластера.
2.  Настройте отказоустойчивый кластер с помощью диспетчера отказоустойчивости кластеров. В диспетчере отказоустойчивости кластеров щелкните **Создать кластер** и добавьте только имя первого кластера (узла A). Не добавляйте пока второй узел. Вы сделаете это позже.

  ![Рис. 8. Добавление имени сервера или виртуальной машины для первого узла кластера][sap-ha-guide-figure-3007]

  _**Рис. 8.** Добавление имени сервера или виртуальной машины для первого узла кластера_

3.  Введите сетевое имя кластера (имя виртуального узла).

  ![Рис. 9. Определение имени кластера][sap-ha-guide-figure-3008]

  _**Рис. 9.** Определение имени кластера_

4.  После создания кластера запустите проверочный тест.

  ![Рис. 10. Проверка кластера][sap-ha-guide-figure-3009]

  _**Рис. 10.** Проверка кластера_

  На этом этапе можно игнорировать любые предупреждения о дисках. Файловый ресурс-свидетель и общие диски SIOS будут добавлены позже. На этом этапе кворум нам не важен.

  ![Рис. 11. Предупреждение об отсутствии диска кворума][sap-ha-guide-figure-3010]

  _**Рис. 11.** Предупреждение об отсутствии диска кворума_

  ![Рис. 12. Определение основного ресурса кластера с IP-адресом (требуется новый IP-адрес)][sap-ha-guide-figure-3011]

  _**Рис. 12.** Определение основного ресурса кластера с IP-адресом (требуется новый IP-адрес)_

5.  Измените IP-адрес основной службы кластера. Кластер не может запуститься, пока не будет изменен IP-адрес основной службы кластера. Это связано с тем, что IP-адрес сервера указывает на один из узлов виртуальных машин. Его можно изменить на странице **Свойства** ресурса IP-адреса основной службы кластера.

  Например, нужно назначить IP-адрес (в нашем примере — 10.0.0.42) для имени виртуального узла кластера pr1-ascs-vir.

  ![Рис. 13. Использование окна "Свойства" для изменения IP-адреса][sap-ha-guide-figure-3012]

  _**Рис. 13.** Использование окна **Свойства** для изменения IP-адреса_

  ![Рис. 14. Назначение IP-адреса, зарезервированного для кластера][sap-ha-guide-figure-3013]

  _**Рис. 14.** Назначение IP-адреса, зарезервированного для кластера_

6.  Подключите имя виртуального узла кластера к сети.

  ![Рис. 15. Основная служба кластера запущена и работает с правильным IP-адресом][sap-ha-guide-figure-3014]

  _**Рис. 15.** Основная служба кластера запущена и работает с правильным IP-адресом_

7.  Добавьте второй узел кластера.

  Теперь, когда основная служба кластера запущена и работает, можно добавить второй узел кластера.

  ![Рис. 16. Добавление второго узла кластера][sap-ha-guide-figure-3015]

  _**Рис. 16.** Добавление второго узла кластера_

8.  Введите имя второго узла кластера.

  ![Рис. 17. Определение имени второго узла кластера][sap-ha-guide-figure-3016]

  _**Рис. 17.** Определение имени второго узла кластера_

  > [!IMPORTANT]
  > Убедитесь, что флажок **Добавление всех допустимых хранилищ в кластер** для кластера *не* установлен.  
  >
  >

  ![Рис. 18. Не следует устанавливать указанный флажок!][sap-ha-guide-figure-3017]

  _**Рис. 18.** *Не* следует устанавливать указанный флажок!_

  Предупреждения о кворуме и дисках можно игнорировать. Настройка диска кворума и общих дисков будет выполнена позже, как описано в разделе [Установка SIOS DataKeeper Cluster Edition для создания общего диска кластера SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![Рис. 19. Предупреждение о диске кворума можно игнорировать][sap-ha-guide-figure-3018]

  _**Рис. 19.** Предупреждение о диске кворума можно игнорировать_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Настройка файлового ресурса-свидетеля кластера

Настройка файлового ресурса-свидетеля кластера состоит из следующих заданий:

- создание файлового ресурса;
- настройка кворума на основе файлового ресурса-свидетеля в диспетчере отказоустойчивости кластеров.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Создание файлового ресурса

1.  Вместо диска кворума нужно выбрать файловый ресурс-свидетель. SIOS DataKeeper поддерживает такой вариант.

  В примерах в этой статье файловый ресурс-свидетель настроен на сервере Active Directory или DNS-сервере, работающем в Azure. Этот файловый ресурс-свидетель называется domcontr 0. Так как вам пришлось бы настроить VPN-подключение к Azure (VPN-шлюз или канал ExpressRoute), то служба Active Directory или DNS находилась бы в локальной среде и поэтому не подошла бы для работы файлового ресурса-свидетеля.

  > [!NOTE]
  > Если служба Active Directory или DNS работает только в локальной среде, не следует настраивать файловый ресурс-свидетель в операционной системе Windows для службы Active Directory или DNS, запущенной локально. Задержка сети между узлами кластера, работающими в Azure, и локальной средой службы Active Directory или DNS может быть слишком большой и вызвать проблемы с подключением. Файловый ресурс-свидетель следует настроить на виртуальной машине Azure, размещенной близко от узла кластера.  
  >
  >

  На диске кворума должно не менее 1024 МБ свободного места. Мы рекомендуем выделить 2048 МБ свободного места для диска кворума.

2.  Добавьте объект имени кластера.

  ![Рис. 20. Назначение разрешений для общего ресурса для объекта имени кластера][sap-ha-guide-figure-3019]

  _**Рис. 20.** Назначение разрешений для общего ресурса для объекта имени кластера_

  Убедитесь, что разрешения позволяют изменять данные в общем ресурсе для объекта имени кластера (в нашем примере это pr1-ascs-vir$).

3.  Чтобы добавить объект имени кластера в список, нажмите кнопку **Добавить**. Измените фильтр для добавления дополнительных объектов-компьютеров к показанным на рис. 22.

  ![Рис. 21. Изменение типов объектов для добавления компьютеров][sap-ha-guide-figure-3020]

  _**Рис. 21.** Изменение **типов объектов** для добавления компьютеров_

  ![Рис 22. Установка флажка "Компьютеры"][sap-ha-guide-figure-3021]

  _**Рис 22.** Установка флажка **Компьютеры**_

4.  Введите объект имени кластера, как показано на рис. 21. Так как запись уже создана, можно изменить разрешения, как показано на рис. 20.

5.  Откройте вкладку **Безопасность** общего ресурса и определите более детальные разрешения для объекта имени кластера.

  ![Рис. 23. Настройка атрибутов безопасности объекта имени кластера для кворума на основе файлового ресурса][sap-ha-guide-figure-3022]

  _**Рис. 23.** Настройка атрибутов безопасности объекта имени кластера для кворума на основе файлового ресурса_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Настройка кворума на основе файлового ресурса-свидетеля в диспетчере отказоустойчивости кластеров

1.  Откройте мастер настройки параметров кворума.

  ![Рис. 24. Запуск мастера настройки кворума кластера][sap-ha-guide-figure-3023]

  _**Рис. 24.** Запуск мастера настройки кворума кластера_

2.  На странице **Выбор параметра конфигурации кворума** установите флажок **Выбрать свидетель кворума**.

  ![Рис. 25. Экран выбора различных конфигураций кворума][sap-ha-guide-figure-3024]

  _**Рис. 25.** Экран выбора различных конфигураций кворума_

3.  На странице **Выбрать свидетель кворума** установите флажок **Настроить файловый ресурс-свидетель**.

  ![Рис. 26. Выбор файлового ресурса-свидетеля][sap-ha-guide-figure-3025]

  _**Рис. 26.** Выбор файлового ресурса-свидетеля_

4.  Введите путь UNC к файловому ресурсу (в нашем примере это \\domcontr-0\FSW). Чтобы просмотреть список изменений, которые можно внести, нажмите кнопку **Далее**.

  ![Рис. 27. Определение расположения файлового ресурса-свидетеля][sap-ha-guide-figure-3026]

  _**Рис. 27.** Определение расположения файлового ресурса-свидетеля_

5.  Выберите нужные изменения и нажмите кнопку **Далее**. Конфигурацию кластера необходимо перенастроить, как показано на рис. 28:  

  ![Рис. 28. Подтверждение перенастройки кластера][sap-ha-guide-figure-3027]

  _**Рис. 28.** Подтверждение перенастройки кластера_

После успешной установки отказоустойчивого кластера Windows необходимо внести изменения в некоторые пороговые значения, чтобы адаптировать обнаружение отработки отказа к условиям в Azure. Параметры, которые необходимо изменить, описаны в записи блога [Tuning Failover Cluster Network Thresholds][tuning-failover-cluster-network-thresholds] (Настройка пороговых значений сети отказоустойчивого кластера). Предположим, что две виртуальные машины, образующие конфигурацию кластера Windows для ASCS/SCS, находятся в одной подсети. Тогда нужно изменить значения приведенных ниже параметров следующими значениями:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Эти параметры были протестированы у клиентов, они обеспечивают удачный компромисс. Они являются достаточно устойчивыми, а также обеспечивают довольно быструю отработку отказа в реальных условиях сбоя программного обеспечения SAP либо узла или виртуальной машины.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Установка SIOS DataKeeper Cluster Edition для создания общего диска кластера SAP ASCS/SCS

Теперь у вас есть рабочая конфигурация отказоустойчивого кластера Windows Server в Azure. Чтобы установить экземпляр SAP ASCS/SCS, требуется общий дисковый ресурс. Azure не позволяет создавать общие дисковые ресурсы. Поэтому для выполнения этой задачи придется воспользоваться SIOS DataKeeper Cluster Edition — сторонним решением для создания общих дисковых ресурсов.

Установка SIOS DataKeeper Cluster Edition для общего диска кластера SAP ASCS/SCS состоит из следующих заданий:

- добавление Microsoft .NET Framework 3.5;
- установка SIOS DataKeeper;
- настройка SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Добавление .NET Framework 3.5
Компонент .NET Framework 3.5 не установлен и не включен автоматически в Windows Server 2012 R2. Для работы SIOS DataKeeper требуется установить .NET на всех узлах, где устанавливается этот продукт. Поэтому необходимо установить компонент .NET Framework 3.5 в операционной системе на всех виртуальных машинах в кластере.

Существует два способа добавления .NET Framework 3.5:

- Первый способ — с помощью мастера добавления ролей и компонентов в Windows, как показано на рис. 29:

  ![Рис. 29. Установка .NET Framework 3.5 с помощью мастера добавления ролей и компонентов][sap-ha-guide-figure-3028]

  _**Рис. 29.** Установка .NET Framework 3.5 с помощью мастера добавления ролей и компонентов_

  ![Рис. 30. Ход установки .NET Framework 3.5 с помощью мастера добавления ролей и компонентов][sap-ha-guide-figure-3029]

  _**Рис. 30.** Ход установки .NET Framework 3.5 с помощью мастера добавления ролей и компонентов_

- Второй способ — с помощью средства командной строки dism.exe. Для этого необходим доступ к каталогу SxS на установочном носителе Windows. В командной строке с повышенными привилегиями выполните следующую команду:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Установка SIOS DataKeeper

SIOS DataKeeper Cluster Edition нужно установить на обоих узлах в кластере. Чтобы создать виртуальное общее хранилище с помощью SIOS DataKeeper, создайте синхронизированное зеркало и сымитируйте общее хранилище кластера.

Перед установкой программного обеспечения SIOS необходимо создать пользователя домена DataKeeperSvc.

> [!NOTE]
> Добавьте пользователя домена DataKeeperSvc в группу локальных администраторов на обоих узлах кластера.
>
>

Чтобы установить SIOS DataKeeper, сделайте следующее:

1.  Установите программное обеспечение SIOS на обоих узлах кластера.

  ![Установщик SIOS][sap-ha-guide-figure-3030]

  ![Рис. 31. Первая страница установки SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Рис. 31.** Первая страница установки SIOS DataKeeper_

2.  В диалоговом окне выберите **Да**.

  ![Рис. 32. DataKeeper информирует об отключении службы][sap-ha-guide-figure-3032]

  _**Рис. 32.** DataKeeper информирует об отключении службы_

3.  В диалоговом окне мы рекомендуем установить флажок **Domain or Server account** (Учетная запись домена или сервера).

  ![Рис. 33. Выбор пользователя для SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Рис. 33.** Выбор пользователя для SIOS DataKeeper_

4.  Укажите имя учетной записи пользователя домена и пароль, созданные для SIOS DataKeeper.

  ![Рис. 34. Ввод имени пользователя и пароля для установки SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Рис. 34.** Ввод имени пользователя и пароля для установки SIOS DataKeeper_

5.  Установите лицензионный ключ для своего экземпляра SIOS DataKeeper, как показано на рис. 35.

  ![Рис. 35. Указание ключа лицензии на использование SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Рис. 35.** Указание ключа лицензии на использование SIOS DataKeeper_

6.  При появлении запроса перезапустите виртуальную машину.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Настройка SIOS DataKeeper

После установки SIOS DataKeeper на обоих узлах начните настройку параметров. Наша цель — настроить синхронную репликацию данных между дополнительными дисками, подключенными к каждой виртуальной машине.

1.  Запустите инструмент управления и настройки DataKeeper, а затем щелкните **Подключение к серверу**.

  ![Рис. 36. Инструмент управления и настройки DataKeeper][sap-ha-guide-figure-3036]

  _**Рис. 36.** Инструмент управления и настройки DataKeeper_

2.  Введите имя или TCP/IP-адрес первого узла, к которому должен подключиться инструмент управления и настройки, а на втором шаге — для второго узла.

  ![Рис. 37. Вставка имени или TCP/IP-адреса первого узла, к которому должен подключиться инструмент управления. На втором шаге — вставка данных для второго узла.][sap-ha-guide-figure-3037]

  _**Рис. 37.** Вставка имени или TCP/IP-адреса первого узла, к которому должен подключиться инструмент управления. На втором шаге — вставка данных для второго узла._

3.  Создайте задание репликации между двумя узлами.

  ![Рис. 38. Создание задания репликации][sap-ha-guide-figure-3038]

  _**Рис. 38.** Создание задания репликации_

  Мастер поможет выполнить эту процедуру.

4.  Определите имя задания репликации.

  ![Рис. 39. Определение имени задания репликации][sap-ha-guide-figure-3039]

  _**Рис. 39.** Определение имени задания репликации_

  ![Рис. 40. Определение базовых данных для узла, который должен быть текущим исходным узлом][sap-ha-guide-figure-3040]

  _**Рис. 40.** Определение базовых данных для узла, который должен быть текущим исходным узлом_

5.  Определите имя, TCP/IP-адрес и дисковый том целевого узла.

  ![Рис. 41. Определение имени, TCP/IP-адреса и дискового тома текущего целевого узла][sap-ha-guide-figure-3041]

  _**Рис. 41.** Определение имени, TCP/IP-адреса и дискового тома текущего целевого узла_

6.  Определите алгоритм сжатия. Для наших целей рекомендуется сжатие потока репликации. Сжатие потока репликации значительно сокращает время повторной синхронизации (особенно в случаях повторной синхронизации). Сжатие использует ресурсы ЦП и памяти виртуальной машины. Чем выше степень сжатия, тем больше том используемых ресурсов ЦП. Этот параметр можно будет откорректировать и изменить позже.

7.  Кроме того, нужно проверить параметр, который позволяет включить синхронный или асинхронный режим репликации. Для защиты конфигураций SAP ASCS/SCS требуется синхронная репликация.  

  ![Рис. 42. Определение сведений о репликации][sap-ha-guide-figure-3042]

  _**Рис. 42.** Определение сведений о репликации_

8.  Определите, следует ли том, реплицируемый с помощью задания репликации, представить в конфигурации кластера WSFC как общий диск. Для конфигурации SAP ASCS/SCS выберите **Да**, чтобы кластер Windows воспринимал реплицируемый том как общий диск, который можно использовать в качестве тома кластера.

  ![Рис. 43. Чтобы задать реплицируемый том как том кластера, следует нажать кнопку "Да"][sap-ha-guide-figure-3043]

  _**Рис. 43.** Чтобы задать реплицируемый том как том кластера, следует нажать кнопку **Да**_

  После создания тома инструмент управления и настройки DataKeeper отобразят задание репликации как активное.

  ![Рис. 44. Синхронное зеркальное отображение DataKeeper для общего диска SAP ASCS/SCS активно][sap-ha-guide-figure-3044]

  _**Рис. 44.** Синхронное зеркальное отображение DataKeeper для общего диска SAP ASCS/SCS активно_

  Теперь диск отображается в диспетчере отказоустойчивости кластеров как диск DataKeeper, как показано на рис. 45:

  ![Рис. 45. Реплицируемый диск DataKeeper отображается в диспетчере отказоустойчивости кластеров][sap-ha-guide-figure-3045]

  _**Рис. 45.** Реплицируемый диск DataKeeper отображается в диспетчере отказоустойчивости кластеров_

## <a name="next-steps"></a>Дополнительная информация

* [Установка SAP NetWeaver высокого уровня доступности в Azure с использованием отказоустойчивого кластера Windows и общего диска для экземпляра SAP (A)SCS][sap-high-availability-installation-wsfc-shared-disk]
