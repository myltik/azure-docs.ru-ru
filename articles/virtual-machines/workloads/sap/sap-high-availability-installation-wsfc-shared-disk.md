---
title: Установка SAP NetWeaver высокого уровня доступности в отказоустойчивом кластере Windows на общем диске для экземпляра SAP ASCS/SCS в Azure | Документация Майкрософт
description: Узнайте, как установить SAP NetWeaver высокого уровня доступности в отказоустойчивом кластере Windows на общем диске для экземпляра SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 419bbdd57a391dbbf01c2110a1609cb3d0ded003
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Установка SAP NetWeaver высокого уровня доступности в отказоустойчивом кластере Windows на общем диске для экземпляра SAP ASCS/SCS в Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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

В этой статье описывается, как установить и настроить в Azure систему SAP высокого уровня доступности, используя отказоустойчивый кластер Windows Server и общий диск для кластеризации экземпляра SAP ASCS/SCS.

## <a name="prerequisites"></a>предварительным требованиям

Перед началом установки изучите следующие документы:

* [Кластеризация экземпляра SAP (A)SCS в отказоустойчивом кластере Windows с помощью общего диска кластера в Azure][sap-high-availability-guide-wsfc-shared-disk].

* [Подготовка инфраструктуры Azure для SAP высокого уровня доступности с использованием отказоустойчивого кластера Windows и общего диска для экземпляра SAP (A)SCS][sap-high-availability-infrastructure-wsfc-shared-disk].

В этой статье мы не рассматриваем настройку СУБД, так как она зависит от используемой СУБД. Мы полагаем, что для СУБД высокий уровень доступности обеспечивается благодаря тем возможностям, которые поставщики СУБД поддерживают для Azure. Например, это AlwaysOn или зеркальное отображение базы данных для SQL Server и Oracle Data Guard для баз данных Oracle. В нашем примере для СУБД не применяются дополнительные меры защиты.

Не существует особых требований, которые нужно учитывать при взаимодействии любых СУБД с кластеризованной конфигурацией SAP ASCS/SCS в Azure.

> [!NOTE]
> Процедуры установки систем SAP NetWeaver ABAP, SAP Java и SAP ABAP с Java практически идентичны. Главное отличие состоит в том, что в системе SAP ABAP всего один экземпляр ASCS. В системе SAP Java имеется один экземпляр SCS. В системе SAP ABAP с Java — один экземпляр ASCS и один экземпляр SCS, которые работают в одной группе отказоустойчивого кластера Майкрософт. Любые отличия в установке для каждого стека установки SAP NetWeaver будут указаны явным образом. Все остальные составляющие считаются одинаковыми.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Установите SAP с экземпляром ASCS/SCS высокого уровня доступности.

> [!IMPORTANT]
> Не размещайте файл подкачки на зеркальных томах SIOS DataKeeper. DataKeeper не поддерживает зеркальные тома. Можно оставить файл подкачки на временном диске D виртуальной машины Azure, который задан по умолчанию. Если файла там нет, поместите его на диск D виртуальной машины Azure.
>
>

Установка SAP с высокодоступным экземпляром ASCS/SCS состоит из следующих заданий:

* Создание имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS.
* Установка первого узла кластера SAP.
* Изменение профиля SAP для экземпляра ASCS/SCS.
* Добавление порта пробы.
* Открытие порта пробы в брандмауэре Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Создание имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS

1.  В диспетчере DNS Windows создайте запись DNS для имени виртуального узла экземпляра ASCS/SCS.

  > [!IMPORTANT]
  > IP-адрес, который вы назначите имени виртуального узла для экземпляра ASCS/SCS, должен совпадать с IP-адресом, назначенным для Azure Load Balancer (\<SID\>-lb-ascs).  
  >
  >

  IP-адрес имени виртуального узла SAP ASCS/SCS (pr1 ascs sap) совпадает с IP-адресом Azure Load Balancer (pr1-lb-ascs).

  ![Рис. 1. Определение записи DNS для виртуального имени и TCP/IP-адреса кластера SAP ASCS/SCS][sap-ha-guide-figure-3046]

  _**Рис. 1.** Определение записи DNS для виртуального имени и TCP/IP-адреса кластера SAP ASCS/SCS_

2.  Чтобы определить IP-адрес, назначенный имени виртуального узла, выберите **Диспетчер DNS** > **Домен**.

  ![Рис. 2. Новое виртуальное имя и TCP/IP-адрес для конфигурации кластера SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Рис. 2.** Новое виртуальное имя и TCP/IP-адрес для конфигурации кластера SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Установка первого узла кластера SAP

1.  Выполните действие создания первого узла кластера на узле А. В нашем примере это узел pr1-ascs-0\*.
2.  Чтобы сохранить порты по умолчанию для внутреннего балансировщика нагрузки Azure, выберите:

  * для **системы ABAP** — экземпляр **ASCS** с номером **00**;
  * для **системы Java** — экземпляр **SCS** с номером **01**;
  * для **системы ABAP с Java** — экземпляр **ASCS** с номером **00** и экземпляр **SCS** с номером **01**.

  Чтобы использовать номера экземпляра, отличные от стандартных (00 для экземпляра ABAP ASCS и 01 для экземпляра Java SCS), сначала следует изменить правила по умолчанию на внутренней подсистеме балансировки нагрузки Azure. Дополнительные сведения см. в разделе [Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure][sap-ha-guide-8.9].

Следующие несколько заданий не описаны в обычной документации по установке SAP.

> [!NOTE]
> В документации по установке SAP содержатся сведения по установке первого узла кластера ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Изменение профиля SAP экземпляра ASCS/SCS

Прежде всего, добавьте новый параметр профиля. Этот параметр профиля запрещает закрытие подключений между рабочими процессами SAP и сервером постановки в очередь, если они простаивают слишком долго. Мы уже упоминали эту проблему в разделе [Добавление записей реестра для обоих узлов кластера экземпляра SAP ASCS/SCS][sap-ha-guide-8.11]. Кроме того, в том разделе мы предложили два изменения в основных параметрах подключения TCP/IP. На втором шаге необходимо настроить сервер постановки в очередь для отправки сигнала `keep_alive`, чтобы простой подключений не превышал пороговое значение, заданное во внутреннем балансировщике нагрузки Azure.

Чтобы изменить профиль SAP экземпляра ASCS/SCS, сделайте следующее:

1.  Добавьте этот параметр профиля в профиль экземпляра SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  В нашем примере путь следующий:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Например, ниже приведен профиль экземпляра SAP SCS и соответствующий путь.

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Чтобы применить эти изменения, перезапустите экземпляр SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Добавление порта пробы

Чтобы вся конфигурация кластера работала с Azure Load Balancer, нужно использовать функцию проб внутреннего балансировщика нагрузки. Обычно внутренний балансировщик нагрузки Azure распределяет входящую рабочую нагрузку поровну между участвующими виртуальными машинами.

 Однако это не будет работать в некоторых конфигурациях кластера, так как активен только один экземпляр. Другой экземпляр пассивен и не может принимать рабочую нагрузку. Функция проб полезна в случаях, когда внутренний балансировщик нагрузки Azure назначает работу только активным экземплярам. С помощью этой функции внутренний балансировщик нагрузки может определить, какой из экземпляров активен, и затем направлять рабочую нагрузку только на него.

Чтобы добавить порт пробы, сделайте следующее:

1.  Проверьте текущее значение **ProbePort**, выполнив следующую команду PowerShell:

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   Эту команду следует выполнять на одной из виртуальных машин в конфигурации кластера.

2.  Укажите порт пробы. По умолчанию для проб задан порт 0. В нашем примере мы используем порт пробы 62000.

  ![Рис. 3. По умолчанию в конфигурации кластера для проб используется порт 0][sap-ha-guide-figure-3048]

  _**Рис. 3.** По умолчанию в конфигурации кластера для проб используется порт 0_

  Номер порта определен в шаблонах Azure Resource Manager для SAP. Назначить номер порта можно в PowerShell.

  Чтобы задать новое значение ProbePort для кластерного ресурса SAP \<SID\> IP, выполните следующий скрипт PowerShell, который изменяет значение переменных среды PowerShell:

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  После подключения к сети роли кластера SAP \<SID\> убедитесь, что используется новое значение параметра **ProbePort**.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  После выполнения скрипта вам будет предложено перезапустить кластерную группу SAP, чтобы активировать изменения.

  ![Рис. 4. Порт пробы кластера после изменения значения][sap-ha-guide-figure-3049]

  _**Рис. 4.** Порт пробы кластера после изменения значения_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Открытие порта пробы в брандмауэре Windows

Откройте порт пробы в брандмауэре Windows на обоих узлах кластера. Чтобы открыть порт пробы брандмауэра Windows, используйте следующий скрипт. Обновите переменные PowerShell для своей среды.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

Для **ProbePort** установлено значение **62000**. Теперь возможен доступ к файловому ресурсу \\\ascsha-clsap\sapmnt с других узлов, например ascsha-dbas.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Установка экземпляра базы данных

Чтобы установить экземпляр базы данных, следуйте инструкциям в документации по установке SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Установка второго узла кластера

Чтобы установить второй кластер, выполните действия, которые описаны в руководстве по установке SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Изменение типа запуска службы Windows для экземпляра SAP ERS

На обоих узлах кластера измените тип запуска служб Windows SAP ERS на **Автоматически (отложенный запуск)**.

![Рис. 5. Изменение типа службы для экземпляра SAP ERS на "Автоматически (отложенный запуск)"][sap-ha-guide-figure-3050]

_**Рис. 5.** Изменение типа службы для экземпляра SAP ERS на "Автоматически (отложенный запуск)"_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Установка основного сервера приложений SAP

Установите экземпляр основного сервера приложений (PAS) \<SID\>-di-0 на виртуальную машину, предназначенную для размещения PAS. В Azure нет зависимостей. Для DataKeeper не требуется специальных настроек.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Установка дополнительного сервера приложений SAP

Установите дополнительный сервер приложений SAP (AAS) на все виртуальные машины, предназначенные для размещения экземпляра сервера приложений SAP. В нашем примере это диапазон с \<SID\>-di-1 по \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> После этого установка системы SAP NetWeaver с высоким уровнем доступности будет завершена. Затем перейдите к тестированию отработки отказа.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Тестирование отработки отказа экземпляра SAP ASCS/SCS и репликации SIOS
С помощью диспетчера отказоустойчивости кластеров и инструмента управления и настройки SIOS DataKeeper можно легко выполнять тестирование и мониторинг отработки отказа экземпляров SAP ASCS/SCS и репликации дисков SIOS.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Экземпляр SAP ASCS/SCS выполняется на узле A кластера

Кластерная группа SAP PR1 работает на узле A, в нашем примере это узел pr1-ascs-0. Присвойте узлу A общий диск S, который входит в кластерную группу SAP PR1. Экземпляр ASCS/SCS также использует этот диск S. 

![Рис. 6. Диспетчер отказоустойчивости кластеров: кластерная группа SAP \<SID\> работает на узле A кластера][sap-ha-guide-figure-5000]

_**Рис. 6.** Диспетчер отказоустойчивости кластеров: кластерная группа SAP \<SID\> работает на узле A кластера_

С помощью инструмента управления и настройки SIOS DataKeeper можно увидеть, что данные общего диска синхронно реплицируются с исходного дискового тома S на узле А кластера на целевой дисковый том S на узле B кластера (в нашем примере с pr1-ascs-0 [10.0.0.40] на pr1-ascs-1 [10.0.0.41]).

![Рис. 7. SIOS DataKeeper: репликация локального тома с узла A на узел B кластера][sap-ha-guide-figure-5001]

_**Рис. 7.** SIOS DataKeeper: репликация локального тома с узла A на узел B кластера_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Отработка отказа с узла A на узел B

1.  Инициируйте отработку отказа кластерной группы SAP \<SID\> с узла A на узел B кластера одним из следующих способов:
  - через диспетчер отказоустойчивости кластеров;  
  - с помощью команд PowerShell для отказоустойчивого кластера.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Перезапустите узел A кластера из гостевой ОС Windows. Это инициирует автоматический переход кластерной группы SAP \<SID\> на другой ресурс с узла A на узел B кластера.  
3.  Перезапустите узел A кластера с помощью портала Azure. Это инициирует автоматический переход кластерной группы SAP \<SID\> на другой ресурс с узла A на узел B кластера.  
4.  Перезапустите узел A кластера с помощью Azure PowerShell. Это инициирует автоматический переход кластерной группы SAP \<SID\> на другой ресурс с узла A на узел B кластера.

  После отработки отказа кластерная группа SAP \<SID\> будет выполняться на узле B кластера, в нашем примере это pr1-ascs-1.

  ![Рис. 8. Диспетчер отказоустойчивости кластеров: кластерная группа SAP \<SID\> работает на узле B кластера][sap-ha-guide-figure-5002]

  _**Рис. 8.** Диспетчер отказоустойчивости кластеров: кластерная группа SAP \<SID\> работает на узле B кластера_

  Теперь общий диск подключен к узлу B кластера, а SIOS DataKeeper реплицирует данные с исходного дискового тома S на узле B кластера на целевой дисковый том S на узле А кластера (в нашем примере с pr1-ascs-1 [10.0.0.41] на pr1-ascs-0 [10.0.0.40]).

  ![Рис. 9. SIOS DataKeeper: репликация локального тома с узла B на узел A кластера][sap-ha-guide-figure-5003]

  _**Рис. 9.** SIOS DataKeeper: репликация локального тома с узла B на узел A кластера_
