---
title: Развертывание виртуальных машин Azure для SAP NetWeaver | Документация Майкрософт
description: Сведения о развертывании программного обеспечения SAP на виртуальных машинах Linux в Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.openlocfilehash: cb402f51ca20551bf34140322f3f259470a637ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656403"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Развертывание виртуальных машин Azure для SAP NetWeaver
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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Развертывание СУБД виртуальных машин Azure для SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Кэширование для виртуальных машин и виртуальных жестких дисков)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Программный RAID-массив)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Служба хранилища Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Структура развертывания реляционной СУБД)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Высокая доступность и аварийное восстановление с использованием виртуальных машин Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 4 (CU4) и последующие выпуски)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3) и предыдущие выпуски)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Использование образов SQL Server из Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Общие сведения об SQL Server для SAP в Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Особенности реляционных СУБД SQL Server)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Конфигурация хранилища)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Резервное копирование и восстановление)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Рекомендации по ускорению резервного копирования и восстановления)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Прочее)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (Развертывание виртуальных машин для SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Ресурсы SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Развертывание виртуальной машины с помощью пользовательского образа)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Сценарий 1. Развертывание виртуальной машины для SAP из Azure Marketplace)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Сценарий 2. Развертывание виртуальной машины для SAP с помощью пользовательского образа)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Сценарий 3. Перемещение виртуальной машины из локальной среды с помощью специализированного виртуального жесткого диска Azure с SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Сценарии развертывания виртуальных машин для SAP в Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Развертывание командлетов Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Скачивание и импорт соответствующих командлетов PowerShell для SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Присоединение виртуальной машины к локальному домену (только для Windows))
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Скачивание, установка и включение агента виртуальной машины Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Настройка расширения расширенного мониторинга Azure для SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Проверка готовности расширения расширенного мониторинга Azure для SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Проверка работоспособности для инфраструктуры мониторинга Azure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Устранение неполадок, связанных с мониторингом Azure для SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Настройка мониторинга)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Настройка прокси-сервера)
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Проверки и устранение неполадок при настройке комплексного мониторинга)

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md (SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Ресурсы)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Высокая доступность и аварийное восстановление для SAP NetWeaver на виртуальных машинах Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Высокая доступность серверов приложений SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Использование автозапуска для экземпляров SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Только облако. Развертывание виртуальных машин в Azure без зависимостей в локальной сети клиента)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Распределенная сеть. Развертывание одной или нескольких виртуальных машин SAP в Azure с полной интеграцией с локальной сетью)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Регионы Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Домены сбоя)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Домены обновления)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Группы доступности Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Концепция виртуальных машин Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Хранилище Azure класса Premium)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Перемещение виртуальной машины из локальной среды в Azure с помощью специализированного диска)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Развертывание виртуальной машины с помощью пользовательского образа)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Подготовка виртуальной машины к перемещению из локальной среды в Azure с помощью специализированного диска)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Подготовка виртуальной машины к развертыванию с помощью пользовательского образа для SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Подготовка виртуальных машин с SAP для Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Разница между диском Azure и образом Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Передача VHD из локальной среды в Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Копирование дисков между учетными записями хранения Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Структура виртуальной машины и VHD для развертываний SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Настройка автоподключения для подключенных дисков)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Обучающий сценарий с демонстрацией одной виртуальной машины с SAP NetWeaver)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Концепция полностью облачного развертывания экземпляров SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Решение мониторинга Azure для SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Хранилище Azure класса Premium)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Управляемые диски)
[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Сеть Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Хранилище. Служба хранилища Microsoft Azure и диски данных)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Матрица доступности продуктов SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Развертывание виртуальных машин и управление ими с помощью шаблонов Azure Resource Manager и интерфейса командной строки Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Управление виртуальными машинами с помощью Azure Resource Manager и PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Виртуальные машины Azure позволяют организациям быстро получать вычислительные ресурсы и ресурсы хранилища без длительных циклов закупки. С помощью этого решения можно развернуть в Azure классические приложения, например приложения на основе SAP NetWeaver, и повысить уровень их надежности и доступности, не привлекая дополнительные локальные ресурсы. Виртуальные машины Azure поддерживают распределенные подключения. Это позволяет интегрировать их в локальные домены, частные облака и системный ландшафт SAP вашей организации.

В этой статье описаны действия, необходимые для развертывания приложений SAP на виртуальных машинах Azure, а также приведены сведения о дополнительных вариантах развертывания и устранении неполадок. Эта статья основана на сведениях, представленных в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide]. Она также дополняет документацию по установке SAP и примечания к SAP, которые являются основными ресурсами по установке и развертыванию ПО SAP.

## <a name="prerequisites"></a>предварительным требованиям
Настройка виртуальной машины Azure перед развертыванием ПО SAP состоит из нескольких этапов и предполагает наличие определенных ресурсов. Перед началом убедитесь, что вы выполнили все предварительные требования для установки программного обеспечения SAP на виртуальных машинах Azure.

### <a name="local-computer"></a>Локальный компьютер
Чтобы управлять виртуальными машинами Windows или Linux, можно использовать скрипт PowerShell и портал Azure. В обоих случаях вам потребуется локальный компьютер под управлением Windows 7 или более поздней версии. Если вы хотите управлять только виртуальными машинами Linux на компьютере с Linux, для этой задачи можно использовать Azure CLI.

### <a name="internet-connection"></a>Подключение к Интернету
Чтобы скачать и запустить средства и скрипты, необходимые для развертывания ПО SAP, требуется подключение к Интернету. Кроме того, виртуальной машине Azure, на которой выполняется расширение для расширенного мониторинга Azure для SAP, также требуется доступ к Интернету. Если эта виртуальная машина Azure входит в состав виртуальной сети Azure или локального домена, настройте соответствующие параметры прокси, как описано в разделе [Настройка прокси][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Подписка на Microsoft Azure
Вам потребуется активная учетная запись Azure.

### <a name="topology-and-networking"></a>Топология и сеть
Необходимо определить топологию и архитектуру развертывания SAP в Azure. Определите следующие компоненты:

* учетные записи хранения Azure;
* виртуальная сеть, в которой будет развернута система SAP;
* группа ресурсов, в которой будет развернута система SAP;
* регион Azure, в котором будет развернута система SAP;
* конфигурация SAP (двух- или трехуровневая);
* Размеры виртуальных машин и число дополнительных дисков данных, которые будут к ним подключены
* конфигурация системы исправлений и транспортировки SAP.

Перед началом развертывания программного обеспечения SAP создайте и настройте учетные записи хранения (при необходимости) и виртуальные сети Azure. Дополнительные сведения о создании и настройке этих ресурсов см. в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide].

### <a name="sap-sizing"></a>Определение размера системы SAP
При определении размера системы SAP учитывайте следующее:

* планируемая рабочая нагрузка SAP определяется, например, с помощью средства SAP Quick Sizer и метрики производительности SAP Application Performance Standard (SAPS);
* потребление ресурсов ЦП и памяти системой SAP;
* требуемое число операций ввода-вывода в секунду;
* требуемая пропускная способность сети при дальнейшем обмене данными между разными виртуальными машинами в Azure;
* требуемая пропускная способность сети между локальными ресурсами и системами SAP, развернутыми в Azure.

### <a name="resource-groups"></a>Группы ресурсов
Группы ресурсов в Azure Resource Manager можно использовать для управления всеми ресурсами приложения в подписке Azure. Дополнительные сведения см. в [обзоре Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Ресурсы

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Ресурсы SAP
Во время конфигурации развертывания ПО SAP необходимы следующие ресурсы SAP:

* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, поддерживаемых для развертывания ПО SAP;
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.

* примечание к SAP [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* примечание к SAP [2178632], содержащее подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [1409604], содержащее сведения о необходимой версии агента SAP Host Agent для Windows в Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1984787], содержащее общие сведения о SUSE Linux Enterprise Server 12;
* примечание к SAP [2002167], содержащее общие сведения о Red Hat Enterprise Linux 7.x;
* примечание к SAP [2069760], содержащее общие сведения об Oracle Linux 7.x;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* примечание к SAP [1597355], содержащее общие сведения об области буфера для Linux;
* на [странице SCN SAP в Azure](https://wiki.scn.sap.com/wiki/x/Pia7Gg) есть новости и коллекция полезных ресурсов;
* [вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), содержащий все необходимые примечания к SAP для Linux;
* командлеты PowerShell для SAP, которые являются частью [Azure PowerShell][azure-ps];
* команды интерфейса командной строки Azure для SAP, который является частью [Azure CLI][azure-cli];

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Ресурсы Windows
Дополнительные сведения о развертывании SAP в Azure см. в следующих статьях:

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание виртуальных машин Azure для SAP NetWeaver (эта статья)][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Сценарии развертывания ПО SAP на виртуальных машинах Azure
Виртуальные машины и связанные диски можно развернуть в Azure несколькими способами. Важно понимать различие между ними, так как подготовка виртуальных машин к развертыванию может отличаться в зависимости от выбранного способа развертывания.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Сценарий 1. Развертывание виртуальной машины для SAP из Azure Marketplace
Вы можете использовать для развертывания виртуальной машины образ из Azure Marketplace, предоставленный корпорацией Майкрософт или сторонним поставщиком. В каталоге Marketplace доступны несколько стандартных образов ОС Windows Server и различные дистрибутивы Linux. Вы также можете развернуть образ, содержащий номера SKU СУБД, например Microsoft SQL Server. Дополнительные сведения об использовании образов с номерами SKU СУБД см. в статье [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide].

На блок-схеме ниже приведена последовательность действий по развертыванию виртуальной машины из Azure Marketplace для SAP.

![Блок-схема развертывания виртуальной машины для систем SAP с помощью образа виртуальной машины из Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Создание виртуальной машины с помощью портала Azure
Проще всего виртуальную машину можно создать с помощью образа из Azure Marketplace на портале Azure.

1.  Перейдите на сайт <https://portal.azure.com/#create/hub>.  или щелкните **+ Создать** в меню портала Azure.
2.  Выберите **Вычисление**, а затем — тип развертываемой операционной системы. Например, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) или Oracle Linux 7.2. В представлении списка по умолчанию не отображаются все поддерживаемые операционные системы. Выберите **Показать все**, чтобы просмотреть полный список. Дополнительные сведения о поддерживаемых операционных системах для развертывания ПО SAP см. в примечании к SAP [1928533].
3.  На следующей странице ознакомьтесь с условиями использования.
4.  В раскрывающемся списке **Выберите модель развертывания** выберите **Resource Manager**.
5.  Нажмите кнопку **Создать**.

Мастер поможет настроить необходимые параметры для создания виртуальной машины, а также все необходимые ресурсы, например сетевые интерфейсы или учетные записи хранения. Ниже приведены некоторые из этих параметров.

1. **Основные сведения**:
 * **Имя.** Имя ресурса, например виртуальной машины.
 * **Тип диска виртуальной машины.** Выберите тип диска ОС. Если вы хотите использовать хранилище класса Premium для дисков данных, рекомендуется также использовать его для диска ОС.
 * **Имя пользователя и пароль** или **открытый ключ SSH**. Введите имя и пароль учетной записи пользователя, созданной во время подготовки. Для виртуальной машины Linux можно ввести открытый ключ SSH, используемый для входа на компьютер.
 * **Подписка.** Выберите подписку, которую нужно использовать для подготовки новой виртуальной машины.
 * **Группа ресурсов.** Имя группы ресурсов для виртуальной машины. Вы можете ввести имя новой или имеющейся группы ресурсов.
 * **Расположение.** Расположение, где следует развернуть новую виртуальную машину. Если виртуальную машину требуется подключить к локальной сети, выберите расположение виртуальной сети, по которой Azure подключается к локальной сети. Дополнительные сведения см. в разделе [Сеть Microsoft Azure][planning-guide-microsoft-azure-networking] [руководства по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide].
2. **Размер**:

     Список поддерживаемых типов виртуальных машин см. в примечании к SAP [1928533]. Выберите правильный тип виртуальной машины, если нужно использовать хранилище Azure класса Premium. Не все типы виртуальных машин поддерживают хранилище класса Premium. Дополнительные сведения см. в разделе [Хранилище. Служба хранилища Microsoft Azure и диски данных][planning-guide-storage-microsoft-azure-storage-and-data-disks] и [Хранилище Azure класса Premium][planning-guide-azure-premium-storage] [руководства по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide].

3. **Параметры**:
  * **Хранилище**
    * **Тип диска.** Выберите тип диска ОС. Если вы хотите использовать хранилище класса Premium для дисков данных, рекомендуется также использовать его для диска ОС.
    * **Использование управляемых дисков.** Если вы хотите использовать управляемые диски, выберите "Да". Дополнительные сведения об управляемых дисках см. в разделе [Управляемые диски][planning-guide-managed-disks] в руководстве по планированию.
    * **Учетная запись хранения.** Создайте учетную запись хранения или выберите имеющуюся. Запуск приложений SAP поддерживают не все типы хранилищ. Дополнительные сведения о типах хранилищ см. в разделе [Служба хранилища Microsoft Azure][dbms-guide-2.3] в статье [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide].
  * **Сеть**
    * **Виртуальная сеть** и **подсеть**. Чтобы интегрировать виртуальную машину в интрасеть, выберите виртуальную сеть, подключенную к локальной сети.
    * **Общедоступный IP-адрес.** Выберите необходимый общедоступный IP-адрес или введите параметры, чтобы создать его. Общедоступный IP-адрес можно использовать для доступа к виртуальной машине через Интернет. Кроме того, создайте группу безопасности сети, используемую для доступа к виртуальной машине.
    * **Группа безопасности сети.** Дополнительные сведения см. в статье [Управление потоком сетевого трафика с помощью групп безопасности сети][virtual-networks-nsg].
  * **Расширения.** Можно установить расширения виртуальных машин, добавив их в развертывание. На этом шаге не нужно добавлять расширение. Расширения, необходимые для поддержки SAP, устанавливаются позже. Дополнительные сведения см. в разделе [Настройка расширения расширенного мониторинга Azure для SAP][deployment-guide-4.5] этого руководства.
  * **Высокая доступность.** Выберите группу доступности или введите параметры, чтобы создать ее. Дополнительные сведения см. в разделе [Группы доступности Azure][planning-guide-3.2.3].
  * **Мониторинг**
    * **Диагностика загрузки.** Диагностику загрузки можно **отключить**.
    * **Диагностика гостевой ОС.** Диагностику мониторинга можно **отключить**.

4. **Сводка**:

  Просмотрите выбранные параметры и нажмите кнопку **ОК**.

После этого виртуальная машина будет развернута в выбранной группе ресурсов.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Создание виртуальной машины с помощью шаблона
Виртуальную машину можно создать с помощью одного из шаблонов SAP, опубликованных в [репозитории azure-quickstart-templates на сайте GitHub][azure-quickstart-templates-github]. Кроме того, ее можно создать вручную с помощью [портала Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] или [Azure CLI][virtual-machines-linux-tutorial].

* [**Шаблон двухуровневой конфигурации (только одна виртуальная машина)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image].

  Этот шаблон позволяет создать двухуровневую систему с использованием только одной виртуальной машины.
* [**Шаблон двухуровневой конфигурации (только одна виртуальная машина) с управляемыми дисками** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md].

  Этот шаблон позволяет создать двухуровневую систему с использованием только одной виртуальной машины и управляемых дисков.
* [**Шаблон трехуровневой конфигурации (несколько виртуальных машин)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image].

  Этот шаблон позволяет создать трехуровневую систему с использованием нескольких виртуальных машин.
* [**Шаблон трехуровневой конфигурации (несколько виртуальных машин) с управляемыми дисками** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md].

  Этот шаблон позволяет создать трехуровневую систему с использованием нескольких виртуальных машин и управляемых дисков.

На портале Azure введите следующие параметры шаблона:

1. **Основные сведения**:
  * **Подписка.** Подписка, используемая для развертывания шаблона.
  * **Группа ресурсов.** Группа ресурсов, используемая для развертывания шаблона. Вы можете создать группу ресурсов или выбрать имеющуюся в подписке.
  * **Расположение.** Расположение, в котором необходимо развернуть шаблон. Если вы выбрали имеющуюся группу ресурсов, используется ее расположение.

2. **Параметры**:
  * **SAP System ID** (Идентификатор системы SAP). Идентификатор системы SAP (ИД безопасности).
  * **OS Type** (Тип ОС). Тип развертываемой операционной системы, например Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) или Oracle Linux 7.2.

    В представлении списка не отображаются все поддерживаемые операционные системы. Дополнительные сведения о поддерживаемых операционных системах для развертывания ПО SAP см. в примечании к SAP [1928533].
  * **Sap System Size** (Размер системы SAP). Размер системы SAP.

    Количество систем SAP, которые предоставляет новая система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям или системному интегратору SAP.
  * **System Availability** (Доступность системы). Доступность системы (только для трехуровневого шаблона).

    Выберите **высокую доступность** в качестве конфигурации, которая подходит для установки высокого уровня доступности. Будут созданы два сервера баз данных и два сервера для ABAP SAP Central Services (ASCS).
  * **Тип хранилища.** Тип используемого хранилища (только для двухуровневого шаблона).

    В системах большого размера мы настоятельно рекомендуем использовать хранилище Azure класса Premium. Дополнительные сведения о типах хранилищ см. в следующих ресурсах:
      * [Использование хранилища SSD класса Premium Azure для экземпляра СУБД SAP][2367194]
      * [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide] (раздел [Служба хранилища Microsoft Azure][dbms-guide-2.3]).
      * [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure][storage-premium-storage-preview-portal]
      * [Введение в службу хранилища Microsoft Azure][storage-introduction]
  * **Имя администратора** и **Пароль администратора.** Имя пользователя и пароль.
    Создается учетная запись пользователя, используемая для входа в виртуальную машину.
  * **New or existing subnet** (Новая или имеющаяся подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите **ее**.
  * **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Чтобы подключить виртуальную машину к локальной сети, выберите подсеть виртуальной частной сети или виртуальной сети Azure ExpressRoute. Идентификатор обычно выглядит следующим образом: /subscriptions/&lt;идентификатор_подписки>/resourceGroups/&lt;имя_группы_ресурсов>/providers/Microsoft.Network/virtualNetworks/&lt;имя_виртуальной_сети>/subnets/&lt;имя_подсети>.

3. **Условия использования:**  
    Прочтите и примите юридические условия.

4.  Щелкните **Приобрести**.

При использовании образа из Azure Marketplace агент виртуальной машины Azure развертывается по умолчанию.

#### <a name="configure-proxy-settings"></a>Настройка параметров прокси
В зависимости от конфигурации локальной сети на виртуальной машине может потребоваться настроить прокси. Если виртуальная машина подключена к локальной сети через VPN или ExpressRoute, она не сможет получить доступ к Интернету и скачать необходимые расширения или собрать данные мониторинга. Дополнительные сведения см. в разделе [Настройка прокси-сервера][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Присоединение к домену (только для Windows)
Если развертывание Azure подключено к локальному экземпляру Active Directory или к локальной службе доменных имен (DNS) с помощью подключения VPN типа "сеть — сеть" Azure или канала ExpressRoute (также называется *распределенным развертыванием* в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide]), то предполагается, что виртуальная машина присоединяется к локальному домену. Рекомендации к этому заданию см. в разделе [Присоединение виртуальной машины к локальному домену (только для Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Настройка мониторинга
Чтобы обеспечить поддержку SAP в своей среде, настройте расширение мониторинга Azure для SAP, как описано в [этом разделе][deployment-guide-4.5]. Проверьте предварительные требования для мониторинга SAP относительно необходимых минимальных версий SAP Kernel и агента SAP Host Agent в ресурсах, указанных в разделе [Ресурсы SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Проверка мониторинга
Проверьте, работает ли мониторинг, как описано в разделе [Проверки и устранение неполадок при комплексной настройке мониторинга][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Действия, выполняемые после развертывания
После создания и развертывания виртуальной машины вам понадобится установить на ней все необходимые программные компоненты. Из-за последовательности развертывания и установки программного обеспечения для этого типа развертывания виртуальной машины необходимое ПО уже должно быть доступно в Azure, на другой виртуальной машине или в качестве диска, который можно подключить. Кроме того, вы можете использовать сценарии распределенного развертывания, где подключение к локальным ресурсам (общим папкам для установки) установлено по умолчанию.

После развертывания виртуальной машины в Azure используйте те же рекомендации и средства для установки ПО SAP на виртуальной машине, что и в локальной среде. Чтобы установить программное обеспечение SAP на виртуальной машине Azure, специалисты SAP и Майкрософт рекомендуют скачать и сохранить установочный носитель SAP на виртуальных жестких дисках Azure или управляемых дисках, или создать виртуальную машину Azure, используемую в качестве файлового сервера со всеми необходимыми установочными носителями SAP.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Сценарий 2. Развертывание виртуальной машины для SAP с помощью пользовательского образа
Разные версии операционной системы или СУБД имеют различные требования по установке исправлений. Поэтому образы, представленные в Azure Marketplace, могут не соответствовать вашим потребностям. В этом случае можно создать виртуальную машину с помощью собственного образа ОС или СУБД, который можно развертывать многократно.
Процедуры создания частного образа в Windows и в Linux отличаются.

- - -
> ![Windows][Logo_Windows] Windows
>
> Чтобы подготовить образ Windows, который можно использовать для развертывания нескольких виртуальных машин, необходимо задать абстрагированные или обобщенные параметры Windows (например, ИД безопасности Windows и имя узла) на локальной виртуальной машине. Это можно сделать с помощью инструмента [Sysprep](https://msdn.microsoft.com/library/hh825084.aspx).
>
> ![Linux][Logo_Linux] Linux
>
> Чтобы подготовить образ Linux, который можно использовать для развертывания нескольких виртуальных машин, необходимо задать некоторые абстрагированные или обобщенные параметры Linux на локальной виртуальной машине. Это можно сделать с помощью команды `waagent -deprovision`. Дополнительные сведения см. в статье [Запись виртуальной машины Linux, работающей в Azure][virtual-machines-linux-capture-image] и в [руководстве пользователя агента Linux для Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Вы можете подготовить и создать пользовательский образ, а затем с его помощью создать несколько виртуальных машин. Дополнительные сведения см. в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide]. Содержимое базы данных можно настроить с помощью диспетчера подготовки программного обеспечения SAP. Это позволяет установить новую систему SAP, восстановить резервную копию базы данных с диска, подключенного к виртуальной машине, или восстановить резервную копию базы данных напрямую из службы хранилища Azure, если СУБД поддерживает это. Дополнительные сведения см. в [руководстве по развертыванию СУБД][dbms-guide]. Если вы уже установили систему SAP на локальную виртуальную машину (особенно важно для двухуровневых систем), то параметры системы SAP можно адаптировать после развертывания виртуальной машины в Azure. Для этого выполните процедуру переименования системы, поддерживаемую диспетчером подготовки программного обеспечения SAP (см. примечание к SAP [1619720]). В противном случае ПО SAP можно установить позже после развертывания виртуальной машины Azure.

На блок-схеме ниже приведена последовательность действий по развертыванию виртуальной машины с помощью пользовательского образа.

![Блок-схема развертывания виртуальной машины для систем SAP с помощью образа виртуальной машины в частном Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Создание виртуальной машины с помощью портала Azure
Самый простой способ создания новой виртуальной машины из образа управляемых дисков — с помощью портала Azure. Дополнительные сведения о создании образа управляемого диска см. в статье [Создание управляемого образа универсальной виртуальной машины в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

1.  Перейдите на сайт <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. или щелкните **Образы** в меню портала Azure.
2.  Выберите образ управляемого диска, который нужно развернуть, и щелкните **Создать виртуальную машину**.

Мастер поможет настроить необходимые параметры для создания виртуальной машины, а также все необходимые ресурсы, например сетевые интерфейсы или учетные записи хранения. Ниже приведены некоторые из этих параметров.

1. **Основные сведения**:
 * **Имя.** Имя ресурса, например виртуальной машины.
 * **Тип диска виртуальной машины.** Выберите тип диска ОС. Если вы хотите использовать хранилище класса Premium для дисков данных, рекомендуется также использовать его для диска ОС.
 * **Имя пользователя и пароль** или **открытый ключ SSH**. Введите имя и пароль учетной записи пользователя, созданной во время подготовки. Для виртуальной машины Linux можно ввести открытый ключ SSH, используемый для входа на компьютер.
 * **Подписка.** Выберите подписку, которую нужно использовать для подготовки новой виртуальной машины.
 * **Группа ресурсов.** Имя группы ресурсов для виртуальной машины. Вы можете ввести имя новой или имеющейся группы ресурсов.
 * **Расположение.** Расположение, где следует развернуть новую виртуальную машину. Если виртуальную машину требуется подключить к локальной сети, выберите расположение виртуальной сети, по которой Azure подключается к локальной сети. Дополнительные сведения см. в разделе [Сеть Microsoft Azure][planning-guide-microsoft-azure-networking] [руководства по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide].
2. **Размер**:

     Список поддерживаемых типов виртуальных машин см. в примечании к SAP [1928533]. Выберите правильный тип виртуальной машины, если нужно использовать хранилище Azure класса Premium. Не все типы виртуальных машин поддерживают хранилище класса Premium. Дополнительные сведения см. в разделе [Хранилище. Служба хранилища Microsoft Azure и диски данных][planning-guide-storage-microsoft-azure-storage-and-data-disks] и [Хранилище Azure класса Premium][planning-guide-azure-premium-storage] [руководства по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide].

3. **Параметры**:
  * **Хранилище**
    * **Тип диска.** Выберите тип диска ОС. Если вы хотите использовать хранилище класса Premium для дисков данных, рекомендуется также использовать его для диска ОС.
    * **Использование управляемых дисков.** Если вы хотите использовать управляемые диски, выберите "Да". Дополнительные сведения об управляемых дисках см. в разделе [Управляемые диски][planning-guide-managed-disks] в руководстве по планированию.
  * **Сеть**
    * **Виртуальная сеть** и **подсеть**. Чтобы интегрировать виртуальную машину в интрасеть, выберите виртуальную сеть, подключенную к локальной сети.
    * **Общедоступный IP-адрес.** Выберите необходимый общедоступный IP-адрес или введите параметры, чтобы создать его. Общедоступный IP-адрес можно использовать для доступа к виртуальной машине через Интернет. Кроме того, создайте группу безопасности сети, используемую для доступа к виртуальной машине.
    * **Группа безопасности сети.** Дополнительные сведения см. в статье [Управление потоком сетевого трафика с помощью групп безопасности сети][virtual-networks-nsg].
  * **Расширения.** Можно установить расширения виртуальных машин, добавив их в развертывание. На этом шаге не нужно добавлять расширение. Расширения, необходимые для поддержки SAP, устанавливаются позже. Дополнительные сведения см. в разделе [Настройка расширения расширенного мониторинга Azure для SAP][deployment-guide-4.5] этого руководства.
  * **Высокая доступность.** Выберите группу доступности или введите параметры, чтобы создать ее. Дополнительные сведения см. в разделе [Группы доступности Azure][planning-guide-3.2.3].
  * **Мониторинг**
    * **Диагностика загрузки.** Диагностику загрузки можно **отключить**.
    * **Диагностика гостевой ОС.** Диагностику мониторинга можно **отключить**.

4. **Сводка**:

  Просмотрите выбранные параметры и нажмите кнопку **ОК**.

После этого виртуальная машина будет развернута в выбранной группе ресурсов.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Создание виртуальной машины с помощью шаблона
Чтобы создать развертывание с помощью частного образа ОС на портале Azure, воспользуйтесь одним из шаблонов SAP ниже. Эти шаблоны опубликованы в [репозитории azure-quickstart-templates на сайте GitHub ][azure-quickstart-templates-github]. Кроме того, виртуальную машину можно создать вручную с помощью [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Шаблон двухуровневой конфигурации (только одна виртуальная машина)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image].

  Этот шаблон позволяет создать двухуровневую систему с использованием только одной виртуальной машины.
* [**Шаблон двухуровневой конфигурации (только одна виртуальная машина) с образом управляемого диска** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md].

  Этот шаблон позволяет создать двухуровневую систему с использованием только одной виртуальной машины и образа управляемого диска.
* [**Шаблон трехуровневой конфигурации (несколько виртуальных машин)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image].

  Этот шаблон позволяет создать трехуровневую систему с помощью нескольких виртуальных машин или собственного образа ОС.
* [**Шаблон трехуровневой конфигурации (несколько виртуальных машин) с образом управляемого диска** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md].

  Этот шаблон позволяет создать трехуровневую систему с помощью нескольких виртуальных машин или собственного образа ОС и образа управляемого диска.

На портале Azure введите следующие параметры шаблона:

1. **Основные сведения**:
  * **Подписка.** Подписка, используемая для развертывания шаблона.
  * **Группа ресурсов.** Группа ресурсов, используемая для развертывания шаблона. Вы можете создать группу ресурсов или выбрать имеющуюся в подписке.
  * **Расположение.** Расположение, в котором необходимо развернуть шаблон. Если вы выбрали имеющуюся группу ресурсов, используется ее расположение.
2. **Параметры**:
  * **SAP System ID** (Идентификатор системы SAP). Идентификатор системы SAP.
  * **OS Type** (Тип ОС). Тип развертываемой операционной системы (Windows или Linux).
  * **Sap System Size** (Размер системы SAP). Размер системы SAP.

    Количество систем SAP, которые предоставляет новая система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям или системному интегратору SAP.
  * **System Availability** (Доступность системы). Доступность системы (только для трехуровневого шаблона).

    Выберите **высокую доступность** в качестве конфигурации, которая подходит для установки высокого уровня доступности. Будут созданы два сервера баз данных и два сервера для ASCS.
  * **Тип хранилища.** Тип используемого хранилища (только для двухуровневого шаблона).

    В системах большого размера мы настоятельно рекомендуем использовать хранилище Azure класса Premium. Дополнительные сведения о типах хранилищ см. в следующих ресурсах:
      * [Использование хранилища SSD класса Premium Azure для экземпляра СУБД SAP][2367194]
      * [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide] (раздел [Служба хранилища Microsoft Azure][dbms-guide-2.3]).
      * [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure][storage-premium-storage-preview-portal]
      * [Введение в службу хранилища Microsoft Azure][storage-introduction]
  * **User image VHD URI** (Универсальный код ресурса (URI) VHD пользовательского образа) (только шаблон с образом неуправляемого диска). Универсальный код ресурса (URI) виртуального жесткого диска частного образа ОС, например https://&lt;имя_учетной_записи>.blob.core.windows.net/vhds/userimage.vhd.
  * **User image storage account** (Учетная запись хранения пользовательского образа) (только шаблон с образом неуправляемого диска). Имя учетной записи хранения, где хранится частный образ ОС, например &lt;имя_учетной_записи> в примере https://&lt;имя_учетной_записи>.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (только шаблон с образом управляемого диска). Идентификатор образа управляемого диска, который нужно использовать.
  * **Имя администратора** и **Пароль администратора.** Имя пользователя и пароль.

    Создается учетная запись пользователя, используемая для входа в виртуальную машину.
  * **New or existing subnet** (Новая или имеющаяся подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите **ее**.
  * **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Чтобы подключить виртуальную машину к локальной сети, выберите подсеть виртуальной частной сети или виртуальной сети ExpressRoute. Идентификатор обычно выглядит следующим образом:

    /subscriptions/&lt;идентификатор_подписки>/resourceGroups/&lt;имя_группы_ресурсов>/providers/Microsoft.Network/virtualNetworks/&lt;имя_виртуальной_сети>/subnets/&lt;имя_подсети>.

3. **Условия использования:**  
    Прочтите и примите юридические условия.

4.  Щелкните **Приобрести**.

#### <a name="install-the-vm-agent-linux-only"></a>Установка агента виртуальной машины (только для Linux)
Если требуется использовать шаблоны, описанные в предыдущем разделе, в пользовательском образе должен быть установлен агент Linux. В противном случае развертывание завершится сбоем. Скачайте и установите агент виртуальной машины в пользовательский образ, как описано в разделе [Скачивание, установка и включение агента виртуальной машины Azure][deployment-guide-4.4]. Если вы не используете шаблоны, агент виртуальной машины можно установить позже.

#### <a name="join-a-domain-windows-only"></a>Присоединение к домену (только для Windows)
Если развертывание Azure подключено к локальному экземпляру Active Directory или к локальной службе доменных имен (DNS) с помощью подключения VPN типа "сеть — сеть" Azure или канала Azure ExpressRoute (также называется *распределенным развертыванием* в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide]), то предполагается, что виртуальная машина присоединяется к локальному домену. Рекомендации к этому этапу см. в разделе [Присоединение виртуальной машины к локальному домену (только для Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Настройка параметров прокси
В зависимости от конфигурации локальной сети на виртуальной машине может потребоваться настроить прокси. Если виртуальная машина подключена к локальной сети через VPN или ExpressRoute, она не сможет получить доступ к Интернету и скачать необходимые расширения или собрать данные мониторинга. Дополнительные сведения см. в разделе [Настройка прокси-сервера][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Настройка мониторинга
Чтобы обеспечить поддержку SAP в своей среде, настройте расширение мониторинга Azure для SAP, как описано в [этом разделе][deployment-guide-4.5]. Проверьте предварительные требования для мониторинга SAP относительно необходимых минимальных версий SAP Kernel и агента SAP Host Agent в ресурсах, указанных в разделе [Ресурсы SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Проверка мониторинга
Проверьте, работает ли мониторинг, как описано в разделе [Проверки и устранение неполадок при комплексной настройке мониторинга][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Сценарий 3. Перемещение локальной виртуальной машины с помощью специализированного виртуального жесткого диска Azure с SAP
В этом случае вы планируете переместить определенную систему SAP из локальной среды в Azure. Для этого можно передать в Azure виртуальный жесткий диск, который содержит ОС, двоичные файлы SAP и конечные двоичные файлы СУБД, а также дополнительные виртуальные жесткие диски с файлами данных и журналов СУБД. В отличие от [сценария 2][deployment-guide-3.3] в этом случае следует сохранить имя узла, ИД безопасности и учетные записи пользователей SAP в виртуальной машине Azure, так как они были настроены в локальной среде. В этом сценарии не требуется выполнять обобщение операционной системы. Чаще всего он применяется для распределенных развертываний, где часть ландшафта SAP выполняется локально, а другая часть — в Azure.

В этом случае агент виртуальной машины **не** устанавливается автоматически во время развертывания. Так как агент виртуальной машины и расширение расширенного мониторинга Azure для SAP требуются для запуска SAP NetWeaver в Azure, их необходимо скачать, установить и включить вручную после создания виртуальной машины.

Дополнительные сведения об агенте виртуальной машины Azure см. в следующих ресурсах:

- - -
> ![Windows][Logo_Windows] Windows
>
> [Обзор агента виртуальной машины Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Руководство пользователя агента Linux для Azure][virtual-machines-linux-agent-user-guide]
>
>

- - -

На блок-схеме ниже приведена последовательность действий по перемещению локальной виртуальной машины с помощью специализированного виртуального жесткого диска Azure.

![Блок-схема развертывания виртуальной машины для систем SAP с помощью диска виртуальной машины][deployment-guide-figure-400]

Если диск уже передан и определен в Azure (см. [руководство по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide]), выполните действия, описанные в нескольких следующих разделах.

#### <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Чтобы создать развертывание с помощью частного диска ОС на портале Azure, воспользуйтесь шаблоном SAP, опубликованным в [репозитории azure-quickstart-templates на сайте GitHub][azure-quickstart-templates-github]. Кроме того, виртуальную машину можно создать вручную с помощью PowerShell.

* [**Шаблон двухуровневой конфигурации (только одна виртуальная машина)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk].

  Этот шаблон позволяет создать двухуровневую систему с использованием только одной виртуальной машины.
* [**Шаблон двухуровневой конфигурации (только одна виртуальная машина) с управляемым диском** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md].

  Этот шаблон позволяет создать двухуровневую систему с использованием только одной виртуальной машины и управляемого диска.

На портале Azure введите следующие параметры шаблона:

1. **Основные сведения**:
  * **Подписка.** Подписка, используемая для развертывания шаблона.
  * **Группа ресурсов.** Группа ресурсов, используемая для развертывания шаблона. Вы можете создать группу ресурсов или выбрать имеющуюся в подписке.
  * **Расположение.** Расположение, в котором необходимо развернуть шаблон. Если вы выбрали имеющуюся группу ресурсов, используется ее расположение.
2. **Параметры**:
  * **SAP System ID** (Идентификатор системы SAP). Идентификатор системы SAP.
  * **OS Type** (Тип ОС). Тип развертываемой операционной системы (Windows или Linux).
  * **Sap System Size** (Размер системы SAP). Размер системы SAP.

    Количество систем SAP, которые предоставляет новая система. Если вы не знаете, сколько систем SAP потребуется для системы, обратитесь к партнеру по технологиям или системному интегратору SAP.
  * **Тип хранилища.** Тип используемого хранилища (только для двухуровневого шаблона).

    В системах большого размера мы настоятельно рекомендуем использовать хранилище Azure класса Premium. Дополнительные сведения о типах хранилищ см. в следующих ресурсах:
      * [Использование хранилища SSD класса Premium Azure для экземпляра СУБД SAP][2367194]
      * [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide] (раздел [Служба хранилища Microsoft Azure][dbms-guide-2.3]).
      * [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure][storage-premium-storage-preview-portal]
      * [Введение в службу хранилища Microsoft Azure][storage-introduction]
  * **OS disk VHD URI** (Универсальный код ресурса (URI) VHD диска ОС) (только шаблон с неуправляемым диском). Универсальный код ресурса (URI) частного диска ОС, например https://&lt;имя_учетной_записи>.blob.core.windows.net/vhds/osdisk.vhd.
  * **OS disk Managed Disk Id** (Идентификатор управляемого диска ОС) (только шаблон с неуправляемым диском). Идентификатор управляемого диска ОС, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN.
  * **New or existing subnet** (Новая или имеющаяся подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. При наличии виртуальной сети, подключенной к локальной сети, выберите **ее**.
  * **Subnet Id** (Идентификатор подсети). Идентификатор подсети, к которой следует подключить виртуальные машины. Чтобы подключить виртуальную машину к локальной сети, выберите подсеть виртуальной частной сети или виртуальной сети Azure ExpressRoute. Идентификатор обычно выглядит следующим образом:

    /subscriptions/&lt;идентификатор_подписки>/resourceGroups/&lt;имя_группы_ресурсов>/providers/Microsoft.Network/virtualNetworks/&lt;имя_виртуальной_сети>/subnets/&lt;имя_подсети>.

3. **Условия использования:**  
    Прочтите и примите юридические условия.

4.  Щелкните **Приобрести**.

#### <a name="install-the-vm-agent"></a>"Установить агент виртуальной машины"
Если требуется использовать шаблоны, описанные в предыдущем разделе, на диске ОС должен быть установлен агент виртуальной машины. В противном случае развертывание завершится сбоем. Скачайте и установите агент виртуальной машины на виртуальной машине, как описано в разделе [Скачивание, установка и включение агента виртуальной машины Azure][deployment-guide-4.4].

Если вы не используете шаблоны, описанные в предыдущем разделе, агент виртуальной машины можно установить позже.

#### <a name="join-a-domain-windows-only"></a>Присоединение к домену (только для Windows)
Если развертывание Azure подключено к локальному экземпляру Active Directory или к локальной службе доменных имен (DNS) с помощью подключения VPN типа "сеть — сеть" Azure или канала ExpressRoute (также называется *распределенным развертыванием* в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide]), то предполагается, что виртуальная машина присоединяется к локальному домену. Рекомендации к этому заданию см. в разделе [Присоединение виртуальной машины к локальному домену (только для Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Настройка параметров прокси
В зависимости от конфигурации локальной сети на виртуальной машине может потребоваться настроить прокси. Если виртуальная машина подключена к локальной сети через VPN или ExpressRoute, она не сможет получить доступ к Интернету и скачать необходимые расширения или собрать данные мониторинга. Дополнительные сведения см. в разделе [Настройка прокси-сервера][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Настройка мониторинга
Чтобы обеспечить поддержку SAP в своей среде, настройте расширение мониторинга Azure для SAP, как описано в [этом разделе][deployment-guide-4.5]. Проверьте предварительные требования для мониторинга SAP относительно необходимых минимальных версий SAP Kernel и агента SAP Host Agent в ресурсах, указанных в разделе [Ресурсы SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Проверка мониторинга
Проверьте, работает ли мониторинг, как описано в разделе [Проверки и устранение неполадок при комплексной настройке мониторинга][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Обновление конфигурации мониторинга для SAP
Конфигурацию мониторинга для SAP следует обновлять в следующих случаях:
* Совместная группа сотрудников Майкрософт и SAP расширила возможности мониторинга и запрашивает увеличение или уменьшение числа счетчиков.
* Корпорация Майкрософт выпустила новую версию базовой инфраструктуры Azure, предоставляющую данные мониторинга, и расширение расширенного мониторинга Azure для SAP нужно адаптировать к этим изменениям.
* Дополнительные диски данных подключаются или удаляются из виртуальной машины Azure. В этом случае необходимо обновить коллекцию данных, связанных с хранилищем. Изменение конфигурации в результате добавления или удаления конечных точек или назначения IP-адресов виртуальной машине не влияет на конфигурацию мониторинга.
* Изменился размер виртуальной машины Azure, например с A5 на любой другой размер.
* В виртуальную машину Azure добавлены новые сетевые интерфейсы.

Чтобы обновить параметры мониторинга, обновите инфраструктуру мониторинга, выполнив действия, указанные в разделе [Настройка расширения расширенного мониторинг Azure для SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Подробное описание развертывания программного обеспечения SAP
В этом разделе приведены подробные указания по выполнению конкретных задач в процессе настройки и развертывания.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Развертывание командлетов Azure PowerShell
1.  Перейдите на страницу [скачиваний Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  В разделе **Программы командной строки** в подразделе **PowerShell** выберите **Установка Windows**.
3.  Откройте скачанный файл (например, WindowsAzurePowershellGet.3f.3f.3fnew.exe), а затем в диалоговом окне Диспетчера загрузки Майкрософт выберите **Запустить**.
4.  Чтобы запустить установщик веб-платформы Майкрософт, нажмите кнопку **Да**.
5.  Откроется следующая страница:

  ![Страница установки командлетов Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Нажмите кнопку **Установить** и примите условия лицензионного соглашения об использовании программного обеспечения Майкрософт.
7.  После этого будет установлен Azure PowerShell. Нажмите кнопку **Готово**, чтобы закрыть окно мастера установки.

Регулярно проверяйте наличие обновлений для командлетов PowerShell. Обычно обновление происходит раз в месяц. Проще всего наличие обновлений можно проверить, выполнив указания, описанные выше, до страницы, показанной на шаге 5. На этом снимке экрана показана дата и номер выпуска командлетов. Если иное не указано в примечаниях к SAP [1928533] или [2015553], то мы советуем работать с последней версией командлетов Azure PowerShell.

Чтобы проверить версию командлетов Azure PowerShell, установленную на компьютере, выполните следующую команду PowerShell:
```powershell
(Get-Module AzureRm.Compute).Version
```
Результат должен выглядеть следующим образом:

![Результат проверки версии командлетов Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Если версия установленных на компьютере командлетов Azure совпадает с текущей, то на первой странице мастера установки к названию продукта будет добавлена часть **(Установлено)** (см. снимок экрана ниже). Это означает, что установлена последняя версия командлетов Azure. Чтобы закрыть окно мастера установки, нажмите кнопку **Выход**.

![Страница установки командлетов Azure PowerShell, на которой указано, что установлена последняя версия командлетов Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Развертывание Azure CLI
1.  Перейдите на страницу [скачиваний Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  В разделе **Программы командной строки** в подразделе **Интерфейс командной строки Azure** щелкните **ссылку** для своей операционной системы.
3.  Откройте скачанный файл (например, WindowsAzureXPlatCLI.3f.3f.3fnew.exe), а затем в диалоговом окне Диспетчера загрузки Майкрософт выберите **Запустить**.
4.  Чтобы запустить установщик веб-платформы Майкрософт, нажмите кнопку **Да**.
5.  Откроется следующая страница:

  ![Страница установки командлетов Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Нажмите кнопку **Установить** и примите условия лицензионного соглашения об использовании программного обеспечения Майкрософт.
7.  После этого будет установлен Azure CLI. Нажмите кнопку **Готово**, чтобы закрыть окно мастера установки.

Регулярно проверяйте наличие обновлений для Azure CLI. Обычно обновление происходит раз в месяц. Проще всего наличие обновлений можно проверить, выполнив указания, описанные выше, до страницы, показанной на шаге 5.

Чтобы проверить версию Azure CLI, установленную на компьютере, выполните следующую команду:
```
azure --version
```

Результат должен выглядеть следующим образом:

![Результат проверки версии с помощью интерфейса командой строки Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Присоединение виртуальной машины к локальному домену (только для Windows)
При развертывании виртуальной машины SAP в распределенной среде, где локальный экземпляр Active Directory и DNS расширяются в Azure, предполагается, что виртуальные машины присоединяются к локальному домену. Действия по присоединению виртуальной машины к локальному домену и установка дополнительного программного обеспечения, необходимого для присоединения к локальному домену, зависит от клиента. Обычно при присоединении виртуальной машины к локальному домену необходимо установить дополнительное программное обеспечение, например антивредоносное ПО или программу наблюдения.

В этом случае необходимо также убедиться, что параметры прокси Интернета, заданные принудительно при присоединении виртуальной машины к домену в вашей среде, совпадают с параметрами прокси учетной записи Windows Local System (S-1-5-18) в операционной системе на виртуальной машине. Проще всего принудительно применить к прокси групповую политику домена, которая применяется к системам в домене.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Скачивание, установка и включение агента виртуальной машины Azure
Для виртуальных машин, развернутых с помощью специализированного образа ОС (например, образа, не обработанного средством Sysprep для Windows), необходимо вручную скачать, установить и включить агент виртуальной машины.

При развертывании виртуальной машины из образа Azure Marketplace этот шаг выполнять необязательно. Эти образы уже содержат агент виртуальной машины.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Скачайте агент виртуальной машины Azure.
  1.  Скачайте [пакет установщика агента виртуальной машины Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
  2.  Сохраните пакет MSI агента виртуальной машины локально на компьютере или сервере.
2.  Установите агент виртуальной машины Azure.
  1.  Подключитесь к развернутой виртуальной машине Azure с помощью протокола удаленного рабочего стола (RDP).
  2.  Откройте окно проводника на виртуальной машине и выберите целевой каталог для MSI-файла агента виртуальной машины.
  3.  Перетащите MSI-файл установщика агента виртуальной машины Azure с локального компьютера или сервера в целевой каталог агента виртуальной машины на виртуальной машине.
  4.  Дважды щелкните MSI-файл в виртуальной машине.
3.  Убедитесь, что для виртуальной машины, присоединенной к локальным доменам, к учетной записи Local System Windows (S-1-5-18) применяются окончательные параметры прокси Интернета, описанные в разделе [Настройка прокси][deployment-guide-configure-proxy]. Агент виртуальной машины выполняется в этом контексте и должен иметь возможность подключиться к Azure.

Обновление агента виртуальной машины происходит без участия пользователя. Это автоматический процесс, не требующий перезагрузки виртуальной машины.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Чтобы установить агент виртуальной машины для Linux, выполните одну из следующих команд:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) или Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Если агент Linux для Azure уже установлен, чтобы обновить его, выполните действия, описанные в [этой статье][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Настройка прокси
Процедура настройки прокси отличается для Windows и Linux.

#### <a name="windows"></a>Windows
Чтобы получать доступ к Интернету, необходимо должным образом настроить параметры прокси для учетной записи Local System. Если эти параметры не заданы с помощью групповой политики, их можно настроить для учетной записи Local System.

1. Войдите в меню **Пуск**, введите **gpedit.msc**, а затем нажмите клавишу **ВВОД**.
2. Выберите **Конфигурация компьютера** > **Административные шаблоны** > **Компоненты Windows** > **Internet Explorer**. Убедитесь, что параметр **Задать параметры прокси для компьютера (а не для пользователя)** отключен или не настроен.
3. На **панели управления** выберите **Центр управления сетями и общим доступом** > **Свойства обозревателя**.
4. На вкладке **Подключения** нажмите кнопку **Параметры сети**.
5. Снимите флажок **Автоматическое определение параметров**.
6. Установите флажок **Использовать прокси-сервер для локальной сети** и введите адрес и порт прокси.
7. Нажмите кнопку **Advanced** (Дополнительно).
8. В поле **Исключения** введите следующий IP-адрес: **168.63.129.16**. Нажмите кнопку **ОК**.

#### <a name="linux"></a>Linux
Настройте правильный прокси в файле конфигурации гостевого агента Microsoft Azure, расположенного в каталоге \\etc\\waagent.conf.

Задайте следующие параметры:

1.  **Узел прокси-сервера HTTP**, например **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **Порт прокси-сервера HTTP**, например **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  Перезапустите агент.

  ```
  sudo service waagent restart
  ```

Параметры прокси в файле \\etc\\waagent.conf также можно применить к требуемым расширениям виртуальной машины. Если вы хотите использовать репозитории Azure, убедитесь, что входящий трафик для них не проходит через локальную интрасеть. Если для включения принудительного туннелирования использовались пользовательские маршруты, добавьте маршрут, который направляет трафик, поступающий в репозитории, непосредственно к Интернету, а не через подключение VPN типа "сеть — сеть".

* **SLES**

  Необходимо также добавить маршруты для IP-адресов, указанных в файле \\etc\\regionserverclnt.cfg. Пример показан на приведенном ниже снимке экрана.

  ![Принудительное туннелирование][deployment-guide-figure-50]


* **RHEL**

  Необходимо также добавить маршруты для IP-адресов узлов, перечисленных в \\etc\\yum.repos.d\\rhui-load-balancers. Пример показан на приведенном ниже снимке экрана.

* **Oracle Linux**

  Для Oracle Linux в Azure нет репозиториев. Необходимо настроить собственные репозитории Oracle Linux или использовать общедоступные.

Дополнительные сведения о пользовательских маршрутах см. в [этой статье][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Настройка расширения расширенного мониторинга Azure для SAP
После подготовки виртуальной машины, как описано в разделе [Сценарии развертывания ПО SAP на виртуальных машинах Azure][deployment-guide-3], агент виртуальной машины устанавливается на виртуальной машине. Теперь необходимо развернуть расширение расширенного мониторинга Azure для SAP, доступное в репозитории расширений Azure в глобальных центрах обработки данных Azure. Дополнительные сведения см. в [руководстве по планированию и внедрению SAP NetWeaver на виртуальных машинах][planning-guide-9.1].

Чтобы установить и настроить расширение расширенного мониторинга Azure для SAP, можно использовать PowerShell или Azure CLI. Дополнительные сведения об установке расширения на виртуальную машину Windows или Linux с помощью компьютера с Windows см. в разделе [Azure PowerShell для виртуальных машин Windows и Linux][deployment-guide-4.5.1]. Рекомендации по установке расширения на виртуальной машине Linux с помощью компьютера с Linux см. в разделе [Интерфейс командной строки Azure для виртуальных машин Linux][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell для виртуальных машин Windows и Linux
Чтобы установить расширение расширенного мониторинга Azure для SAP с помощью PowerShell, сделайте следующее:

1. Установите последнюю версию командлетов Azure PowerShell. Дополнительные сведения см. в разделе [Развертывание командлетов Azure PowerShell][deployment-guide-4.1].  
2. Выполните приведенный ниже командлет PowerShell.
    Чтобы просмотреть список доступных сред, выполните командлет `commandlet Get-AzureRmEnvironment`. Если нужно использовать глобальный экземпляр Azure, выберите среду **AzureCloud**. Для Azure в Китае выберите **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

После указания данных учетной записи и определения виртуальной машины Azure сценарий развернет требуемые расширения и включит необходимые компоненты. Это может занять несколько минут.
Дополнительные сведения о командлете `Set-AzureRmVMAEMExtension` см. в [этой статье][msdn-set-azurermvmaemextension].

![Экран успешного выполнения командлета Azure Set-AzureRmVMAEMExtension для SAP][deployment-guide-figure-900]

Сценарий `Set-AzureRmVMAEMExtension` выполняет все действия, необходимые для настройки функции мониторинга узла для SAP.

Результат выполнения сценария содержит следующие сведения:

* Подтверждение, что мониторинг диска ОС и всех дополнительных дисков данных настроен.
* В следующих двух сообщениях подтверждается настройка метрик хранилища для определенной учетной записи хранения.
* В одной из строк вывода указывается состояние фактического обновления конфигурации мониторинга,
* а в другой подтверждается развертывание или обновление конфигурации.
* Последняя строка вывода — информационная. В ней содержатся сведения о возможности тестирования конфигурации мониторинга.
* Чтобы проверить, успешно ли выполнены все этапы расширенного мониторинга Azure и содержит ли инфраструктура Azure необходимые данные, продолжите проверку готовности расширения расширенного мониторинга Azure для SAP, как описано в разделе [Проверка готовности расширения расширенного мониторинг Azure для SAP][deployment-guide-5.1].
* Подождите 15–30 минут, в течение которых система диагностики Azure соберет соответствующие данные.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Интерфейс командной строки Azure для виртуальных машин Linux
Чтобы установить расширение расширенного мониторинга Azure для SAP с помощью Azure CLI, сделайте следующее:

1. Установите Azure CLI 1.0, как описано в [этой статье][azure-cli].
2. Войдите в систему, используя учетную запись Azure.

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

5. Проверьте, включено ли расширение расширенного мониторинга Azure на виртуальной машине Linux в Azure. Проверьте, создан ли файл \\var\\lib\\AzureEnhancedMonitor\\PerfCounters. Если он создан, чтобы отобразить сведения, собираемые расширением расширенного мониторинга Azure, в окне командной строки выполните следующую команду:
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

Выходные данные выглядят следующим образом:
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
???
???
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Проверки и устранение неполадок при комплексной настройке мониторинга
Развернув виртуальную машину Azure и настроив соответствующую инфраструктуру мониторинга Azure, проверьте, работают ли все компоненты расширения расширенного мониторинга Azure должным образом.

Проверьте готовность расширения расширенного мониторинга Azure для SAP, как описано в разделе [Проверка готовности расширения расширенного мониторинг Azure для SAP][deployment-guide-5.1]. Если проверка выполнена успешно и получены все соответствующие счетчики производительности, мониторинг Azure успешно настроен. В этом случае продолжайте установку агента SAP Host Agent, как описано в примечаниях к SAP, указанных в разделе [Ресурсы SAP][deployment-guide-2.2]. Если в результате проверки готовности обнаружены отсутствующие счетчики, проверьте работоспособность инфраструктуры мониторинга Azure, как описано в разделе [Проверка работоспособности конфигурации инфраструктуры мониторинга Azure][deployment-guide-5.2]. Сведения об устранении неполадок см. в разделе [Устранение неполадок инфраструктуры мониторинга Azure для SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Проверка готовности расширения расширенного мониторинг Azure для SAP
Эта проверка позволяет убедиться, что все метрики производительности, отображаемые в приложении SAP, предоставляются базовой инфраструктурой мониторинга Azure.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Проверка готовности на виртуальной машине Windows

1.  Войдите в виртуальную машину Azure (учетная запись администратора не требуется).
2.  Откройте окно командной строки и
3.  перейдите в папку установки расширения расширенного мониторинга Azure для SAP: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;версия>\\drop.

  *Версия* в приведенном выше пути к расширению мониторинга может отличаться. Если в папке установки отображается несколько папок версий расширения мониторинга, проверьте конфигурацию службы расширенного мониторинга Azure для Windows и перейдите в папку, указанную в поле *Path to executable* (Путь к исполняемому объекту).

  ![Свойства службы, в которой выполняется расширение расширенного мониторинга Azure для SAP][deployment-guide-figure-1000]

4.  В окне командной строки запустите файл **azperflib.exe** без параметров.

  > [!NOTE]
  > Файл azperflib.exe выполняется в цикле и обновляет собираемые счетчики каждые 60 секунд. Чтобы завершить цикл, закройте окно командной строки.
  >
  >

Если расширение расширенного мониторинга Azure не установлено или не запущена служба расширенного мониторинга Azure, расширение настроено неправильно. Подробные сведения о развертывании расширения см. в разделе [Устранение неполадок инфраструктуры мониторинга Azure для SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Проверка результата выполнения azperflib.exe
Результат выполнения файла azperflib.exe содержит все заполненные счетчики производительности Azure для SAP. В нижней части списка собранных счетчиков указана сводка и индикатор работоспособности, указывающий на состояние мониторинга Azure.

![Результат проверки работоспособности с использованием файла azperflib.exe, в котором указано, что ошибок нет][deployment-guide-figure-1100]
<a name="figure-11"></a>

Проверьте возвращенные значения **общего числа счетчиков** (отсутствующие значения), а также **проверки работоспособности**, показанные на снимке экрана выше.

Полученные значения можно интерпретировать следующим образом:

| Итоговые значения azperflib.exe | Состояние работоспособности мониторинга Azure |
| --- | --- |
| **Вызовы API — недоступны** | Недоступные счетчики могут не применяться к конфигурации виртуальной машины или вызывать ошибки. Просмотрите **состояние работоспособности**. |
| **Counters total - empty** (Всего счетчиков: пусто) |Значения могут отсутствовать для следующих двух счетчиков службы хранилища Azure: <ul><li>задержка операций чтения в хранилище на сервере (мс);</li><li>задержка операций чтения в хранилище E2E (мс).</li></ul>Все остальные значения счетчиков должны присутствовать. |
| **Состояние работоспособности** |Отображается только при возврате состояния **ОК**. |
| **Диагностика** |Подробные сведения о состоянии работоспособности. |

Если значение **состояния работоспособности** не **ОК**, следуйте инструкциям в разделе [Проверка работоспособности конфигурации инфраструктуры мониторинга Azure][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Проверка готовности на виртуальной машине Linux

1.  Подключитесь к виртуальной машине Azure по протоколу SSH.

2.  Проверьте выходные данные расширения расширенного мониторинга Azure.

  a.  Запустите `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Ожидаемый результат.** Возвращает список счетчиков производительности. Файл не должен быть пустым.

 Б. Запустите `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Ожидаемый результат.** Возвращает одну строку, где для параметра error задано значение **none**, **3;config;Error;;0;0;none;0;1456416792;tst-servercs;**.

  c. Запустите `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

    **Ожидаемый результат.** Возвращает пустую строку или строка отсутствует.

Если предыдущая проверка прошла безуспешно, выполните следующие дополнительные проверки:

1.  Проверьте, установлен и запущен ли каталог waagent.

  a.  Запустите `sudo ls -al /var/lib/waagent/`

      **Ожидаемый результат.** Должно отобразиться содержимое каталога waagent.

  Б.  Запустите `ps -ax | grep waagent`

   **Ожидаемый результат.** Должна отобразиться одна запись, аналогичная следующей: `python /usr/sbin/waagent -daemon`.

3.   Проверьте, установлено и запущено ли расширение расширенного мониторинга Azure.

  a.  Запустите `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

    **Ожидаемый результат.** Должно отобразиться содержимое каталога расширения расширенного мониторинга Azure.

  Б. Запустите `ps -ax | grep AzureEnhanced`

     **Ожидаемый результат.** Должна отобразиться одна запись, аналогичная следующей: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`.

3. Установите агент SAP Host Agent, как описано в примечании к SAP [1031096], и проверьте выходные данные `saposcol`.

  a.  Запустите `/usr/sap/hostctrl/exe/saposcol -d`

  Б.  Запустите `dump ccm`

  c.  Проверьте, задано ли для метрики **Virtualization_Configuration\Enhanced Monitoring Access** значение **true**.

Если сервер приложений ABAP SAP NetWeaver уже установлен, откройте транзакцию ST06 и проверьте, включен ли расширенный мониторинг.

Сведения об устранении неполадок в случае сбоя любой из этих проверок, а также информацию о повторном развертывании этого расширения см. в разделе [Устранение неполадок инфраструктуры мониторинга Azure для SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Проверка работоспособности конфигурации инфраструктуры мониторинга Azure
Если некоторые данные мониторинга не предоставляются именно так, как указано в тесте, описанном в разделе [Проверка готовности расширения расширенного мониторинг Azure для SAP][deployment-guide-5.1], то выполните командлет `Test-AzureRmVMAEMExtension`, чтобы проверить, настроены ли параметры инфраструктуры мониторинга Azure и расширение мониторинга для SAP должным образом.

1.  Установите последнюю версию командлетов Azure PowerShell, как описано в разделе [Развертывание командлетов Azure PowerShell][deployment-guide-4.1].
2.  Выполните приведенный ниже командлет PowerShell. Чтобы просмотреть список доступных сред, выполните командлет `Get-AzureRmEnvironment`. Если нужно использовать глобальный экземпляр Azure, выберите среду **AzureCloud**. Для Azure в Китае выберите **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Connect-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  Введите данные учетной записи и укажите виртуальную машину Azure.

  ![Страница ввода командлета Azure Test-VMConfigForSAP_GUI для SAP][deployment-guide-figure-1200]

4. После этого сценарий тестирует конфигурацию выбранной виртуальной машины.

  ![Результат успешной проверки инфраструктуры мониторинга Azure для SAP][deployment-guide-figure-1300]

Убедитесь, что для каждой проверки установлено состояние **ОК**. Если состояние некоторых проверок не **ОК**, выполните командлет обновления, как описано в разделе [Настройка расширения расширенного мониторинг Azure для SAP][deployment-guide-4.5]. Подождите 15 минут и повторно запустите проверки, описанные в разделах [Проверка готовности расширения расширенного мониторинг Azure для SAP][deployment-guide-5.1] и [Проверка работоспособности конфигурации инфраструктуры мониторинга Azure][deployment-guide-5.2]. Если при проверке по-прежнему возникают проблемы с некоторыми или со всеми счетчиками, см. раздел [Устранение неполадок инфраструктуры мониторинга Azure для SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Устранение неполадок инфраструктуры мониторинга Azure для SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Счетчики производительности Azure вообще не отображаются
Сбор показателей производительности в Azure выполняет служба расширенного мониторинга Azure для Windows. Если она установлена неправильно или не запущена на виртуальной машине, сбор метрик производительности невозможен.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Каталог установки расширения для расширенного мониторинга Azure пустой

###### <a name="issue"></a>Проблема
Каталог установки C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;версия>\\drop пустой.

###### <a name="solution"></a>Решение
Расширение не установлено. Определите, связано ли это с неполадками прокси (как описано выше). Может потребоваться перезагрузить компьютер или повторно выполнить скрипт конфигурации `Set-AzureRmVMAEMExtension`.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Служба для расширенного мониторинга Azure не существует

###### <a name="issue"></a>Проблема
Служба расширенного мониторинга Azure для Windows отсутствует

В результате выполнения azperlib.exe отображается следующая ошибка:

![Выполнение файла azperflib.exe, в результате которого указано, что служба расширения для расширенного мониторинга Azure для SAP не запущена][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Решение
Если служба отсутствует, то расширение расширенного мониторинга Azure для SAP установлено неправильно. Разверните расширение повторно, выполнив действия для сценария развертывания, описанные в разделе [Сценарии развертывания ПО SAP на виртуальных машинах Azure][deployment-guide-3].

Через час после развертывания расширения снова проверьте, предоставляются ли счетчики производительности Azure в виртуальной машине Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Служба для расширенного мониторинга Azure существует, но не запускается

###### <a name="issue"></a>Проблема
Служба расширенного мониторинга Azure для Windows установлена и включена, но не запускается. Дополнительные сведения см. в журнале событий приложений.

###### <a name="solution"></a>Решение
Недопустимая конфигурация. Перезапустите расширение мониторинга Azure для виртуальной машины, как описано в разделе [Настройка расширения расширенного мониторинга Azure для SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Отсутствуют некоторые счетчики производительности Azure
Сбор показателей производительности в Azure выполняет служба расширенного мониторинга Azure для Windows. Она получает данные из нескольких источников. Некоторые данные конфигурации собираются локально, метрики производительности считываются из системы диагностики Azure, а счетчики хранилища используются из журналов на уровне подписки в хранилище.

Если после устранения неполадок с использованием примечания к SAP [1999351] проблема не исчезла, повторно запустите скрипт конфигурации `Set-AzureRmVMAEMExtension`. Необходимо подождать час, так как счетчики аналитики или диагностики хранилищ создаются не сразу после включения. Если проблема не исчезла, откройте сообщение о поддержке клиентов SAP в компоненте BC-OP-NT-AZR для виртуальной машины Windows или BC-OP-LNX-AZR для виртуальной машины Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Счетчики производительности Azure вообще не отображаются
Сбор показателей производительности в Azure выполняет управляющая программа. Если она не запущена, сбор метрик производительности не выполняется.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Каталог установки расширения для расширенного мониторинга Azure пустой

###### <a name="issue"></a>Проблема
Каталог \\var\\lib\\waagent\\ не содержит подкаталог для расширения расширенного мониторинга Azure.

###### <a name="solution"></a>Решение
Расширение не установлено. Определите, связано ли это с неполадками прокси (как описано выше). Может потребоваться перезагрузить компьютер и (или) повторно выполнить скрипт конфигурации `Set-AzureRmVMAEMExtension`.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Отсутствуют некоторые счетчики производительности Azure
Сбор показателей производительности в Azure выполняет управляющая программа, которая получает данные из нескольких источников. Некоторые данные конфигурации собираются локально, метрики производительности считываются из системы диагностики Azure, а счетчики хранилища используются из журналов на уровне подписки в хранилище.

Полный и актуальный список известных проблем см. в примечании SAP [1999351]. В нем содержатся дополнительные сведения об устранении неполадок расширенного мониторинга Azure для SAP.

Если после устранения неполадок с использованием примечания к SAP [1999351] проблема не исчезла, повторно выполните скрипт конфигурации `Set-AzureRmVMAEMExtension`, как описано в разделе [Настройка расширения расширенного мониторинг Azure для SAP][deployment-guide-4.5]. Необходимо подождать час, так как счетчики аналитики или диагностики хранилищ создаются не сразу после включения. Если проблема не исчезла, откройте сообщение о поддержке клиентов SAP в компоненте BC-OP-NT-AZR для виртуальной машины Windows или BC-OP-LNX-AZR для виртуальной машины Linux.
