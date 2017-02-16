---
title: "SAP NetWeaver на виртуальных машинах Linux. Руководство по развертыванию | Документация Майкрософт"
description: "SAP NetWeaver на виртуальных машинах Linux — руководство по развертыванию"
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 3a5e22315e338fe089cf557688f741ee16416da2
ms.openlocfilehash: 5214f55b8e65ec758ba93fb86d548b45a1b4f21e


---
# <a name="sap-netweaver-on-azure-virtual-machines-vms--deployment-guide"></a>SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (SAP NetWeaver on Linux virtual machines (VMs) – DBMS Deployment Guide) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching for VMs and VHDs) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure of a RDBMS Deployment) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (High Availability and Disaster Recovery with Azure VMs) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 and earlier releases) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Using a SQL Server images out of the Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General SQL Server for SAP on Azure Summary) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Storage configuration) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup and Restore) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Performance Considerations for Backup and Restore) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (SAP NetWeaver on Linux virtual machines (VMs) – Deployment Guide) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP Resources) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Deploying a VM with a custom image) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Deploying a VM out of the Azure Marketplace for SAP) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Deploying a VM with a custom image for SAP) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Moving a VM from on-premises using a non-generalized Azure VHD with SAP) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Deployment Scenarios of VMs for SAP on Microsoft Azure) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Deploying Azure PowerShell cmdlets) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download and Import SAP relevant PowerShell cmdlets) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Join VM into on-premises Domain - Windows only) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Download, Install and enable Azure VM Agent) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure Azure Enhanced Monitoring Extension for SAP) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Readiness Check for Azure Enhanced Monitoring for SAP) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Health check for Azure Monitoring Infrastructure Configuration) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Further troubleshooting of Azure Monitoring infrastructure for SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (SAP NetWeaver on Linux virtual machines (VMs) – Planning and Implementation Guide) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resources) [planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (High Availability (HA) and Disaster Recovery (DR) for SAP NetWeaver running on Azure Virtual Machines) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (High Availability for SAP Application Servers) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Using Autostart for SAP instances) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud-Only - Virtual Machine deployments into Azure without dependencies on the on-premises customer network) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-Premise - Deployment of single or multiple SAP VMs into Azure with the requirement of being fully integrated into the on-premises network) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure Regions) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fault Domains) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgrade Domains) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure Availability Sets) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (The Microsoft Azure Virtual Machine Concept) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Moving a VM from on-premises to Azure with a non-generalized disk) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Deploying a VM with a customer specific image) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparation for moving a VM from on-premises to Azure with a non-generalized disk) [planning-guide-5.2.2]:virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparation for deploying a VM with a customer specific image for SAP) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparing VMs with SAP for Azure) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Difference Between an Azure Disk and Azure Image) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Uploading a VHD from on-premises to Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copying disks between Azure Storage Accounts) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD structure for SAP deployments) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Setting automount for attached disks) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Single VM with SAP NetWeaver demo/training scenario) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts of Cloud-Only deployment of SAP instances) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure позволяет компаниям максимально быстро получать вычислительные ресурсы и ресурсы хранения без длительных циклов закупки. С помощью виртуальных машин Azure на предприятиях можно развернуть классические приложения, например приложения на основе SAP NetWeaver, в среде Azure и повысить их надежность и доступность без хранения ресурсов локально. Microsoft Azure также поддерживает возможность подключения между организациями, благодаря чему компании могут активно интегрировать виртуальные машины Azure в локальные домены, частные облака и системный ландшафт SAP.

В этом техническом документе приведено пошаговое руководство по подготовке виртуальной машины Azure к развертыванию приложений на основе SAP NetWeaver. Предполагается, что вы уже ознакомились с [руководством по планированию и реализации][planning-guide]. В противном случае сначала следует изучить соответствующий документ.

Это руководство дополняет документацию по установке SAP и комментарии по SAP, которые являются основными ресурсами по установке и развертыванию SAP на упомянутых платформах.

## <a name="introduction"></a>Введение
Во многих компаниях по всему миру используются приложения на основе SAP NetWeaver (а именно SAP Business Suite) для выполнения критически важных бизнес-процессов. Таким образом, работоспособность системы выходит на первое место, а возможность предоставления поддержки предприятиям в случае сбоя, включая проблемы с ухудшением производительности, становится крайне важным требованием.
Microsoft Azure предоставляет превосходные инструменты платформы для удовлетворения требованиям к поддержке всех критически важных для бизнеса приложений. Это руководство позволяет настроить виртуальную машину Microsoft Azure, предназначенную для развертывания программного обеспечения SAP, таким образом, чтобы обеспечить предоставление поддержки предприятиям независимо от способа создания виртуальной машины (получение из Azure Marketplace или создание на основе пользовательского образа).
Далее подробно описаны все действия, необходимые для настройки.

## <a name="prerequisites-and-resources"></a>Предварительные условия и ресурсы
### <a name="prerequisites"></a>Предварительные требования
Перед началом работы необходимо выполнить все предварительные условия, описанные в следующих разделах.

#### <a name="local-personal-computer"></a>Локальный персональный компьютер
Настройка виртуальной машины Azure для развертывания программного обеспечения SAP состоит из нескольких этапов. Чтобы управлять виртуальными машинами Windows или Linux, необходимо использовать сценарий PowerShell и портал Microsoft Azure. Для этого понадобится локальный персональный компьютер под управлением Windows 7 или более поздней версии. Если вам требуется управлять только виртуальными машинами Linux и использовать для этого компьютер Linux, это также можно сделать с помощью интерфейса командной строки Azure.

#### <a name="internet-connection"></a>Подключение к Интернету
Чтобы скачать и запустить необходимые средства и сценарии, требуется подключение к Интернету. Кроме того, виртуальной машине Microsoft Azure, на которой выполняется расширение для расширенного мониторинга Azure для SAP, требуется доступ к Интернету. Если эта виртуальная машина Azure является частью виртуальной сети Azure или локального домена, настройте соответствующие параметры прокси-сервера, как описано в разделе [Настройка прокси-сервера][deployment-guide-configure-proxy] в этом документе.

#### <a name="microsoft-azure-subscription"></a>Подписка на Microsoft Azure
Учетная запись Azure уже должна быть создана, а соответствующие учетные данные для входа — известны.

#### <a name="topology-consideration-and-networking"></a>Сеть и рекомендации по топологии
Необходимо определить топологию и архитектуру развертывания SAP в Azure. Требуется определить архитектуру для следующих компонентов:

* используемые учетные записи хранения Microsoft Azure;
* виртуальная сеть, в которой будет развернута система SAP;
* группа ресурсов, в которой будет развернута система SAP;
* регион Azure, в котором будет развернута система SAP;
* конфигурация SAP (двух- или трехуровневая);
* размер виртуальных машин и число дополнительных виртуальных жестких дисков, которые будут подключены к ним;
* конфигурация системы SAP Correction and Transport.

Таким образом, учетные записи хранения Azure или виртуальные сети Azure уже должны быть созданы и настроены. Сведения об их создании и настройке см. в [руководстве по планированию и реализации][planning-guide].

#### <a name="sap-sizing"></a>Определение размера системы SAP
* Планируемая рабочая нагрузка SAP определяется, например, с помощью SAP Quicksizer, а соответствующие показатели системы SAP известны. 
* Должны быть известны сведения о потреблении ресурсов ЦП и памяти для системы SAP.
* Должно быть известно требуемое число операций ввода-вывода в секунду.
* Требуемая пропускная способность сети при дальнейшем обмене данными между разными виртуальными машинами в Azure известна.
* Необходимая пропускная способность сети между локальными ресурсами и системами SAP, развернутыми в Azure, известна.

#### <a name="resource-groups"></a>Группы ресурсов
Группы ресурсов — это новое понятие в развертывании. Группа ресурсов содержит все ресурсы с одинаковым жизненным циклом, например такие, которые создаются и удаляются одновременно. Дополнительные сведения о группах ресурсов см. в [этой статье][resource-group-overview]. 

### <a name="a-name42ee2bdb-1efc-4ec7-ab31-fe4c22769b94asap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Ресурсы SAP
Во время конфигурации необходимы следующие ресурсы:

* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, которые поддерживаются для развертывания программного обеспечения SAP; 
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.
  
* примечание к SAP [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Microsoft Azure;
* примечание к SAP [2178632], содержащее подробные сведения о всех доступных метриках мониторинга для SAP в Microsoft Azure; 
* примечание к SAP [1409604], содержащее сведения о необходимой версии агента SAP Host Agent для Windows в Microsoft Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Microsoft Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1984787], содержащее общие сведения о SUSE LINUX Enterprise Server 12;
* примечание к SAP [2002167], содержащее общие сведения о Red Hat Enterprise Linux 7.x;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок для расширенного мониторинга Azure для SAP;
* [вики-сайт SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), содержащий все необходимые примечания к SAP для Linux;
* командлеты PowerShell для SAP, которые являются частью [Azure PowerShell][azure-ps];
* интерфейс командной строки Azure для SAP, который является частью [интерфейса командной строки Azure][azure-cli] (Azure CLI);
* [Портал Microsoft Azure][azure-portal].

[comment]: <> (MSSedusch, TODO: добавить уровень исправлений ARM для агента SAP Host Agent в примечание SAP 1409604)

Ниже приведены руководства по использованию SAP в Microsoft Azure.

* [SAP NetWeaver на виртуальных машинах Azure. Руководство по планированию и внедрению][planning-guide]
* [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию (этот документ)][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД][dbms-guide]

## <a name="a-nameb3253ee3-d63b-4d74-a49b-185e76c4088eadeployment-scenarios-of-vms-for-sap-on-microsoft-azure"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Сценарии развертывания виртуальных машин для SAP в Microsoft Azure
В этом разделе рассматриваются различные способы развертывания, а также отдельные указания для каждого типа развертывания.

### <a name="deployment-of-vms-for-sap"></a>Развертывание виртуальных машин для SAP
Microsoft Azure предусматривает несколько способов развертывания виртуальных машин и связанных дисков. Важно понимать различия между ними, так как подготовка виртуальной машины может отличаться в зависимости от способа развертывания. Здесь будут рассмотрены следующие сценарии:

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Развертывание виртуальной машины из Azure Marketplace
Вы можете использовать для развертывания виртуальной машины образ из Azure Marketplace, предоставленный корпорацией Майкрософт или сторонним поставщиком. Когда вы развернете виртуальную машину в Microsoft Azure, следует выполнить те же рекомендации и использовать те же средства для установки программного обеспечения SAP, что и в локальной среде. Чтобы установить программное обеспечение SAP на виртуальную машину Azure, специалисты SAP и Майкрософт рекомендуют скачать и сохранить установочный носитель SAP на виртуальных жестких дисках Azure или создать виртуальную машину Azure в качестве файлового сервера, содержащего все необходимые установочные носители SAP.

[comment]: <> (MSSedusch, TODO: зачем нужно рекомендовать управление файлами, например файловый сервер или виртуальный жесткий диск? Это сильно отличается от локальной среды?)

Дополнительные сведения см. в разделе [Сценарий 1. Развертывание виртуальной машины для SAP из Azure Marketplace][deployment-guide-3.2].

#### <a name="a-name3688666f-281f-425b-a312-a77e7db2dfabadeploying-a-vm-with-a-custom-image"></a><a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Развертывание виртуальной машины с помощью пользовательского образа
Если для вашей версии ОС или СУБД существуют особые требования по установке исправлений, представленные в Azure Marketplace образы могут не соответствовать вашим потребностям. В этом случае следует создать виртуальную машину с помощью собственного пользовательского образа ОС или СУБД для виртуальной машины, который можно разворачивать многократно.
Процедуры создания собственного образа в Windows и в Linux отличаются.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Чтобы подготовить образ Windows, который можно использовать для развертывания нескольких виртуальных машин, необходимо задать абстрагированные или обобщенные параметры Windows (например, идентификатор безопасности Windows и имя узла) на локальной виртуальной машине. Это можно сделать с помощью инструмента Sysprep, как описано на странице <https://msdn.microsoft.com/library/hh825084.aspx>.
> 
> ![Linux][Logo_Linux] Linux
> 
> Чтобы подготовить образ Linux, который можно использовать для развертывания нескольких виртуальных машин, необходимо задать некоторые абстрагированные или обобщенные параметры Linux на локальной виртуальной машине. Это можно сделать с помощью команды waagent -deprovision, как описано в [этой][virtual-machines-linux-capture-image] или [этой статье][virtual-machines-linux-agent-user-guide-command-line-options].
> 
> 

- - -
Содержимое базы данных можно настроить с помощью SAP Software Provision Manager, чтобы установить новую систему SAP, восстановить резервную копию базы данных с виртуального жесткого диска, подключенного к виртуальной машине, или восстановить резервную копию базы данных напрямую из службы хранилища Azure, если СУБД поддерживает это. (См. [руководство по развертыванию СУБД][dbms-guide].) Если вы уже установили систему SAP на локальную виртуальную машину (особенно важно для двухуровневых систем), то параметры системы SAP можно адаптировать после развертывания виртуальной машины в Azure. Для этого выполните процедуру переименования системы, которую поддерживает SAP Software Provisioning Manager (см. примечание к SAP [1619720]). В противном случае программное обеспечение SAP можно установить позже после развертывания виртуальной машины Azure.

Дополнительные сведения см. в разделе [Сценарий 2. Развертывание виртуальной машины для SAP с помощью пользовательского образа][deployment-guide-3.3].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Перемещение виртуальной машины из локальной среды в Microsoft Azure с помощью специализированного диска
Предположим, что нужно переместить определенную систему SAP из локальной среды в Microsoft Azure. Для этого можно передать в Microsoft Azure виртуальный жесткий диск, который содержит ОС, двоичные файлы SAP и конечные двоичные файлы СУБД, а также дополнительные виртуальные жесткие диски с файлами данных и журналов СУБД. В отличие от сценария, описанного в разделе [Развертывание виртуальной машины с помощью пользовательского образа][deployment-guide-3.1.2], следует сохранить имя узла, идентификатор безопасности и учетные записи пользователей SAP в виртуальной машине Azure, так как они были настроены в локальной среде. В этом случае не требуется выполнять обобщение операционной системы. Этот случай главным образом касается сценариев, при которых часть ландшафта SAP выполняется локально, а другая часть — в Microsoft Azure.

Дополнительные сведения см. в разделе [Сценарий 3. Перемещение виртуальной машины из локальной среды с помощью специализированного виртуального жесткого диска Azure с SAP][deployment-guide-3.4].

### <a name="a-namedb477013-9060-4602-9ad4-b0316f8bb281ascenario-1-deploying-a-vm-out-of-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Сценарий 1. Развертывание виртуальной машины для SAP из Azure Marketplace
В Microsoft Azure можно развернуть экземпляр виртуальной машины из Azure Marketplace, в котором содержится несколько стандартных образов ОС Windows Server и различные дистрибутивы Linux. Можно также развернуть образ, содержащий номера SKU СУБД, например Microsoft SQL Server. Дополнительные сведения об использовании этих образов с номерами SKU СУБД см. в [руководстве по развертыванию СУБД][dbms-guide].

Последовательность действий по развертыванию виртуальной машины из Azure Marketplace для SAP выглядит следующим образом:

![Блок-схема развертывания виртуальной машины для систем SAP с помощью образа виртуальной машины из Azure Marketplace][deployment-guide-figure-100]

Представленные на блок-схеме действия, которые необходимо выполнить, описаны в следующих разделах.

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Создание виртуальной машины с помощью портала Azure
Самый простой способ создать виртуальную машину — с помощью образа из Azure Marketplace на портале Azure. Перейдите по ссылке <https://portal.azure.com/#create> или щелкните знак "+" в левой части портала Azure. В поле поиска введите тип развертываемой операционной системы, например Windows, SUSE или RHEL, и выберите версию. Выберите модель развертывания Resource Manager и нажмите кнопку "Создать".

Мастер позволит настроить необходимые параметры для создания виртуальной машины, а также все необходимые ресурсы, например сетевые интерфейсы или учетные записи хранения. Ниже приведены некоторые из этих параметров.

1. Основы
   1. Имя. Имя ресурса, например виртуальной машины.
   1. Имя пользователя и пароль или открытый ключ SSH. Введите имя пользователя и пароль для учетной записи пользователя, которая создается во время подготовки. Для виртуальной машины Linux можно также ввести открытый ключ SSH, который нужно использовать для входа на компьютер по протоколу SSH.
   1. Подписка. Выберите подписку, которую нужно использовать для подготовки новой виртуальной машины.
   1. Группа ресурсов. Имя группы ресурсов. Можно вставить имя новой или имеющейся группы ресурсов.
   1. Расположение. Выберите расположение, где следует развернуть новую виртуальную машину. Если виртуальную машину требуется подключить к локальной сети, выберите расположение виртуальной сети, по которой Azure подключается к локальной сети. Дополнительные сведения см. в разделе [Сети Microsoft Azure][planning-guide-microsoft-azure-networking] в [руководстве по планированию][planning-guide].
1. Размер

    Ознакомьтесь с примечанием к SAP [1928533], чтобы просмотреть список поддерживаемых типов виртуальных машин. Кроме того, выберите правильный тип, если нужно использовать хранилище класса Premium. Не все типы виртуальных машин поддерживают хранилище класса Premium. Дополнительные сведения см. в разделах [Хранилище. Служба хранилища Microsoft Azure и диски данных][pplanning-guide-storage-microsoft-azure-storage-and-data-disks] и [Хранилище Azure уровня "Премиум"][planning-guide-azure-premium-storage] в [руководстве по планированию][planning-guide].
    
1. Параметры
   1. Учетная запись хранения. Выберите существующую учетную запись хранения или создайте новую. Дополнительные сведения о различных типах хранилищ см. в разделе [Служба хранилища Microsoft Azure][dbms-guide-2.3] в [руководстве по СУБД][dbms-guide]. Обратите внимание, что запуск приложений SAP поддерживают не все типы хранилищ.
   1. Виртуальная сеть и подсети. Выберите виртуальную сеть, подключенную к локальной сети, если требуется интегрировать виртуальную машину в интрасеть.
   1. Общедоступный IP-адрес. Выберите общедоступный IP-адрес, который требуется использовать, или введите параметры, чтобы создать его. Общедоступный IP-адрес можно использовать для доступа к виртуальной машине через Интернет. Кроме того, создайте группу безопасности сети для фильтрации доступа к виртуальной машине.
   1. Группа безопасности сети. Дополнительные сведения см. в статье [Группа безопасности сети][virtual-networks-nsg].
   1. Доступность. Выберите группу доступности или введите параметры, чтобы создать ее. Дополнительные сведения см. в разделе [Группы доступности Azure][planning-guide-3.2.3].
   1. Мониторинг. Параметр диагностики можно отключить. Он включается автоматически при выполнении команды для включения расширенного мониторинга Azure, как описано в разделе [Настройка мониторинга][deployment-guide-configure-monitoring-scenario-1].
   
1. Сводка. Проверьте информацию на странице сводки и нажмите кнопку "ОК".

После завершения работы мастера виртуальная машина будет развернута в выбранной группе ресурсов.

#### <a name="create-virtual-machine-using-a-template"></a>Создание виртуальной машины с помощью шаблона
Развертывание можно также создать с помощью одного из шаблонов SAP, опубликованных в [репозитории azure-quickstart-templates на сайте GitHub][azure-quickstart-templates-github]. Кроме того, можно создать виртуальную машину вручную с помощью [Azure Portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] или [интерфейса командной строки Azure][virtual-machines-linux-tutorial].

* [Шаблон двухуровневой конфигурации (только одна виртуальная машина) (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]. Этот шаблон используется, если нужно создать двухуровневую систему с использованием только одной виртуальной машины.
* [Шаблон трехуровневой конфигурации (несколько виртуальных машин) (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]. Этот шаблон используется, если нужно создать трехуровневую систему с использованием нескольких виртуальных машин.

После открытия одного из шаблонов, приведенных выше, на портале Azure отобразится страница, на которой можно будет ввести параметры для шаблона. Введите следующие сведения:

1. Основы
  * **Подписка**: подписка, в которой необходимо развернуть шаблон.
  * **Группа ресурсов**: группа ресурсов, в которой требуется развернуть шаблон. Можно создать новую группу ресурсов или выбрать существующую в выбранной подписке.
  * **Расположение**: расположение, в котором необходимо развернуть шаблон. Если вы выбрали существующую группу ресурсов, используется расположение этой группы ресурсов.
1. Параметры
  * **Sap System Id** (Идентификатор системы SAP). Идентификатор системы SAP.
  * **Os Type** (Тип ОС). Операционная система, которую требуется развернуть, например Windows Server 2012 R2, SLES 12 или RHEL 7.2.
    * Список не содержит все поддерживаемые операционные системы, например, в нем нет Windows Server 2008 R2, хотя эта ОС поддерживается SAP. Ознакомьтесь с примечанием к SAP [1928533], чтобы просмотреть список всех поддерживаемых операционных систем.
  * **Sap System Size** (Размер системы SAP). Размер системы SAP.
    * Количество систем SAP, которые будет содержать система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям SAP или к системному интегратору SAP.
  * **System Availability** (Доступность системы). Доступность системы (только для трехуровневого шаблона).
    * Выберите высокую доступность в качестве конфигурации, которая подходит для установки высокого уровня доступности. Будут созданы два сервера баз данных и два сервера для ASCS.
  * **Storage Type** (Тип хранилища). Тип хранилища, которое следует использовать (только для двухуровневого шаблона).
    * Для систем большого размера настоятельно рекомендуется использовать хранилище класса Premium. Дополнительные сведения о различных типах хранилищ см. в разделе
      * [Использование хранилища SSD класса Premium Azure для экземпляра СУБД SAP][2367194]
      * [Служба хранилища Microsoft Azure][dbms-guide-2.3] [руководство по СУБД][dbms-guide]
      * [Хранилище Premium: высокопроизводительное хранилище для рабочих нагрузок виртуальной машины Azure][storage-premium-storage-preview-portal]
      * [Введение в службу хранилища Microsoft Azure][storage-introduction]
  * **Admin Username** (Администратор) и **Admin Password** (Пароль администратора). Имя пользователя и пароль.
    * Создается учетная запись пользователя, которую можно использовать для входа на компьютер.
  * **New Or Existing Subnet** (Новая или существующая подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите ее.
  * **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Выберите подсеть виртуальной сети VPN или Express Route, чтобы подключить виртуальную машину к локальной сети. Идентификатор обычно выглядит следующим образом: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

1. Условия использования  
    Прочтите и примите юридические условия.

После ввода всех параметров подтвердите их, нажав кнопку "Purchase" (Купить). 

Обратите внимание, что при использовании образа из Azure Marketplace агент виртуальной машины Azure развертывается по умолчанию.

#### <a name="configure-proxy-settings"></a>Настройка параметров прокси-сервера
В зависимости от конфигурации локальной сети на виртуальной машине может потребоваться настроить прокси-сервер, если она подключена к локальной сети через VPN или Express Route. В противном случае виртуальная машина не сможет получить доступ к Интернету и скачать необходимые расширения или выполнить сбор данных мониторинга. Ознакомьтесь с разделом [Настройка прокси-сервера][deployment-guide-configure-proxy] в этом документе.

#### <a name="join-domain-windows-only"></a>Присоединение к домену (только для Windows)
Если развертывание в Azure подключено к локальному экземпляру AD или к локальной DNS с помощью подключения типа "сеть — сеть" Azure или канала Express Route (также называется распределенным подключением в [руководстве по планированию и реализации][planning-guide]), то предполагается, что виртуальная машина присоединяется к локальному домену. Рекомендации к этому шагу описаны в разделе [Присоединение виртуальной машины к локальному домену (только для Windows)][deployment-guide-4.3] в этом документе.

#### <a name="a-nameec323ac3-1de9-4c3a-b770-4ff701def65baconfigure-monitoring"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Настройка мониторинга
Чтобы обеспечить совместимость среды с SAP, настройте расширение для расширенного мониторинга Azure для SAP, как описано в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5] в этом документе.

Проверьте предварительные требования для мониторинга SAP относительно необходимых минимальных версий SAP Kernel и агента SAP Host Agent в ресурсах, указанных в разделе [Ресурсы SAP][deployment-guide-2.2] в этом документе.

#### <a name="monitoring-check"></a>Проверка мониторинга
Проверьте, работает ли мониторинг, как описано в разделе [Проверки и устранение неполадок при комплексной настройке мониторинга для SAP в Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Процедуры, выполняемые после развертывания
Созданная виртуальная машина будет развернута. Вам понадобится установить на ней все необходимые программные компоненты. Следовательно, для этого типа развертывания виртуальной машины потребуется установить программное обеспечение, доступное в Microsoft Azure на другой виртуальной машине или в качестве диска, который можно подключить. Кроме того, можно рассмотреть сценарии распределенного подключения, где подключение к локальным ресурсам (общие папки для установки) установлено по умолчанию.

### <a name="a-name54a1fc6d-24fd-4feb-9c57-ac588a55dff2ascenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Сценарий 2. Развертывание виртуальной машины для SAP с помощью пользовательского образа
Как описано в [руководстве по планированию и реализации][planning-guide], можно подготовить, создать и использовать пользовательский образ для создания нескольких виртуальных машин. Последовательность действий на блок-схеме будет выглядеть следующим образом:

![Блок-схема развертывания виртуальной машины для систем SAP с помощью образа виртуальной машины в частном Marketplace][deployment-guide-figure-300]

Представленные на блок-схеме действия, которые необходимо выполнить, описаны в следующих разделах.

#### <a name="create-the-virtual-machine"></a>Создание виртуальной машины
Чтобы создать развертывание с помощью частного образа ОС на портале Azure, воспользуйтесь одним из шаблонов SAP, перечисленных ниже. Эти шаблоны опубликованы в репозитории GitHub [azure-quickstart-templates][azure-quickstart-templates-github].
Кроме того, виртуальную машину можно создать вручную с помощью [PowerShell][virtual-machines-upload-image-windows-resource-manager]. 

* [Шаблон двухуровневой конфигурации (только одна виртуальная машина) (sap-2-tier-user-image)][sap-templates-2-tier-user-image]. Этот шаблон используется, если нужно создать двухуровневую систему с использованием только одной виртуальной машины и собственного образа ОС.
* [Шаблон трехуровневой конфигурации (несколько виртуальных машин) (sap-3-tier-user-image)][sap-templates-3-tier-user-image]. Этот шаблон используется, если нужно создать трехуровневую систему с использованием нескольких виртуальных машин и собственного образа ОС.

После открытия одного из шаблонов, приведенных выше, на портале Azure отобразится страница, на которой можно будет ввести параметры для шаблона. Введите следующие сведения:

1. Основы
  * **Подписка**: подписка, в которой необходимо развернуть шаблон.
  * **Группа ресурсов**: группа ресурсов, в которой требуется развернуть шаблон. Можно создать новую группу ресурсов или выбрать существующую в выбранной подписке.
  * **Расположение**: расположение, в котором необходимо развернуть шаблон. Если вы выбрали существующую группу ресурсов, используется расположение этой группы ресурсов.
1. Параметры
  * **Sap System Id** (Идентификатор системы SAP). Идентификатор системы SAP.
  * **Os Type** (Тип ОС). Тип операционной системы, которую необходимо развернуть (Windows или Linux).
  * **Sap System Size** (Размер системы SAP). Размер системы SAP.
    * Количество систем SAP, которые будет содержать система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям SAP или к системному интегратору SAP.
  * **System Availability** (Доступность системы). Доступность системы (только для трехуровневого шаблона). 
    * Выберите высокую доступность в качестве конфигурации, которая подходит для установки высокого уровня доступности. Будут созданы два сервера баз данных и два сервера для ASCS.
  * **Storage Type** (Тип хранилища). Тип хранилища, которое следует использовать (только для двухуровневого шаблона). 
    * Для систем большого размера настоятельно рекомендуется использовать хранилище класса Premium. Дополнительные сведения о различных типах хранилищ см. в разделе 
      * [Использование хранилища SSD класса Premium Azure для экземпляра СУБД SAP][2367194]
      * [Служба хранилища Microsoft Azure][dbms-guide-2.3] [руководство по СУБД][dbms-guide]
      * [Хранилище Premium: высокопроизводительное хранилище для рабочих нагрузок виртуальной машины Azure][storage-premium-storage-preview-portal]
      * [Введение в службу хранилища Microsoft Azure][storage-introduction]
  * **User Image Vhd Uri** (Универсальный код ресурса (URI) VHD пользовательского образа). Универсальный код ресурса (URI) виртуального жесткого диска частного образа ОС, например https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd.
  * **User Image Storage Account** (Учетная запись хранения пользовательского образа). Имя учетной записи хранения, где хранится частный образ ОС, например `<accountname`> в приведенном выше примере универсального кода ресурса (URI).
  * **Admin Username** (Администратор) и **Admin Password** (Пароль администратора). Имя пользователя и пароль.
    * Создается учетная запись пользователя, которую можно использовать для входа на компьютер.
  * **New Or Existing Subnet** (Новая или существующая подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите ее.
  * **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Выберите подсеть виртуальной сети VPN или Express Route, чтобы подключить виртуальную машину к локальной сети. Идентификатор обычно выглядит следующим образом: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

1. Условия использования  
    Прочтите и примите юридические условия.

После ввода всех параметров подтвердите их, нажав кнопку "Purchase" (Купить).

#### <a name="install-vm-agent-linux-only"></a>Установка агента виртуальной машины (только для Linux)
Если требуется использовать приведенные выше шаблоны, агент Linux уже должен быть установлен в пользовательский образ. В противном случае подготовка завершится с ошибкой. Скачайте и установите агент виртуальной машины в пользовательский образ, как описано в разделе [Скачивание, установка и включение агента виртуальной машины Azure][deployment-guide-4.4] в этом документе.
Если приведенные выше шаблоны не используются, агент виртуальной машины можно также установить позже.

#### <a name="join-domain-windows-only"></a>Присоединение к домену (только для Windows)
Если развертывание в Azure подключено к локальному экземпляру AD или к локальной DNS с помощью подключения типа "сеть — сеть" Azure или канала Express Route (также называется распределенным подключением в [руководстве по планированию и реализации][planning-guide]), то предполагается, что виртуальная машина присоединяется к локальному домену. Рекомендации к этому шагу описаны в разделе [Присоединение виртуальной машины к локальному домену (только для Windows)][deployment-guide-4.3] в этом документе.

#### <a name="configure-proxy-settings"></a>Настройка параметров прокси-сервера
В зависимости от конфигурации локальной сети на виртуальной машине может потребоваться настроить прокси-сервер, если она подключена к локальной сети через VPN или Express Route. В противном случае виртуальная машина не сможет получить доступ к Интернету и скачать необходимые расширения или выполнить сбор данных мониторинга. Ознакомьтесь с разделом [Настройка прокси-сервера][deployment-guide-configure-proxy] в этом документе.

#### <a name="configure-monitoring"></a>Настройка мониторинга
Чтобы обеспечить совместимость среды с SAP, настройте расширение для мониторинга Azure для SAP, как описано в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5] в этом документе.
Проверьте предварительные требования для мониторинга SAP относительно необходимых минимальных версий SAP Kernel и агента SAP Host Agent в ресурсах, указанных в разделе [Ресурсы SAP][deployment-guide-2.2] в этом документе.

#### <a name="monitoring-check"></a>Проверка мониторинга
Проверьте, работает ли мониторинг, как описано в разделе [Проверки и устранение неполадок при комплексной настройке мониторинга для SAP в Azure][deployment-guide-troubleshooting-chapter].

### <a name="a-namea9a60133-a763-4de8-8986-ac0fa33aa8c1ascenario-3-moving-a-vm-from-on-premises-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Сценарий 3. Перемещение виртуальной машины из локальной среды с помощью специализированного виртуального жесткого диска Azure с SAP
Если планируется переместить систему SAP в ее текущем виде (с тем же именем узла и ИД безопасности SAP) из локальной среды в Azure, то при развертывании в качестве диска операционной системы напрямую используется VHD, а не ссылка на образ. В этом случае агент виртуальной машины не будет автоматически устанавливаться во время развертывания. Так как агент виртуальной машины и расширение для расширенного мониторинга Azure для SAP являются необходимыми компонентами для поддержки SAP, их необходимо скачать, установить и включить вручную после создания виртуальной машины. 

Дополнительные сведения об агенте виртуальной машины Azure см. в этой статье:

[comment]: <> (MSSedusch, TODO: нужно обновить ссылку для Windows ниже) 

- - -
> ![Windows][Logo_Windows] Windows
> 
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> [Руководство пользователя агента Linux для Azure][virtual-machines-linux-agent-user-guide]
> 
> 

- - -
Рабочий процесс для разных шагов выглядит следующим образом:

![Блок-схема развертывания виртуальной машины для систем SAP с помощью диска виртуальной машины][deployment-guide-figure-400]

Если диск уже передан и определен в Azure (см. [руководство по планированию и реализации][planning-guide]), сделайте следующее.

#### <a name="create-virtual-machine"></a>Создание виртуальной машины
Чтобы создать развертывание с помощью частного диска операционной системы на портале Azure, воспользуйтесь шаблоном SAP, опубликованным в [репозитории azure-quickstart-templates на сайте GitHub][azure-quickstart-templates-github]. Кроме того, можно создать виртуальную машину с помощью [PowerShell][virtual-machines-windows-create-vm-specialized] вручную.

* [Шаблон двухуровневой конфигурации (только одна виртуальная машина) (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]
  * Этот шаблон используется, если нужно создать двухуровневую систему с использованием только одной виртуальной машины.

После открытия одного из шаблонов, приведенных выше, на портале Azure отобразится страница, на которой можно будет ввести параметры для шаблона. Введите следующие сведения:

1. Основы
  * **Подписка**: подписка, в которой необходимо развернуть шаблон.
  * **Группа ресурсов**: группа ресурсов, в которой требуется развернуть шаблон. Можно создать новую группу ресурсов или выбрать существующую в выбранной подписке.
  * **Расположение**: расположение, в котором необходимо развернуть шаблон. Если вы выбрали существующую группу ресурсов, используется расположение этой группы ресурсов.
1. Параметры
  * **Sap System Id** (Идентификатор системы SAP). Идентификатор системы SAP.
  * **Os Type** (Тип ОС). Тип операционной системы, которую необходимо развернуть (Windows или Linux).
  * **Sap System Size** (Размер системы SAP). Размер системы SAP.
    * Количество систем SAP, которые будет содержать система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям SAP или к системному интегратору SAP.
  * **Storage Type** (Тип хранилища). Тип хранилища, которое следует использовать (только для двухуровневого шаблона). 
    * Для систем большого размера настоятельно рекомендуется использовать хранилище класса Premium. Дополнительные сведения о различных типах хранилищ см. в разделе
      * [Использование хранилища SSD класса Premium Azure для экземпляра СУБД SAP][2367194] 
      * [Служба хранилища Microsoft Azure][dbms-guide-2.3] [руководство по СУБД][dbms-guide]
      * [Хранилище Premium: высокопроизводительное хранилище для рабочих нагрузок виртуальной машины Azure][storage-premium-storage-preview-portal]
      * [Введение в службу хранилища Microsoft Azure][storage-introduction]
  * **Os Disk Vhd Uri** (Универсальный код ресурса (URI) VHD диска ОС). Универсальный код ресурса (URI) частного диска ОС, например https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd.
  * **New Or Existing Subnet** (Новая или существующая подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите ее.
  * **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Выберите подсеть виртуальной сети VPN или Express Route, чтобы подключить виртуальную машину к локальной сети. Идентификатор обычно выглядит следующим образом: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

1. Условия использования  
    Прочтите и примите юридические условия.

После ввода всех параметров подтвердите их, нажав кнопку "Purchase" (Купить).

#### <a name="install-vm-agent"></a>Установка агента виртуальной машины
Если требуется использовать приведенные выше шаблоны, на диск ОС должен быть установлен агент виртуальной машины. В противном случае подготовка завершится с ошибкой. Скачайте и установите агент виртуальной машины на виртуальную машину, как описано в разделе [Скачивание, установка и включение агента виртуальной машины Azure][deployment-guide-4.4] в этом документе.

Если приведенные выше шаблоны не используются, агент виртуальной машины можно также установить позже.

#### <a name="join-domain-windows-only"></a>Присоединение к домену (только для Windows)
Если развертывание в Azure подключено к локальному экземпляру AD или к локальной DNS с помощью подключения типа "сеть — сеть" Azure или канала Express Route (также называется распределенным подключением в [руководстве по планированию и реализации][planning-guide]), то предполагается, что виртуальная машина присоединяется к локальному домену. Рекомендации к этому шагу описаны в разделе [Присоединение виртуальной машины к локальному домену (только для Windows)][deployment-guide-4.3] в этом документе.

#### <a name="configure-proxy-settings"></a>Настройка параметров прокси-сервера
В зависимости от конфигурации локальной сети на виртуальной машине может потребоваться настроить прокси-сервер, если она подключена к локальной сети через VPN или Express Route. В противном случае виртуальная машина не сможет получить доступ к Интернету и скачать необходимые расширения или выполнить сбор данных мониторинга. Ознакомьтесь с разделом [Настройка прокси-сервера][deployment-guide-configure-proxy] в этом документе.

#### <a name="configure-monitoring"></a>Настройка мониторинга
Чтобы обеспечить совместимость среды с SAP, настройте расширение для расширенного мониторинга Azure для SAP, как описано в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5] в этом документе.

Проверьте предварительные требования для мониторинга SAP относительно необходимых минимальных версий SAP Kernel и агента SAP Host Agent в ресурсах, указанных в разделе [Ресурсы SAP][deployment-guide-2.2] в этом документе.

#### <a name="monitoring-check"></a>Проверка мониторинга
Проверьте, работает ли мониторинг, как описано в разделе [Проверки и устранение неполадок при комплексной настройке мониторинга для SAP в Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Сценарий 4. Обновление конфигурации мониторинга для SAP
В следующих случаях необходимо обновить конфигурацию мониторинга SAP.

* Совмещенная группа, использующая продукты Майкрософт и SAP, расширила возможности мониторинга и запрашивает увеличение или уменьшение числа счетчиков. 
* Корпорация Майкрософт выпустила новую версию базовой инфраструктуры Azure, предоставляющую данные мониторинга, и расширение для расширенного мониторинга Azure для SAP нужно адаптировать к этим изменениям.
* Добавлены или удалены виртуальные жесткие диски, подключенные к виртуальной машине Azure. В этом случае необходимо обновить коллекцию данных, связанных с хранилищем. Если конфигурация изменяется в результате добавления или удаления конечных точек или назначения IP-адресов виртуальной машине, это не повлияет на конфигурацию мониторинга.
* Изменился размер виртуальной машины Azure, например с A5 на любой другой размер.
* В виртуальную машину Azure добавлены новые сетевые интерфейсы.

Чтобы обновить конфигурацию мониторинга, выполните следующее:

* Обновите инфраструктуру мониторинга, выполнив действия, указанные в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5] в этом документе. Если повторно запустить сценарий, описанный в этом разделе, обнаружится, что конфигурация мониторинга развернута. После этого конфигурация мониторинга будет изменена соответствующим образом. 

## <a name="detailed-single-deployment-steps"></a>Подробное описание одного развертывания
### <a name="a-name604bcec2-8b6e-48d2-a944-61b0f5dee2f7adeploying-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Развертывание командлетов Azure PowerShell
* Перейдите на страницу <https://azure.microsoft.com/downloads/>.
* В разделе "Программы командной строки" есть подраздел "PowerShell". Перейдите по ссылке "Установка Windows".
* Появится всплывающее окно Диспетчера загрузки Майкрософт с элементом строки, который заканчивается на .exe. Выберите параметр "Запустить".
* Появится всплывающее окно с запросом на запуск установщика веб-платформы Майкрософт. Нажмите кнопку "Да".
* Появится экран следующего вида:

![Экран установки командлетов Azure PowerShell][deployment-guide-figure-500]
<a name="figure-5"></a>

* Нажмите кнопку "Установить" и примите условия лицензионного соглашения.

Регулярно проверяйте обновление командлетов PowerShell. Обычно обновление происходит раз в месяц. Для этого проще всего выполнить указания, описанные выше, до экрана установки на [этом][deployment-guide-figure-5] рисунке. На этом снимке экрана показана дата и текущий номер выпуска командлетов. Если иное не указано в примечаниях к SAP [1928533] или [2015553], то рекомендуется работать с последней версией командлетов Azure PowerShell.

Установленную версию командлетов Azure на настольном компьютере или ноутбуке можно проверить с помощью команды PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

Результат должен выглядеть, как показано на [этом][deployment-guide-figure-6] рисунке.

![Результат проверки версии с помощью командлета Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Если версия командлетов Azure, установленных на настольном компьютере или ноутбуке, совпадает с текущей, то первый экран, который отобразится после запуска установщика веб-платформы Майкрософт, будет выглядеть немного иначе, чем на снимке экрана, показанном на [этом][deployment-guide-figure-5] рисунке.

Обратите внимание на красный прямоугольник на [рисунке][deployment-guide-figure-7] ниже.

![Экран установки командлетов Azure PowerShell, на котором указано, что установлена последняя версия командлетов Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

Если экран выглядит как на снимке экрана [выше][deployment-guide-figure-7], на котором указано, что установлена последняя версия командлетов Azure, то продолжать установку не нужно. В этом случае можно остановить установку на этом этапе.

### <a name="a-name1ded9453-1330-442a-86ea-e0fd8ae8cab3adeploying-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Развертывание интерфейса командной строки Azure
* Перейдите на страницу <https://azure.microsoft.com/downloads/>.
* В разделе "Программы командной строки" есть раздел "Интерфейс командной строки Azure". Перейдите по ссылке "Установка" для вашей операционной системы.

Регулярно проверяйте обновление интерфейса командной строки Azure. Обычно обновление происходит раз в месяц. Для этого проще всего выполнить указания, описанные выше.

Установленную версию интерфейса командной строки Azure на настольном компьютере или ноутбуке можно проверить с помощью следующей команды:

```
azure --version
```

Результат должен выглядеть, как показано на [этом][deployment-guide-figure-azure-cli-version] рисунке.

![Результат проверки версии с помощью интерфейса командой строки Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="a-name31d9ecd6-b136-4c73-b61e-da4a29bbc9ccajoin-vm-into-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Присоединение виртуальной машины к локальному домену (только для Windows)
При развертывании виртуальной машины SAP в распределенной среде, где локальный экземпляр AD и DNS расширяются в Azure, предполагается, что виртуальные машины присоединяются к локальному домену. Подробное описание присоединения виртуальной машины к локальному домену и установка дополнительного программного обеспечения, необходимого для присоединения к локальному домену, зависит от клиента. Обычно при присоединении виртуальной машины к локальному домену устанавливается дополнительное программное обеспечение, например программное обеспечение для защиты от вредоносных программ, или разные агенты программного обеспечения для резервного копирования или мониторинга.

Кроме того, если параметры прокси-сервера Интернета задаются принудительно при присоединении к домену, убедитесь, что для учетной записи Local System Windows (S-1-5-18) в гостевой виртуальной машине также заданы эти параметры. Проще всего принудительно применить к прокси-серверу групповую политику домена, которая применяется к системам в домене.

### <a name="a-namec7cbb0dc-52a4-49db-8e03-83e7edc2927dadownload-install-and-enable-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Скачивание, установка и включение агента виртуальной машины Azure
Для виртуальных машин, развернутых из специализированного образа ОС (например, не обработанного Sysprep для Windows), необходимо вручную скачать, установить и включить агент виртуальной машины. 

Для виртуальных машин, развернутых из образа Azure Marketplace, этот шаг не является обязательным, так как эти образы уже содержат агент виртуальной машины.

#### <a name="a-nameb2db5c9a-a076-42c6-9835-16945868e866awindows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
* Скачайте агент виртуальной машины Azure.
  * Скачайте пакет установщика агента виртуальной машины Azure, перейдя по адресу <https://go.microsoft.com/fwlink/?LinkId=394789>.
  * Сохраните пакет MSI агента виртуальной машины локально на ноутбуке или сервере.
* Установите агент виртуальной машины Azure.
  * Подключение к развернутой виртуальной машине Azure с помощью удаленного рабочего стола (RDP)
  * Откройте окно проводника на виртуальной машине и выберите целевой каталог для MSI-файла агента виртуальной машины.
  * Перетащите файл MSI установщика агента виртуальной машины Azure с локального ноутбука или сервера в целевой каталог агента виртуальной машины на виртуальной машине.
  * Дважды щелкните MSI-файл в виртуальной машине.
  * Убедитесь, что для виртуальной машины, присоединенной к локальным доменам, к учетной записи Local System Windows (S-1-5-18) применяются окончательные параметры прокси-сервера Интернета, описанные в разделе [Настройка прокси-сервера][deployment-guide-configure-proxy]. Агент виртуальной машины будет выполняться в этом контексте и должен иметь возможность подключиться к Azure.

Для обновления агента виртуальной машины Azure не требуется вмешательство пользователя. Агент виртуальной машины обновляется автоматически и не требует перезагрузки виртуальной машины.

#### <a name="a-name6889ff12-eaaf-4f3c-97e1-7c9edc7f7542alinux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Установите агент виртуальной машины для Linux с помощью следующей команды:

* **SLES**

```
sudo zypper install WALinuxAgent
```
* **RHEL**

```
sudo yum install WALinuxAgent
```

Выполните инструкции в [этой статье][virtual-machines-linux-update-agent], чтобы обновить агент Linux для Azure, если он уже установлен. 

### <a name="a-namebaccae00-6f79-4307-ade4-40292ce4e02daconfigure-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Настройка прокси-сервера
Процедура настройки прокси-сервера отличается для Windows и Linux.

#### <a name="windows"></a>Windows
Эти параметры также должны быть допустимыми для учетной записи Local System, чтобы получать доступ к Интернету. Если параметры прокси-сервера не заданы с помощью групповой политики, их можно настроить для учетной записи LocalSystem. Выполните следующие действия.

1. Откройте файл gpedit.msc.
2. Выберите "Конфигурация компьютера" > "Административные шаблоны" > "Компоненты Windows" > "Internet Explorer". Убедитесь, что параметр политики "Задать параметры прокси для компьютера (а не для пользователя)" включен.
3. Откройте панель управления и последовательно выберите "Сеть и Интернет -> Свойства браузера".
4. Откройте вкладку "Подключения" и щелкните "Параметры сети".
5. Снимите флажок "Автоматическое определение параметров".
6. Установите флажок "Использовать прокси-сервер для локальной сети" и введите адрес и порт прокси-сервера.

#### <a name="linux"></a>Linux
Настройте правильный прокси-сервер в файле конфигурации гостевого агента Microsoft Azure, расположенного в каталоге /etc/waagent.conf. Нужно задать следующие параметры:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Изменив конфигурацию, перезапустите агент с помощью следующей команды:

```
sudo service waagent restart
```

Параметры прокси-сервера в файле /etc/waagent.conf также применяются к требуемым расширениям виртуальной машины. Если нужно использовать репозитории Azure, убедитесь, что входящий трафик для них не проходит через локальную интрасеть. Если созданы определяемые пользователем маршруты для включения принудительного туннелирования, добавьте маршрут, который направляет трафик по адресу 104.45.17.148 непосредственно к Интернету, а не через подключение типа "сеть — сеть".

* **SLES.** Необходимо также добавить маршруты для IP-адресов, указанных в файле /etc/regionserverclnt.cfg. Пример показан на приведенном ниже снимке экрана. 
* **RHEL.** Необходимо также добавить маршруты для IP-адресов узлов, перечисленных в /etc/yum.repos.d/rhui-load-balancers. Пример показан на приведенном ниже снимке экрана.

Дополнительные сведения об определяемых пользователем маршрутах см. в [этой статье][virtual-networks-udr-overview].

![Принудительное туннелирование][deployment-guide-figure-50]

### <a name="a-named98edcd3-f2a1-49f7-b26a-07448ceb60caaconfigure-azure-enhanced-monitoring-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Расширенный мониторинг Azure для SAP: настройка расширения
После подготовки виртуальной машины, как описано в разделе [Сценарии развертывания виртуальных машин для SAP в Microsoft Azure][deployment-guide-3], агент виртуальной машины Azure устанавливается на виртуальной машине. На следующем важном шаге необходимо развернуть расширение для расширенного мониторинга Azure для SAP, которое доступно в репозитории расширений Azure в глобальных центрах обработки данных Microsoft Azure. Дополнительные сведения см. в [руководстве по планированию и реализации][planning-guide-9.1]. 

Чтобы установить и настроить расширение для расширенного мониторинга Azure для SAP, можно использовать Azure PowerShell или интерфейс командной строки Azure. См. раздел [Azure PowerShell][deployment-guide-4.5.1], если нужно установить расширение на виртуальную машину Windows или Linux с помощью компьютера Windows. Сведения об установке расширения на виртуальной машине Linux с использованием настольного компьютера Linux см. в разделе [Интерфейс командной строки Azure][deployment-guide-4.5.2].

#### <a name="a-name987cf279-d713-4b4c-8143-6b11589bb9d4aazure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell для виртуальных машин Windows и Linux
Чтобы установить расширение для расширенного мониторинга Azure для SAP, сделайте следующее:

* Убедитесь, что у вас установлена последняя версия командлета Microsoft Azure PowerShell. См. раздел [Развертывание командлетов Azure PowerShell][deployment-guide-4.1] в этом документе.  
* Выполните приведенный ниже командлет PowerShell.
    Чтобы получить список доступных сред, выполните командлет Get-AzureRmEnvironment. Если нужно использовать общедоступный экземпляр Azure, выберите среду AzureCloud. Для Azure в Китае выберите AzureChinaCloud.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

После указания данных учетной записи и виртуальной машины Azure сценарий развернет требуемые расширения и включит необходимые компоненты. Это может занять несколько минут.
Дополнительные сведения о Set-AzureRmVMAEMExtension см. в [этой статье MSDN][msdn-set-azurermvmaemextension].

![Итоговый экран успешного выполнения командлета Azure Set-AzureRmVMAEMExtension для SAP][deployment-guide-figure-900]

При выполнении сценария Set-AzureRmVMAEMExtension будут произведены все действия, необходимые для настройки функции мониторинга узла для SAP. 

Результат выполнения сценария выглядит следующим образом:

* Подтверждение настройки конфигурации мониторинга для базового виртуального жесткого диска (содержащего ОС) и всех дополнительных виртуальных жестких дисков, подключенных к виртуальной машине.
* В следующих двух сообщениях подтверждается настройка метрик хранилища для определенной учетной записи хранения. 
* В одной из строк вывода указывается состояние фактического обновления конфигурации мониторинга.
* В другой подтверждается развертывание или обновление конфигурации.
* Последняя строка вывода — информационная. В ней содержатся сведения о возможности тестирования конфигурации мониторинга.
* Чтобы проверить, успешно ли выполнены все этапы расширенного мониторинга Azure и содержит ли инфраструктура Azure необходимые данные, продолжите проверку готовности расширения для расширенного мониторинга Azure для SAP, как описано в разделе [Расширенный мониторинг Azure для SAP: проверка готовности][deployment-guide-5.1] в этом документе.
* Чтобы продолжить, подождите 15–30 минут, в течение которых система диагностики Azure соберет соответствующие данные.

#### <a name="a-name408f3779-f422-4413-82f8-c57a23b4fc2faazure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Интерфейс командной строки Azure для виртуальных машин Linux
Чтобы установить расширение для расширенного мониторинга Azure для SAP, сделайте следующее:

1. Установите Azure CLI, как описано в [этой][azure-cli] статье.
2. Войдите со своей учетной записью Azure.
   
    ```
    azure login
    ```
3. Переключитесь в режим Azure Resource Manager.
   
    ```
    azure config mode arm
    ```
4. Включите расширенный мониторинг Azure.
   
    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
5. Проверьте, включен ли расширенный мониторинг Azure на виртуальной машине Linux в Azure. Проверьте, существует ли файл /var/lib/AzureEnhancedMonitor/PerfCounters. Если он существует, отобразите информацию, собираемую AEM, с помощью приведенной ниже команды.
   
    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    Результат должен будет выглядеть следующим образом.
   
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="a-name564adb4f-5c95-4041-9616-6635e83a810bachecks-and-troubleshooting-for-end-to-end-monitoring-setup-for-sap-on-azure"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Проверки и устранение неполадок при комплексной настройке мониторинга для SAP в Azure
Развернув виртуальную машину Azure и настроив соответствующую инфраструктуру мониторинга Azure, проверьте, работают ли все компоненты расширенного мониторинга Azure должным образом. 

Выполните проверку готовности расширения для расширенного мониторинга Azure для SAP, как описано в разделе [Расширенный мониторинг Azure для SAP: проверка готовности][deployment-guide-5.1]. Если проверка выполнена успешно и получены все соответствующие счетчики производительности, мониторинг Azure настроен. В этом случае продолжайте установку агента SAP Host Agent, как описано в примечаниях к SAP, указанных в разделе [Ресурсы SAP][deployment-guide-2.2] в этом документе. Если в результате проверки готовности обнаружены отсутствующие счетчики, выполните проверку работоспособности инфраструктуры мониторинга Azure, как описано в разделе [Конфигурация инфраструктуры мониторинга Azure: проверка работоспособности][deployment-guide-5.2]. В случае возникновения проблем с конфигурацией мониторинга Azure см. раздел [Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок][deployment-guide-5.3], чтобы получить дополнительные сведения об устранении неполадок.

### <a name="a-namebb61ce92-8c5c-461f-8c53-39f5e5ed91f2areadiness-check-for-azure-enhanced-monitoring-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Расширенный мониторинг Azure для SAP: проверка готовности
Эта проверка позволяет обеспечить, что метрики, которые будут отображаться в приложении SAP, полностью предоставляются базовой инфраструктурой мониторинга Azure. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Выполнение проверки готовности на виртуальной машине Windows
Чтобы выполнить проверку готовности, войдите в виртуальную машину Azure (учетная запись администратора не требуется) и сделайте следующее.

* Откройте командную строку Windows и перейдите в папку установки расширения мониторинга Azure для SAP: C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`>\drop.

Часть версии в приведенном выше пути к расширению мониторинга может отличаться. Если в папке установки отображается несколько папок версий расширения мониторинга, проверьте конфигурацию службы Windows AzureEnhancedMonitoring и перейдите в папку, указанную в поле "Путь к исполняемому объекту".

![Свойства службы, в которой выполняется расширение для расширенного мониторинга Azure для SAP][deployment-guide-figure-1000]

* Выполните файл azperflib.exe в командном окне без параметров.

> [!NOTE]
> Файл azperflib.exe выполняется в цикле и обновляет собираемые счетчики каждые 60 секунд. Чтобы завершить цикл, закройте командное окно.
> 
> 

Если расширение для расширенного мониторинга Azure не установлено или не запущена служба AzureEnhancedMonitoring, расширение настроено неправильно. В этом случае изучите раздел [Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок][deployment-guide-5.3], в котором содержатся подробные указания по повторному развертыванию расширения.

##### <a name="check-the-output-of-azperflibexe"></a>Проверка результата выполнения azperflib.exe
Результат выполнения файла azperflib.exe содержит все заполненные счетчики производительности Azure для SAP. В нижней части списка собранных счетчиков указана сводка и индикатор работоспособности, который указывает состояние мониторинга Azure. 

![Результат проверки работоспособности с использованием файла azperflib.exe, в котором указано, что ошибок нет][deployment-guide-figure-1100]
<a name="figure-11"></a>

Проверьте результат, возвращенный для общего числа счетчиков (соответствующая строка отображается пустой), а также для проверки работоспособности, как показано на рисунке [выше][deployment-guide-figure-11].

Полученные значения можно интерпретировать следующим образом:

| Итоговые значения azperflib.exe | Состояние работоспособности мониторинга Azure |
| --- | --- |
| **Вызовы API — недоступны** | Недоступные счетчики могут быть неприменимыми к конфигурации виртуальной машины или вызывать ошибки. Ознакомьтесь с состоянием работоспособности. | 
| **Всего счетчиков: пусто** |Значения для следующих двух счетчиков службы хранилища Azure могут отсутствовать:  <ul><li>задержка операций чтения в хранилище на сервере (мс);</li><li>задержка операций чтения в хранилище E2E (мс).</li></ul>Все остальные счетчики должны содержать значения. |
| **Проверка работоспособности** |Отображается только при возврате состояния "ОК". |
| **Диагностика** |Подробные сведения о состоянии работоспособности |

Если значение состояния работоспособности не "ОК", следуйте указаниям по проверке работоспособности для конфигурации инфраструктуры мониторинга Azure, как описано в разделе [Конфигурация инфраструктуры мониторинга Azure: проверка работоспособности][deployment-guide-5.2] ниже.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Выполнение проверки готовности на виртуальной машине Linux
Чтобы выполнить проверку готовности, подключитесь к виртуальной машине Azure по протоколу SSH и выполните следующее:

* Проверьте выходные данные расширения для расширенного мониторинга Azure.
  * Выполните /var/lib/AzureEnhancedMonitor/PerfCounters.
    * Будет выведен список счетчиков производительности. Файл не должен быть пустым.
  * Выполните /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error.
    * Будет возвращена одна строка, где для параметра error задано значение none, например, 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;.
  * Выполните /var/lib/AzureEnhancedMonitor/LatestErrorRecord.
    * Строка должна быть пустой или отсутствовать.
* Если первая проверка не прошла успешно, выполните следующие дополнительные проверки:
  * Убедитесь в том, что каталог waagent установлен и запущен.
    * Выполните sudo ls -al /var/lib/waagent/.
      * Должно отобразиться содержимое каталога waagent.
    * Выполните ps -ax | grep waagent.
      * Должна отобразиться одна запись, аналогичная python /usr/sbin/waagent -daemon.
  * Убедитесь, что диагностическое расширение Linux установлено и запущено.
    * Выполните sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
      * Должно отобразиться содержимое каталога диагностического расширения Linux.
    * Выполните ps -ax | grep diagnostic.
      * Должна отобразиться одна запись, аналогичная python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon.
  * Убедитесь, что расширение мониторинга Azure установлено и запущено.
    * Выполните sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
      * Должно отобразиться содержимое каталога расширения для расширенного мониторинга Azure.
    * Выполните ps -ax | grep AzureEnhanced.
      * Должна отобразиться одна запись, аналогичная python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon.
* Установите агент SAP Host Agent, как описано в примечании к SAP [1031096] , и проверьте выходные данные saposcol.
  * Выполните /usr/sap/hostctrl/exe/saposcol -d.
  * Выполните dump ccm.
  * Проверьте, задано ли значение true для метрики Virtualization_Configuration\Enhanced Monitoring Access.
* Если сервер приложений ABAP SAP NetWeaver уже установлен, откройте транзакцию ST06 и проверьте, включен ли расширенный мониторинг.

При сбое любой из проверок выше см. раздел [Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок][deployment-guide-5.3], в котором содержатся подробные указания по повторному развертыванию расширения.

### <a name="a-namee2d592ff-b4ea-4a53-a91a-e5521edb6cd1ahealth-check-for-azure-monitoring-infrastructure-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Конфигурация инфраструктуры мониторинга Azure: проверка работоспособности
Если некоторые данные мониторинга не предоставляются именно так, как указано в тесте, описанном в разделе [Расширенный мониторинг Azure для SAP: проверка готовности][deployment-guide-5.1] выше, то выполните командлет Test-AzureRmVMAEMExtension, чтобы проверить, настроены ли текущая конфигурация инфраструктуры мониторинга Azure и расширение мониторинга для SAP должным образом.

Чтобы проверить конфигурацию мониторинга, выполните действия в следующей последовательности:

* Убедитесь, что установлена последняя версия командлета Microsoft Azure PowerShell, как описано в разделе [Развертывание командлетов Azure PowerShell][deployment-guide-4.1] в этом документе.
* Выполните приведенный ниже командлет PowerShell. Чтобы получить список доступных сред, выполните командлет Get-AzureRmEnvironment. Если нужно использовать общедоступный экземпляр Azure, выберите среду AzureCloud. Для Azure в Китае выберите AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* После указания данных учетной записи и виртуальной машины Azure сценарий выполнит тестирование выбранной конфигурации виртуальной машины.

![Экран ввода командлета Azure Test-VMConfigForSAP_GUI для SAP][deployment-guide-figure-1200]

После ввода сведений об учетной записи и виртуальной машине Azure сценарий выполнит тестирование выбранной конфигурации виртуальной машины.

![Результат успешной проверки инфраструктуры мониторинга Azure для SAP][deployment-guide-figure-1300]

Убедитесь, что для каждой проверки установлено состояние "ОК". В противном случае выполните командлет обновления, как описано в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5] в этом документе. Подождите 15 минут и повторно выполните проверки, описанные в разделах [Расширенный мониторинг Azure для SAP: проверки готовности][deployment-guide-5.1] и [Конфигурация инфраструктуры мониторинга Azure: проверка работоспособности][deployment-guide-5.2]. Если при проверке по-прежнему возникают проблемы с некоторыми или со всеми счетчиками, см. раздел [Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок][deployment-guide-5.3].

### <a name="a-namefe25a7da-4e4e-4388-8907-8abc2d33cfd8afurther-troubleshooting-of-azure-monitoring-infrastructure-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок
#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Счетчики производительности Azure вообще не отображаются
Сбор показателей производительности в Azure выполняет служба Windows AzureEnhancedMonitoring. Если служба установлена неправильно или не запущена на виртуальной машине, сбор метрик производительности невозможен.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Каталог установки расширения для расширенного мониторинга Azure пустой
###### <a name="issue"></a>Проблема
Каталог установки C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`>\drop пустой.

###### <a name="solution"></a>Решение
Расширение не установлено. Проверьте, связано ли это с неполадками прокси-сервера (как описано выше). Может потребоваться перезагрузить компьютер и/или повторно выполнить сценарий конфигурации Set-AzureRmVMAEMExtension.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Служба для расширенного мониторинга Azure не существует
###### <a name="issue"></a>Проблема
Служба Windows AzureEnhancedMonitoring не существует. В результате выполнения azperlib.exe отображается ошибка, как показано на [рисунке ниже][deployment-guide-figure-14].

![Выполнение файла azperflib.exe, в результате которого указано, что служба расширения для расширенного мониторинга Azure для SAP не запущена][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Решение
Если служба не существует, как показано на [рисунке выше][deployment-guide-figure-14], то расширение мониторинга Azure для SAP установлено неправильно. Разверните расширение повторно, выполнив действия для сценария развертывания, описанные в разделе [Сценарии развертывания виртуальных машин для SAP в Microsoft Azure][deployment-guide-3]. 

Через час после повторного развертывания расширения проверьте, предоставляются ли счетчики производительности Azure в виртуальной машине Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Служба для расширенного мониторинга Azure существует, но не запускается
###### <a name="issue"></a>Проблема
Служба Windows AzureEnhancedMonitoring существует и включена, но не запускается. Дополнительные сведения см. в журнале событий приложений.

###### <a name="solution"></a>Решение
Недопустимая конфигурация. Включите расширение мониторинга Azure для SAP повторно, как описано в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Отсутствуют некоторые счетчики производительности Azure
Сбор показателей производительности в Azure выполняет служба Windows AzureEnhancedMonitoring, которая получает данные из нескольких источников. Некоторые данные конфигурации собираются локально, метрики производительности считываются из системы диагностики Azure, а счетчики хранилища используются из журналов на уровне подписки в хранилище.

Если после устранения неполадок с использованием примечания к SAP [1999351] проблемы не исчезли, повторно запустите сценарий конфигурации Set-AzureRmVMAEMExtension. Необходимо подождать час, так как счетчики аналитики или диагностики хранилищ не могут быть созданы сразу после включения. Если проблема не исчезла, откройте сообщение о поддержке клиентов SAP в компоненте BC-OP-NT-AZR для виртуальной машины Windows или BC-OP-LNX-AZR для виртуальной машины Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Счетчики производительности Azure вообще не отображаются
Сбор показателей производительности в Azure выполняет управляющая программа. Если она не запущена, сбор метрик производительности невозможен.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Каталог установки расширения для расширенного мониторинга Azure пустой
###### <a name="issue"></a>Проблема
Каталог /var/lib/waagent/ не содержит подкаталог для расширения для расширенного мониторинга Azure.

###### <a name="solution"></a>Решение
Расширение не установлено. Проверьте, связано ли это с неполадками прокси-сервера (как описано выше). Может потребоваться перезагрузить компьютер и/или повторно выполнить сценарий конфигурации Set-AzureRmVMAEMExtension.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Отсутствуют некоторые счетчики производительности Azure
Сбор показателей производительности в Azure выполняет управляющая программа, которая получает данные из нескольких источников. Некоторые данные конфигурации собираются локально, метрики производительности считываются из системы диагностики Azure, а счетчики хранилища используются из журналов на уровне подписки в хранилище.

Полный и актуальный список известных проблем см. в примечании SAP [1999351]. В нем содержатся дополнительные сведения об устранении неполадок расширенного мониторинга Azure для SAP.

Если при устранении неполадок с использованием примечания SAP [1999351] проблема не исчезла, повторно запустите сценарий конфигурации Set-AzureRmVMAEMExtension, как описано в разделе [Расширенный мониторинг Azure для SAP: настройка расширения][deployment-guide-4.5]. Необходимо подождать час, так как счетчики аналитики или диагностики хранилищ не могут быть созданы сразу после включения. Если проблема не исчезла, откройте сообщение о поддержке клиентов SAP в компоненте BC-OP-NT-AZR для виртуальной машины Windows или BC-OP-LNX-AZR для виртуальной машины Linux.




<!--HONumber=Nov16_HO5-->


