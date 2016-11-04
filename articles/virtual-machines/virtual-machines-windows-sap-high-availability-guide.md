<properties
   pageTitle="SAP NetWeaver на виртуальных машинах Windows. Руководство по обеспечению высокого уровня доступности | Microsoft Azure"
   description="Руководство по обеспечению высокого уровня доступности для SAP NetWeaver на виртуальных машинах Windows"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="goraco"/>


# <a name="sap-netweaver-on-windows-virtual-machines-vms-highavailability-guide"></a>SAP NetWeaver на виртуальных машинах Windows. Руководство по обеспечению высокого уровня доступности

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbm
[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД)
[dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Кэширование для виртуальных машин и виртуальных жестких дисков)
[dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Программный RAID-массив)
[dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Служба хранилища Microsoft Azure)
[dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Структура развертывания реляционной СУБД)
[dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Высокая доступность и аварийное восстановление с использованием виртуальных машин Azure)
[dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 с пакетом обновления 1 (SP1), накопительным пакетом обновления 4 (CU4) и последующие выпуски)
[dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 с пакетом обновления 1 (SP1), накопительным пакетом обновления 3 (CU3) и более ранние выпуски)
[dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Использование образов SQL Server из Microsoft Azure Marketplace)
[dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Общая сводка по SQL Server для SAP в Azure)
[dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Особенности реляционных СУБД SQL Server)
[dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Конфигурация хранилища)
[dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Архивация и восстановление)
[dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Рекомендации по ускорению архивации и восстановления)
[dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Прочее)
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию)
[deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Материалы по SAP)
[deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Развертывание виртуальной машины с помощью пользовательского образа)
[deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Сценарий 1. Развертывание виртуальной машины для SAP из Azure Marketplace)
[deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Сценарий 2. Развертывание виртуальной машины с помощью пользовательского образа для SAP)
[deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Сценарий 3. Перемещение виртуальной машины из локальной среды с помощью специализированного виртуального жесткого диска Azure с SAP)
[deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Сценарии развертывания виртуальных машин для SAP в Microsoft Azure)
[deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Развертывание командлетов Azure PowerShell)
[deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Скачивание и импорт соответствующих командлетов PowerShell для SAP)
[deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Присоединение виртуальной машины к локальному домену (только для Windows))
[deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Скачивание, установка и включение агента виртуальной машины Azure)
[deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Интерфейс командной строки Azure)
[deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Расширенный мониторинг Azure для SAP: настройка расширения)
[deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Расширенный мониторинг Azure для SAP: проверка готовности)
[deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Конфигурация инфраструктуры мониторинга Azure: проверка работоспособности)
[deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению)
[planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Материалы)
[planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Высокая доступность и аварийное восстановление для SAP NetWeaver на виртуальных машинах Azure)
[planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Высокая доступность серверов приложений SAP)
[planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Использование автозапуска для экземпляров SAP)
[planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Только облако. Развертывание виртуальных машин в Azure без зависимостей в локальной сети клиента)
[planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Распределенная сеть. Развертывание одной или нескольких виртуальных машин SAP в Azure с необходимостью полной интеграции с локальной сетью)
[planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Регионы Azure)
[planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Домены сбоя)
[planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Домены обновления)
[planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Группы доступности Azure)
[planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Концепция виртуальных машин Microsoft Azure)
[planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Хранилище Azure класса Premium)
[planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Перемещение виртуальной машины из локальной среды в Azure с помощью специализированного диска)
[planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Развертывание виртуальной машины с помощью пользовательского образа)
[planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Подготовка виртуальной машины к перемещению из локальной среды в Azure с помощью специализированного диска)
[planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Подготовка виртуальной машины к развертыванию с помощью пользовательского образа для SAP)
[planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Подготовка виртуальных машин с SAP для Azure)
[planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Разница между диском Azure и образом Azure)[planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Отправка VHD-диска из локальной среды в Azure)
[planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Копирование дисков между учетными записями хранения Azure)
[planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Структура виртуальной машины и VHD для развертываний SAP)
[planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Настройка автоподключения для подключенных дисков)
[planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Обучающий сценарий с демонстрацией одной виртуальной машины с SAP NetWeaver)
[planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Концепция полностью облачного развертывания экземпляров SAP)
[planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Решение мониторинга Azure для SAP)
[planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Хранилище Azure класса Premium)

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
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa (Высокий уровень доступности SAP NetWeaver в IaaS Azure)
[sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e (Высокая доступность серверов приложений SAP)
[sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db (Высокая доступность экземпляра SAP ASCS/SCS)
[sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 (Обеспечение высокого уровня доступности экземпляра SAP ASCS/SCS с помощью отказоустойчивого кластера Windows Server в Azure)
[sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 (Высокий уровень доступности для экземпляра СУБД)
[sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 (Сценарии комплексного развертывания с высоким уровнем доступности)
[sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf (Подготовка инфраструктуры)
[sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 (Развертывание виртуальных машин с подключением к корпоративной сети (распределенное развертывание) для использования в рабочей среде)
[sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 (Полностью облачное развертывание экземпляров SAP для тестирования или демонстрации)
[sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Виртуальная сеть Azure)
[sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e (IP-адреса DNS)
[sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f (Имена узлов и статические IP-адреса для кластеризованного экземпляра SAP ASCS/SCS и кластеризованного экземпляра СУБД)
[sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 (Настройка статических IP-адресов для виртуальных машин SAP)
[sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e (Создание статического IP-адреса для внутреннего балансировщика нагрузки)
[sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c (Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure)
[sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 (Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure)
[sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c (Присоединение виртуальных машин Windows к домену)
[sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 (Добавление записей реестра для обоих узлов кластера, используемых для экземпляра SAP ASCS/SCS)
[sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab (Установка отказоустойчивого кластера Windows Server для экземпляра SAP ASCS/SCS)
[sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 (Сбор узлов кластера в конфигурации кластера)
[sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca (Настройка файлового ресурса-свидетеля кластера)
[sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 (Создание файлового ресурса)
[sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d (Настройка кворума на основе файлового ресурса-свидетеля в диспетчере отказоустойчивости кластеров)
[sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 (Установка SIOS DataKeeper Cluster Edition для создания общего диска кластера SAP ASCS/SCS)
[sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 (Добавление компонента .NET Framework 3.5)
[sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 (Установка SIOS DataKeeper)
[sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 (Настройка SIOS DataKeeper)
[sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b (Установка системы SAP NetWeaver)
[sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 (Установка SAP с высокодоступным экземпляром ASCS/SCS)
[sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 (Создание имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS)
[sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 (Установка первого узла кластера SAP)
[sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 (Изменение профиля SAP экземпляра ASCS/SCS)
[sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 (Добавление порта пробы)
[sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 (Установка экземпляра базы данных)
[sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 (Установка второго узла кластера)
[sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a (Изменение типа запуска службы Windows для экземпляра SAP ERS)
[sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 (Установка основного сервера приложений SAP)
[sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 (Установка дополнительного сервера приложений SAP)
[sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 (Тестирование отработки отказа экземпляра SAP ASCS/SCS и репликации SIOS)
[sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 (Экземпляр SAP ASCS/SCS выполняется на узле А кластера A)
[sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 (Процесс отработки отказа с узла A на узел B)
[sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 (Экземпляр SAP ASCS/SCS выполняется на узле B кластера))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
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


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
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
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
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
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


Виртуальные машины Azure позволяют организациям быстро получать вычислительные ресурсы, ресурсы хранилища и сетевые ресурсы без длительных циклов закупки. С помощью виртуальных машин Azure можно развернуть в Azure классические приложения, например приложения на основе SAP NetWeaver (ABAP, Java и ABAP со стеком Java), и повысить их надежность и доступность без локального развертывания ресурсов. Повысьте уровень надежности и доступности без привлечения дополнительных локальных ресурсов. Так как виртуальные машины Azure поддерживают распределенные подключения, ваша организация может интегрировать виртуальные машины Azure в свои локальные домены, частные облака и системный ландшафт SAP.

В этой статье описаны действия, необходимые для развертывания в Azure высокодоступных систем SAP посредством новой модели развертывания с помощью Azure Resource Manager. Здесь рассматриваются следующие основные задачи:

- Поиск соответствующих руководств по установке SAP и примечаний, которые приведены в разделе [Материалы][sap-ha-guide-2]. Это руководство дополняет документацию по установке SAP и комментарии по SAP, которые являются основными ресурсами по установке и развертыванию ПО SAP на определенных платформах.

- Понимание различий между классической моделью развертывания Azure и моделью развертывания с помощью Azure Resource Manager.

- Понимание режимов кворума отказоустойчивого кластера Windows Server, которое позволит выбрать модель, подходящую для развертывания Azure.

- Понимание принципов работы общего хранилища отказоустойчивого кластера Windows Server в Azure.

- Понимание того, как могут быть защищены в Azure компоненты SAP, представляющие собой единственную точку сбоя (например, SAP ASCS/SCS и СУБД), и избыточные компоненты (например, серверы приложений SAP).

- Выполнение пошаговых инструкций по установке и настройке высокодоступной системы SAP в отказоустойчивом кластере Windows Server с использованием платформы Azure и Azure Resource Manager.

- Дополнительные шаги, необходимые для использования WSFC в Azure, которые не требуется выполнять в локальных развертываниях.

Чтобы упростить развертывание и настройку, в этой статье используются новые шаблоны Resource Manager для трехуровневой конфигурации SAP с высоким уровнем доступности. Шаблоны автоматизируют развертывание полной инфраструктуры, необходимой для высокодоступной системы SAP. Эта инфраструктура поддерживает нужный размер SAPS для вашей системы SAP.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="a-name217c547955954cd8870d15ab00d4f84ca-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Предварительные требования

Перед началом работы необходимо выполнить все предварительные условия, описанные в следующих разделах. Кроме того, ознакомьтесь со всеми минералами, указанными в разделе [Материалы][sap-ha-guide-2].

В этой статье мы используем шаблоны Azure Resource Manager для [трехуровневой SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Обзор шаблонов представлен в статье о [шаблонах Azure Resource Manager для SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="a-name42b8f6007ba34606b8a553c4f026da08a-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Материалы

По теме развертывания SAP в Azure доступны перечисленные далее руководства.

- [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
- [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию][deployment-guide]
- [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide-2.1]
- [SAP NetWeaver на виртуальных машинах Windows. Руководство по обеспечению высокого уровня доступности [это руководство] ][sap-ha-guide]

> [AZURE.NOTE] При возможности указывается ссылка на соответствующее руководство по установке SAP (см. [руководства по установке SAP][sap-installation-guides]). Что касается необходимых компонентов и процесса установки, внимательно изучите руководства по установке SAP NetWeaver. В этой статье рассматриваются только отдельные задачи для систем на основе SAP NetWeaver, устанавливаемых на виртуальные машины Microsoft Azure.

Следующие примечания по SAP актуальны для развертывания SAP в Azure.

| Номер примечания   | Название                                                    
| ------------- |----------------------------------------------------------
| [1928533]       | Приложения SAP в Azure: поддерживаемые продукты и размеры
| [2015553]       | SAP в Microsoft Azure: требования         
| [1999351]       | Расширенный мониторинг Azure для SAP                        
| [2178632]       | Ключевые метрики мониторинга для SAP в Microsoft Azure        
| [1999351]       | Виртуализация в Windows: расширенный мониторинг      


Узнайте больше об [ограничениях для подписок Azure][azure-subscription-service-limits-subscription], включая общие ограничения по умолчанию и максимальные ограничения.

## <a name="a-name42156640c601cf45a9b2254baa678b24f1ahighavailability-sap-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Различия SAP с высоким уровнем доступности в модели развертывания Azure Resource Manager и классической модели развертывания

Между моделью развертывания с использованием и классической моделью развертывания существует два основных различия:

- Группы ресурсов
- Требования к кластеризации

### <a name="a-namef76af27319934d83b12d65deeae23686a-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Группы ресурсов
Группы ресурсов в Azure Resource Manager можно использовать для управления всеми ресурсами приложения в подписке Azure. В рамках интегрированного подхода в группе ресурсов все ресурсы имеют одинаковый цикл жизни. Например, все они создаются одновременно и удаляются тоже одновременно. Дополнительные сведения о группах ресурсов см. [здесь](resource-group-overview.md#resource-groups).

### <a name="a-name3e85fbe084b1489287afd9b65ff91860a-clustering-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Сравнение кластеризации в модели развертывания с использованием Azure Resource Manager и классической модели развертывания

В модели с использованием Azure Resource Manager нет необходимости настраивать облачную службу для использования внутреннего балансировщика нагрузки Azure в целях обеспечения высокого уровня доступности.

Если вы хотите использовать классическую модель Azure, то следуйте процедуре, описанной в документе [SSAP NetWeaver on Azure — Clustering SAP ASCS/SCS Instances using Windows Server Failover Cluster on Azure with SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver в Azure. Кластеризация экземпляров SAP ASCS/SCS с помощью отказоустойчивого кластера Windows Server в Azure и SIOS DataKeeper).

> [AZURE.NOTE] Для установки приложений SAP настоятельно рекомендуется использовать модель развертывания с помощью Azure Resource Manager. Она предлагает преимущества, недоступные в классической модели развертывания. Узнайте больше о [моделях развертывания][virtual-machines-azure-resource-manager-architecture-benefits-arm].   

## <a name="a-name8ecf3ba067c044959c14feec1a2255b7a-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Отказоустойчивый кластер Windows Server (WSFC)

Отказоустойчивая кластеризация Windows Server является основой для установки высокодоступных приложений SAP ASCS/SCS установки и СУБД в Windows.

Отказоустойчивый кластер представляет собой группу из 1 + n независимых серверов (узлов), работающих совместно для повышения доступности приложений и служб. В случае отказов узлов WSFC должен определить количество возможных сбоев, при котором сохраняется работоспособность кластера и предоставляются определенные приложения и службы. Для этого доступны разные режимы кворума.

### <a name="a-name1a3c5408b16846d699f54219ad1b1ff2a-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Режимы кворума

При использовании WSFC доступно четыре режима кворума.

- **Большинство узлов**. Голосовать может каждый узел кластера. Кластер работает только при большинстве голосов (более половины). Этот вариант рекомендуется при наличии нечетного числа узлов. Пример: в кластере из 7 узлов могут отказать 3 узла, при этом в нем сохранится большинство голосов и кластер продолжит работу.  

- **Большинство узлов и диска**. Голосовать может каждый узел и указанный диск в хранилище кластера (диск-свидетель), когда они доступны и могут обмениваться данными. Кластер работает только при большинстве голосов (более половины). Этот режим целесообразно использовать в среде кластера с четным числом узлов. Пока половина узлов и диск находятся в сети, кластер находится в работоспособном состоянии.

- **Большинство узлов и файлового ресурса**. Голосовать может каждый узел, а также указанный файловый ресурс, созданный администратором (файловый ресурс-свидетель), если они доступны и могут обмениваться данными. Кластер работает только при большинстве голосов (более половины). Этот режим целесообразно использовать в среде кластера с четным числом узлов. Он похож на режим большинства узлов и дисков, только вместо диска-свидетеля используется файловый ресурс-свидетель. Данный режим прост в реализации, но если файловый ресурс не является высокодоступным, то он может оказаться единственной точкой отказа.

- **Отсутствие большинства — только диск**. В кластере есть кворум, если доступен один узел, подключенный к определенному диску в хранилище кластера. Только узлы, которые также подключены к этому диску, могут присоединиться к кластеру. Использовать этот режим не рекомендуется.
 
## <a name="a-namefdfee8756e66483aa34314bbaee33275a-windows-server-failover-clustering-onpremises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Отказоустойчивый кластер Windows Server в локальной среде
В примере на рис. 1 показан кластер, состоящий из двух узлов. В случае отказа сетевого подключения между узлами, если оба из них работоспособны, необходимо выяснить, какой узел должен по-прежнему предоставлять приложения и службы кластера. Узел, у которого есть доступ к диску или файловому ресурсу кворума, должен обеспечить работоспособность служб.

Так как в этом примере используется кластер из двух узлов, был выбран режим кворума "Большинство узлов и файлового ресурса". Можно также использовать большинство узлов и дисков. В рабочей среде рекомендуется использовать диск кворума. Можно применить сетевую систему хранения, чтобы обеспечить его высокую доступность.

![Рис. 1. Пример конфигурации отказоустойчивого кластера Windows Server для SAP ASCS/SCS в Azure][sap-ha-guide-figure-1000]

_**Рис. 1.** Пример конфигурации отказоустойчивого кластера Windows Server для SAP ASCS/SCS в Azure_

### <a name="a-namebe21cf3efb01402b995554fbecf66592a-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Общее хранилище

На рис. 1 также показано общее хранилище кластера с двумя узлами. В кластере с общим хранилищем в локальной среде имеется общее хранилище, видимое для всех узлов в кластере. Механизм блокировки защищает данные от повреждения. Кроме того, каждый узел может обнаружить отказ другого узла. При сбое одного узла второй узел получает право на владение ресурсами хранилища и обеспечивает доступность служб.

> [AZURE.NOTE] В некоторых СУБД, например SQL Server, общие диски не требуются для достижения высокого уровня доступности. SQL Server AlwaysOn выполняет репликацию файлов данных и журналов СУБД с локального диска одного узла кластера на локальный диск другого узла кластера. Поэтому в конфигурации кластера Windows не нужен общий диск.

### <a name="a-nameff7a9a062bc54b20860a46cdb44669cda-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Сетевые подключения и разрешение имен

Клиентские компьютеры обращаются к кластеру посредством виртуального IP-адреса и имени виртуального узла, которое предоставляет DNS-сервер. Узлы находятся в локальной среде, и DNS-сервер может обрабатывать несколько IP-адресов.

В стандартной конфигурации используется от двух сетевых подключений:

- выделенное подключение к хранилищу;
- внутреннее сетевое подключение в кластере для передачи пульса;
- общедоступная сеть, используемая клиентами для подключения к кластеру.

## <a name="a-name2ddba413a7f54e4e9a5187908879c10aa-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Отказоустойчивый кластер Windows Server в Azure

По сравнению с развертываниями без операционной системы или развертываниями частных облаков виртуальные машины Azure требуют дополнительной настройки WSFC. При создании общего диска кластера экземпляру SAP ASCS/SCS потребуется задать несколько IP-адресов и имен виртуальных узлов.

В этой статье мы обсудим основные понятия и дополнительные действия, необходимые при создании в Azure кластера SAP Central Services с высоким уровнем доступности. Мы покажем, как настроить сторонний инструмент SIOS DataKeeper и внутренний балансировщик нагрузки Azure. С помощью этих инструментов можно создать отказоустойчивый кластер Windows с файловым ресурсом-свидетелем в Azure.


![Рис. 2. Конфигурация отказоустойчивого кластера Windows Server в Azure без общего диска][sap-ha-guide-figure-1001]

_**Рис. 2.** Конфигурация отказоустойчивого кластера Windows Server в Azure без общего диска_


### <a name="a-name1a464091922b48d79d087cecf757f341a-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Создание общего диска в Azure с помощью SIOS DataKeeper

Общее хранилище кластера необходимо для экземпляра SAP ASCS/SCS с высоким уровнем доступности. По состоянию на сентябрь 2016 г. Azure не предоставляет общее хранилище для создания кластера с общим хранилищем. Вы можете использовать стороннее ПО SIOS DataKeeper Cluster Edition, чтобы создать зеркальное хранилище, имитирующее общее хранилище кластера. Решение SIOS обеспечивает синхронную репликацию данных в реальном времени. Вот как можно создать общий дисковый ресурс для кластера.

1. Подключите дополнительный виртуальный жесткий диск (VHD) Azure к каждой виртуальной машине в конфигурации кластера Windows.
2. Запустите SIOS DataKeeper Cluster Edition на обоих узлах виртуальной машины.
3. Настройте ПО SIOS DataKeeper Cluster Edition таким образом, чтобы оно зеркально отображало содержимое тома дополнительного подключенного VHD исходной виртуальной машины в том дополнительного подключенного VHD целевой виртуальной машины. SIOS DataKeeper абстрагирует исходные и конечные локальные тома, а затем предоставляет их отказоустойчивому кластеру Windows как один общий диск.

Дополнительные сведения о SIOS DataKeeper см. [здесь](http://us.sios.com/products/datakeeper-cluster/).

 ![Рис. 3. Конфигурация отказоустойчивого кластера Windows Server в Azure с использованием SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Рис. 3.** Конфигурация отказоустойчивого кластера Windows Server в Azure с использованием SIOS DataKeeper_

> [AZURE.NOTE] В некоторых СУБД, например SQL Server, общие диски не требуются для достижения высокого уровня доступности. SQL Server AlwaysOn выполняет репликацию файлов данных и журналов СУБД с локального диска одного узла кластера на локальный диск другого узла кластера. Поэтому в конфигурации кластера Windows не нужен общий диск.

### <a name="a-name44641e18a94e431f95ff303ab65e0bcba-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Разрешение имен в Azure

 Облачная платформа Azure не предоставляет возможность настройки виртуальных IP-адресов, т. е. плавающих IP-адресов. Поэтому необходимо альтернативное решение для настройки виртуального IP-адреса, используемого для доступа к кластерному ресурсу в облаке.
Azure предоставляет внутренний балансировщик нагрузки в службе балансировки нагрузки Azure. С его помощью клиенты могут обращаться к кластеру через виртуальный IP-адрес кластера.
Внутренний балансировщик нагрузки необходимо развернуть в группе ресурсов, содержащей узлы кластера. Затем нужно настроить все необходимые правила перенаправления портов для портов проб внутреннего балансировщика нагрузки.
Клиенты могут подключаться через имя виртуального узла. DNS-сервер разрешает IP-адрес кластера, и внутренний балансировщик нагрузки выполняет перенаправление на активный узел кластера.

## <a name="a-name2e3fec50241e441b87080b1864f66dfaa-highavailability-sap-netweaver-in-azure-infrastructureasaservice-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Высокий уровень доступности SAP NetWeaver в IaaS Azure

Чтобы обеспечить высокий уровень доступности приложения SAP, т. е. компонентов программного обеспечения SAP, необходимо защитить указанные далее компоненты. Этот вопрос рассматривается более подробно в документе [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide-11].

- серверов приложений SAP,
- экземпляров ASCS/SCS SAP,
- сервер СУБД.

### <a name="a-name93faa747907e440ab00a1ae0a89b1c0ea-highavailability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Высокая доступность серверов приложений SAP

Как правило, для экземпляров серверов приложений или диалоговых экземпляров SAP специальное решение для обеспечения высокой доступности не требуется. Высокий уровень доступности достигается за счет избыточности, то есть на разных экземплярах виртуальных машин Azure настраивается несколько диалоговых экземпляров. Необходимо установить как минимум два экземпляра приложения SAP на два экземпляра виртуальных машин Azure.

![Рис. 4. Высокая доступность серверов приложений SAP][sap-ha-guide-figure-2000]

_**Рис. 4.** Высокая доступность серверов приложений SAP_

Все виртуальные машины, на которых размещены серверы приложений SAP, должны находиться в одной группе доступности Azure. Группа доступности Azure гарантирует следующее.

- Все виртуальные машины входят в одни и те же домены обновления. Домен обновления, например, позволяет избежать одновременного обновления виртуальных машин во время планового обслуживания.
- Все виртуальные машины входят в одни и те же домены сбоя. Домен сбоя, например, позволяет избежать развертывания виртуальных машин таким образом, что единая точка отказа сможет влиять на доступность всех виртуальных машин.

См. статью о том, как [управлять доступностью виртуальных машин][virtual-machines-manage-availability].

Так как учетная запись хранения Azure является потенциальной единственной точкой отказа, важно иметь по меньшей мере две учетные записи хранения Azure, в которых будут распределены как минимум две виртуальные машины. В идеале все виртуальные машины, на которых выполняются диалоговые экземпляры SAP, следует развертывать в разных учетных записях хранения.

### <a name="a-namef559c285ee684eecadd1f60fe7b978dba-highavailability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Высокая доступность экземпляра SAP ASCS/SCS

![Рис. 5. Высокая доступность экземпляра SAP ASCS/SCS][sap-ha-guide-figure-2001]

_**Рис. 5.** Высокая доступность экземпляра SAP ASCS/SCS_


#### <a name="a-nameb5b1fd0b1db44d499162de07a0132a51a-highavailability-sap-ascsscs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Обеспечение высокого уровня доступности экземпляра SAP ASCS/SCS с помощью отказоустойчивого кластера Windows Server в Azure

По сравнению с развертываниями без операционной системы или развертываниями частных облаков виртуальные машины Azure требуют дополнительной настройки WSFC. Чтобы создать отказоустойчивый кластер Windows для кластеризации экземпляра SAP ASCS/SCS, требуется общий диск кластера, несколько IP-адресов и имен виртуальных узлов, а также внутренний балансировщик нагрузки Azure.

Этот вопрос рассматривается более подробно далее в этом документе.

![Рис. 6. Конфигурация отказоустойчивого кластера Windows Server для SAP ASCS/SCS в Azure с использованием SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Рис. 6.** Конфигурация отказоустойчивого кластера Windows Server для SAP ASCS/SCS в Azure с использованием SIOS DataKeeper_


### <a name="a-nameddd878a09c2f4b8e896826ce60be1027a-highavailability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Высокий уровень доступности экземпляра СУБД

СУБД также является единой точкой отказа системы SAP. Она должна быть защищена с помощью решения для обеспечения высокого уровня доступности. На рис. 7 показан пример решения для обеспечения высокого уровня доступности SQL Server AlwaysOn в Azure, в котором используется отказоустойчивый кластер Windows Server и внутренний балансировщик нагрузки Azure. SQL Server AlwaysOn реплицирует файлы данных и журналов СУБД с помощью собственной функции репликации СУБД. Поэтому вам не требуются общие диски кластера, что упрощает всю конфигурацию.

![Рис. 7. Пример сервера СУБД SAP с высоким уровнем доступности: SQL Server Always On][sap-ha-guide-figure-2003]

_**Рис. 7.** Пример сервера СУБД SAP с высоким уровнем доступности: SQL Server Always On_


Дополнительные сведения о кластеризации SQL Server в Azure посредством модели развертывания с помощью Azure Resource Manager можно найти в следующих статьях:

- [Ручная настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Настройка внутреннего балансировщика нагрузки для группы доступности AlwaysOn в Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="a-name045252ed02774fc88f46c5a29694a816a-endtoend-highavailability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Сценарии комплексного развертывания с высоким уровнем доступности

На рис. 8 показан пример архитектуры SAP NetWeaver с высоким уровнем доступности в Azure. В этом сценарии используется один выделенный кластер для экземпляра SAP ASCS/SCS и еще один — для СУБД.

![Рис. 8. Шаблон 1 архитектуры SAP с высоким уровнем доступности с выделенным кластером для ASCS/SCS и выделенным кластером для экземпляра СУБД][sap-ha-guide-figure-2004]

_**Рис. 8.** Шаблон 1 архитектуры SAP с высоким уровнем доступности с выделенным кластером для ASCS/SCS и выделенным кластером для экземпляра СУБД_

## <a name="a-name78092dbe165b454c92f54972bdbef9bfa-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Подготовка инфраструктуры

Шаблоны Azure Resource Manager для SAP помогают упростить развертывание необходимых ресурсов.

Эти трехуровневые шаблоны также поддерживают сценарии обеспечения высокого уровня доступности, например, шаблон 1 содержит два кластера. Каждый кластер является единой точкой отказа SAP для SAP ASCS/SCS и СУБД.

Шаблоны Azure Resource Manager для сценария 1 доступны здесь:

- [Образ Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
- [Пользовательский образ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Если щелкнуть трехуровневый образ SAP из Marketplace, на портале Azure появится приведенный ниже экран.

![Рис. 9. Указание параметров Azure Resource Manager для SAP с высоким уровнем доступности][sap-ha-guide-figure-3000]

_**Рис. 9.** Указание параметров Azure Resource Manager для SAP с высоким уровнем доступности_


Для параметра **SYSTEMAVAILABILITY** выберите значение **HA**.

Этот шаблон создаст:

- **Виртуальные машины**:
    - виртуальные машины сервера приложений SAP: <*SAPSystemSID*>-di-<*Number*>;
    - виртуальные машины кластера SCS/SCS <*SAPSystemSID*>-ascs-<*Number*>;
    - виртуальные машины кластера СУБД: <*SAPSystemSID*>-db-<*Number*>.
- **Сетевые карты для всех виртуальных машин с соответствующими IP-адресами**:
    - <*SAPSystemSID*>-nic-di-<*Number*>;
    - <*SAPSystemSID*>-nic-ascs-<*Number*>;
    - <*SAPSystemSID*>-nic-db-<*Number*>.
- **Учетные записи хранения Azure**
- **Группы доступности** для:
    - виртуальных машин сервера приложений SAP: <*SAPSystemSID*>-avset-di;
    - виртуальных машин кластера SCS/SCS: <*SAPSystemSID*>-avset-ascs;
    - виртуальных машин кластера СУБД: <*SAPSystemSID*>-avset-db.
- **Внутренний балансировщик нагрузки Azure**:
  - со всеми портами для экземпляра ASCS/SCS и IP-адресом: <*SAPSystemSID*>-lb-ascs;
  - со всеми портами для СУБД SQL Server и IP-адресом: <*SAPSystemSID*>-lb-db.
- **Группа безопасности сети**: <*SAPSystemSID*>-nsg-ascs-0;  
    - с открытым внешним портом RDP для виртуальной машины: <*SAPSystemSID*>-ascs-0.


> [AZURE.NOTE] Все IP-адреса сетевых карт и внутренних балансировщиков нагрузки Azure изначально создаются как **динамические**. Их необходимо изменить на **статические** IP-адреса. Процедура будет описана далее.


### <a name="a-namec87a8d3fb1dc4d2fb23cda4b72977489a-deploy-virtual-machines-with-corporate-network-connectivity-crosspremises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Развертывание виртуальных машин с подключением к корпоративной сети (распределенное развертывание) для использования в рабочей среде

Для производственных систем SAP виртуальные машины Azure развертываются с [подключением к корпоративной сети (распределенное развертывание)][planning-guide-2.2] с помощью VPN типа "сеть — сеть" Azure или канала Azure ExpressRoute.

> [AZURE.NOTE] Можно использовать имеющийся экземпляр виртуальной сети Azure. Виртуальная сеть и подсеть уже созданы и подготовлены.

В поле **NEWOREXISTINGSUBNET** выберите **existing**.

В текстовом поле **SUBNETID** введите полную строку SubnetID подготовленной сети Azure, в которой планируется развертывание виртуальных машин Azure.

Чтобы получить список всех подсетей в сети Azure, выполните эту команду PowerShell:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

В поле **ID** отображается **SUBNETID**.

Список всех значений **SUBNETID** можно получить с помощью этой команды PowerShell:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

**SUBNETID** выглядит следующим образом:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="a-name7fe9af0e3cce495ba5ecdcb4d8e0a310a-cloudonly-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Полностью облачное развертывание экземпляров SAP для тестирования или демонстрации

Систему SAP с высоким уровнем доступности можно также развернуть в модели полностью облачного развертывания.

Этот тип развертывания больше подходит для демонстрации или тестирования использования. Он не поддерживается для использования в рабочей среде.

В поле **NEWOREXISTINGSUBNET** выберите **new**. Оставьте поле **SUBNETID** пустым.

Виртуальная сеть и подсеть Azure создадутся автоматически с помощью шаблона Azure Resource Manager для SAP.

> [AZURE.NOTE] Кроме того, необходимо развернуть по меньшей мере одну выделенную виртуальную машину для служб AD и DNS в этой же виртуальной сети. Эти виртуальные машины не создаются с помощью шаблона.

### <a name="a-name47d5300aa83041d483dd1a0d1ffdbe6aa-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Виртуальная сеть Azure

В нашем примере адресное пространство виртуальной сети Azure — 10.0.0.0/16. Существует одна подсеть **Subnet** с диапазоном адресов 10.0.0.0/24. Все виртуальные машины и внутренние балансировщики нагрузки развертываются в этой виртуальной сети.

> [AZURE.NOTE] Не изменяйте параметры сети в гостевой ОС. Например, IP-адреса, DNS-серверы и подсети. Все параметры сети настраиваются с помощью Azure. Они распространяются через службу DHCP.

### <a name="a-nameb22d7b3b434340ffa319097e13f62f9ea-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> IP-адреса DNS

Убедитесь, что для параметра виртуальной сети **DNS-серверы** задано значение **Пользовательская служба DNS**.
Затем выберите параметры, в зависимости от типа сети:

- [Подключения к корпоративной сети (распределенное развертывание)][planning-guide-2.2]: добавьте IP-адреса локальных DNS-серверов.  

    Локальные DNS-серверы могут быть расширены до виртуальных машин в Azure. В этом случае можно добавить IP-адреса этих виртуальных машин Azure, на которых запущена служба DNS.

-   [Полностью облачное развертывание][planning-guide-2.1]: разверните дополнительную виртуальную машину в той же виртуальной сети, которая будет выполнять функции DNS-серверов. Добавьте IP-адреса этих виртуальных машин Azure, настроенных для запуска службы DNS.


![Рис. 10. Настройка DNS-серверов для виртуальной сети Azure][sap-ha-guide-figure-3001]

_**Рис. 10.** Настройка DNS-серверов для виртуальной сети Azure_

> [AZURE.NOTE] В случае изменения IP-адресов DNS-серверов необходимо перезагрузить виртуальные машины Azure, чтобы применить изменения и распространить новые адреса DNS-серверов.

В нашем примере служба DNS установлена и настроена на следующих виртуальных машинах Windows.

| Роль виртуальной машины        | Имя узла виртуальной машины | Имя сетевой карты  | Статический IP-адрес  
| ---------------|-------------|--------------------|-------------------
| 1-й DNS-сервер | domcontr-0  | pr1-nic-domcontr-0 | 10.0.0.10         
| 2-й DNS-сервер | domcontr-1  | pr1-nic-domcontr-1 | 10.0.0.11         


### <a name="a-name9fbd43c05850496597262a921d85d73fa-host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Имена узлов и статические IP-адреса для кластеризованного экземпляра SAP ASCS/SCS и кластеризованного экземпляра СУБД

Для локального развертывания необходимы следующие зарезервированные имена узлов и IP-адреса.

| Роль имени виртуального узла                                                       | Имя виртуального узла | Виртуальный статический IP-адрес
| ----------------------------------------------------------------------------|------------------|---------------------------
| Имя 1-го виртуального узла кластера SAP ASCS/SCS (используется для управления кластером) | pr1-ascs-vir     | 10.0.0.42                 
| Имя виртуального узла экземпляра SAP ASCS/SCS                                  | pr1-ascs-sap     | 10.0.0.43             
| Имя 2-го виртуального узла кластера SAP ASCS/SCS (используется для управления кластером)     | pr1-dbms-vir     | 10.0.0.32                 

Имена виртуальных узлов **pr1-ascs-vir** и **pr1-dbms-vir** и связанные IP-адреса, используемые для управления кластером, создаются вместе с кластером. Процесс описан в разделе [Сбор узлов кластера в конфигурации кластера][sap-ha-guide-8.12.1].

Два других имени виртуальных узлов — **pr1-ascs-sap** и **pr1-dbms-sap** — и связанные IP-адреса можно вручную создать на DNS-сервере. Эти ресурсы используются кластеризованным экземпляром SAP ASCS/SCS и кластеризованным экземпляром СУБД. Процесс описан в разделе [Создание имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="a-name84c019fe8c584dac9e54173efd4b2c30a-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Настройка статических IP-адресов для виртуальных машин SAP

После развертывания виртуальных машин для использования в кластере необходимо настроить статические IP-адреса для всех виртуальных машин. Это невозможно сделать в гостевой ОС. Задача выполняется в конфигурации виртуальной сети Azure.

Одним из способов настройки статических IP-адресов является использование портала Azure. На портале Azure последовательно выберите **Группа ресурсов** > **Сетевая карта** > **Параметры** > **IP-адрес**.

В поле **Назначение** выберите **Статический**. В поле **IP-адрес** введите нужный IP-адрес.

> [AZURE.NOTE] В случае изменения IP-адреса сетевой карты необходимо перезапустить виртуальные машины Azure, чтобы применить изменение.  


![Рис. 11. Настройка статического IP-адреса для сетевой карты каждой виртуальной машины][sap-ha-guide-figure-3002]

_**Рис. 11.** Настройка статического IP-адреса для сетевой карты каждой виртуальной машины_

Повторите этот шаг для всех сетевых интерфейсов, т. е. для всех виртуальных машин, включая те, которые вы хотите использовать для служб Active Directory и DNS.

В нашем примере используются следующие виртуальные машины и статические IP-адреса.

| Роль виртуальной машины                                 | Имя узла виртуальной машины  | Имя сетевой карты  | Статический IP-адрес  
| ----------------------------------------|--------------|--------------------|-------------------
| 1-й сервер приложений SAP              | pr1-di-0     | pr1-nic-di-0       | 10.0.0.50         
| 2-й сервер приложений SAP              | pr1-di-1     | pr1-nic-di-1       | 10.0.0.51         
| ...                                     | ...          | ...                | ...               
| Последний сервер приложений SAP             | pr1-di-5     | pr1-nic-di-5       | 10.0.0.55         
| 1-й узел кластера для экземпляра ASCS/SCS  | pr1-ascs-0   | pr1-nic-ascs-0     | 10.0.0.40         
| 2-й узел кластера для экземпляра ASCS/SCS  | pr1-ascs-1   | pr1-nic-ascs-1     | 10.0.0.41         
| 1-й узел кластера для экземпляра СУБД      | pr1-db-0     | pr1-nic-db-0       | 10.0.0.30         
| 2-й узел кластера для экземпляра СУБД      | pr1-db-1     | pr1-nic-db-1       | 10.0.0.31         

### <a name="a-name7a8f3e9b062440519e41b73fff816a9ea-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Настройка статического IP-адреса для внутреннего балансировщика нагрузки

Шаблон Azure Resource Manager для SAP создает внутренний балансировщик нагрузки Azure, используемый для кластера экземпляров SAP ASCS/SCS и кластера СУБД.

При первоначальном развертывании для внутреннего балансировщика нагрузки задается **динамический** IP-адрес. Необходимо изменить его на **статический**.

В нашем примере есть два внутренних балансировщика нагрузки Azure со следующими статическими IP-адресами.

| Роль внутреннего балансировщика нагрузки Azure             | Имя внутреннего балансировщика нагрузки Azure | Статический IP-адрес
| ---------------------------|----------------|-------------------
| Внутренний балансировщик нагрузки экземпляра SAP ASCS/SCS  | pr1-lb-ascs    | 10.0.0.43         
| Внутренний балансировщик нагрузки экземпляра СУБД SAP               | pr1-lb-dbms    | 10.0.0.33         


> [AZURE.NOTE] IP-адрес имени виртуального узла для SAP ASCS/SCS совпадает с IP-адресом внутреннего балансировщика нагрузки SAP ASCS/SCS, pr1-lb-ascs.
IP-адрес виртуального имени СУБД IP-адресом внутреннего балансировщика нагрузки СУБД, pr1-lb-dbms.

В нашем примере необходимо задать для внутреннего балансировщика нагрузки **pr1-lb-ascs** IP-адрес имени виртуального узла экземпляра SAP ASCS/SCS (**10.0.0.43**).

![Рис. 12. Настройка статического IP-адреса внутреннего балансировщика нагрузки для экземпляра SAP ASCS/SCS][sap-ha-guide-figure-3003]

_**Рис. 12.** Настройка статического IP-адреса внутреннего балансировщика нагрузки для экземпляра SAP ASCS/SCS_

Задайте для внутреннего балансировщика нагрузки **pr1-lb-dbms** IP-адрес имени виртуального узла экземпляра СУБД (в нашем примере это **10.0.0.33**).

### <a name="a-namef19bd997154d4583a46e7f5a69d0153ca-default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure

По умолчанию шаблон Azure Resource Manager для SAP создает все необходимые порты для следующих экземпляров:

- экземпляра ABAP ASCS с номером экземпляра по умолчанию **00**;
- экземпляра Java SCS с номером экземпляра по умолчанию **01**.

При установке экземпляра SAP ASCS/SCS необходимо использовать номер экземпляра по умолчанию 00 для экземпляра ABAP ASCS и номер экземпляра по умолчанию 01 для экземпляра Java SCS.

Затем создайте эти необходимые конечные точки внутренней балансировки нагрузки для портов SAP NetWeaver ABAP ASCS.

| Служба, имя правила балансировки нагрузки           | Номера портов по умолчанию | Определенные порты для (экземпляра ASCS с номером экземпляра 00) (ERS с номером 10)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Сервер постановки в очередь, _lbrule3200_                | 32<*InstanceNumber*>  | 3200                                                                     
| Сервер сообщений ABAP, _lbrule3600_           | 36<*InstanceNumber*>  | 3600                                                                     
| Внутренняя служба сообщений ABAP, _lbrule3900_         | 39<*InstanceNumber*>  | 3900                                                                     
| Сервер сообщений (HTTP), _Lbrule8100_           | 81<*InstanceNumber*>  | 8100                                                                     
| Служба запуска SAP ASCS (HTTP), _Lbrule50013_  | 5<*InstanceNumber*>13 | 50013                                                                    
| Служба запуска SAP ASCS (HTTPS), _Lbrule50014_ | 5<*InstanceNumber*>14 | 50014                                                                    
| Служба постановки в очередь для репликации, _Lbrule50016_          | 5<*InstanceNumber*>16 | 50016                                                                    
| Служба запуска SAP ERS (HTTP), _Lbrule51013_     | 5<*InstanceNumber*>13 | 51013                                                                    
| Служба запуска SAP ERS (HTTPS), _Lbrule51014_     | 5<*InstanceNumber*>14 | 51014                                                                    
| Win RM, _Lbrule5985_                          |                       | 5985                                                                     
| Общий доступ к файлам, _Lbrule445_                       |                       | 445                                                                      

_**Таблица 1.** Номера портов экземпляров SAP NetWeaver ABAP ASCS_


Затем создайте эти необходимые конечные точки внутренней балансировки нагрузки для портов SAP NetWeaver Java SCS.

| Служба, имя правила балансировки нагрузки           | Номера портов по умолчанию | Определенные порты для (экземпляра SCS с номером экземпляра 01) (ERS с номером 11)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Сервер постановки в очередь, _lbrule3201_                | 32<*InstanceNumber*>  | 3201                                                                     
| Сервер шлюза, _lbrule3301_                | 33<*InstanceNumber*>  | 3301                                                                     
| Сервер сообщений Java, _lbrule3900_           | 39<*InstanceNumber*>  | 3901                                                                     
| Сервер сообщений (HTTP), _Lbrule8101_           | 81<*InstanceNumber*>  | 8101                                                                     
| Служба запуска SAP SCS (HTTP), _Lbrule50113_   | 5<*InstanceNumber*>13 | 50113                                                                    
| Служба запуска SAP SCS (HTTPS), _Lbrule50114_  | 5<*InstanceNumber*>14 | 50114                                                                    
| Служба постановки в очередь для репликации, _Lbrule50116_          | 5<*InstanceNumber*>16 | 50116                                                                    
| Служба запуска SAP ERS (HTTP), _Lbrule51113_     | 5<*InstanceNumber*>13 | 51113                                                                    
| Служба запуска SAP ERS (HTTPS), _Lbrule51114_     | 5<*InstanceNumber*>14 | 51114                                                                    
| Win RM, _Lbrule5985_                          |                       | 5985                                                                     
| Общий доступ к файлам, _Lbrule445_                       |                       | 445                                                                      

_**Таблица 2.** Номера портов экземпляров SAP NetWeaver Java SCS_


![Рис. 13. Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure][sap-ha-guide-figure-3004]

_**Рис. 13.** Правила балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure_

Задайте для балансировщика нагрузки **pr1-lb-dbms** IP-адрес имени виртуального узла экземпляра СУБД (в нашем примере это **10.0.0.33**).

### <a name="a-namefe0bd8b52b4345e3829580bee5415716a-change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure

Если вы хотите использовать другие номера для экземпляров SAP ASCS или SCS, необходимо обновить имена и значения их портов.

Это можно сделать на портале Azure.

Последовательно выберите **<*SID*> балансировщик нагрузки -lb-ascs** > **правила балансировки нагрузки**.

Для всех правил балансировки нагрузки, относящихся к экземпляру SAP ASCS или SCS, измените следующие значения.

- Имя
- Порт
- Внутренний порт

Например, если требуется изменить номер экземпляра ASCS по умолчанию с 00 на 31, то необходимо внести изменения для всех портов, указанных в таблице 1.

Ниже приведен пример обновления _lbrule3200_для порта.

![Рис. 14. Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure][sap-ha-guide-figure-3005]

_**Рис. 14.** Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure_

### <a name="a-namee69e9a34460147a3a41cd2e11c626c0ca-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Присоединение виртуальных машин Windows к домену

После назначения виртуальным машинам статических IP-адресов добавьте эти машины в домен.

![Рис. 15. Добавление виртуальной машины в домен][sap-ha-guide-figure-3006]

_**Рис. 15.** Добавление виртуальной машины в домен_

### <a name="a-name661035b24d0f4d3186f8dc0a50d78158a-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Добавление записей реестра для обоих узлов кластера экземпляра SAP ASCS/SCS

Azure Load Balancer имеет внутренний балансировщик нагрузки, который закрывает подключения, если они простаивают определенный период времени (время простоя). Рабочие процессы SAP в диалоговых экземплярах открывают подключения к процессу постановки в очередь SAP при первой же необходимости отправить первый запрос на постановку в очередь или вывод из очереди. Эти подключения обычно сохраняются до перезапуска рабочего процесса или процесса постановки в очередь. Однако если подключение бездействует в течение некоторого времени, внутренний балансировщик нагрузки Azure закроет его. Это не является проблемой, так как рабочие процессы SAP повторно установят подключение к процессу постановки в очередь, если обнаружат его отсутствие. Эти действия будут описаны в трассировках разработчика для процессов SAP, но это приведет к созданию большого объема содержимого в этих трассировках. Поэтому рекомендуется изменить параметры TCP/IP `KeepAliveTime` и `KeepAliveInterval` на обоих узлах кластера. Необходимо также изменить параметры профиля SAP, которые будут описаны далее в этом документе.

Добавьте следующие записи реестра Windows на обоих узлах кластера Windows для SAP ASCS/SCS.

| Путь                  | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters   
| ----------------------|-----------------------------------------------------------
| Имя переменной         | `KeepAliveTime`                                              
| Тип переменной         | REG_DWORD (десятичное)                                        
| Значение                 | 120000                                                     
| Ссылка на документацию | [https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx)


_**Таблица 3.** Изменение первого параметра TCP/IP_


| Путь                  | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters   
| ----------------------|-----------------------------------------------------------
| Имя переменной         | `KeepAliveInterval`                                          
| Тип переменной         | REG_DWORD (десятичное)                                        
| Значение                 | 120000                                                     
| Ссылка на документацию | [https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx)


_**Таблица 4.** Изменение второго параметра TCP/IP_

Чтобы применить изменения, перезапустите оба узла кластера.

### <a name="a-name0d67f090792843e087725ccbf8f59aaba-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Установка отказоустойчивого кластера Windows Server для экземпляра SAP ASCS/SCS

#### <a name="a-name5eecb071c7034cccba6dfe9c6ded9d79a-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Сбор узлов кластера в конфигурации кластера

Первым шагом является добавление компонента отказоустойчивого кластера на оба узла кластера. Это делается с помощью мастера добавления ролей и компонентов.

Вторым шагом будет настройка отказоустойчивого кластера с помощью диспетчера отказоустойчивости кластеров Windows.

В диспетчере отказоустойчивости кластеров щелкните **Создать кластер** и добавьте только имя первого узла A кластера, например **pr1-ascs-0**. Пока не добавляйте второй узел. Он будет добавлен позже.

![Рис. 16. Добавление имени сервера или виртуальной машины для первого узла кластера][sap-ha-guide-figure-3007]

_**Рис. 16.** Добавление имени сервера или виртуальной машины для первого узла кластера_

Далее будет предложено ввести сетевое имя кластера (имя виртуального узла).

![Рис. 17. Определение имени кластера][sap-ha-guide-figure-3008]

_**Рис. 17.** Определение имени кластера_


После создания кластера запустите проверочный тест.

![Рис. 18. Проверка кластера][sap-ha-guide-figure-3009]

_**Рис. 18.** Проверка кластера_

![Рис. 19. Предупреждение об отсутствии диска кворума][sap-ha-guide-figure-3010]

_**Рис. 19.** Предупреждение об отсутствии диска кворума_

На этом этапе можно игнорировать любые предупреждения о дисках. Файловый ресурс-свидетель и общие диски SIOS будут добавлены позже. На этом этапе кворум нам не важен.

![Рис. 20. Определение основного ресурса кластера с IP-адресом (требуется новый IP-адрес)][sap-ha-guide-figure-3011]

_**Рис. 20.** Определение основного ресурса кластера с IP-адресом (требуется новый IP-адрес)_

Так как IP-адрес сервера указывает на один из узлов виртуальных машин, кластер не может запуститься. Нужно изменить IP-адрес основной службы кластера.

Например, нужно назначить IP-адрес (в нашем примере — **10.0.0.42**) для имени виртуального узла кластера **pr1-ascs-vir**. Это можно сделать в окне свойств ресурса IP-адреса основной службы кластера, как показано на рис. 21.

![Рис. 21. Использование окна **Свойства** для изменения IP-адреса][sap-ha-guide-figure-3012]

_**Рис. 21**. Использование окна **Свойства** для изменения IP-адреса_

![Рис. 22. Назначение IP-адреса, зарезервированного для кластера][sap-ha-guide-figure-3013]

_**Рис. 22.** Назначение IP-адреса, зарезервированного для кластера_

После изменения IP-адреса подключите имя виртуального узла кластера к сети.

![Рис. 23. Основная служба кластера запущена и работает с правильным IP-адресом][sap-ha-guide-figure-3014]

_**Рис. 23.** Основная служба кластера запущена и работает с правильным IP-адресом_

Теперь, когда основная служба кластера запущена и работает, можно добавить второй узел кластера.

![Рис. 24. Добавление второго узла кластера][sap-ha-guide-figure-3015]

_**Рис. 24.** Добавление второго узла кластера_

![Рис. 25. Добавление имени второго узла кластера, например pr1-ascs-1][sap-ha-guide-figure-3016]

_**Рис. 25.** Добавление имени второго узла кластера, например **pr1-ascs-1**_

![Рис. 26. Не следует устанавливать указанный флажок!][sap-ha-guide-figure-3017]

_**Рис. 26.** *Не* следует устанавливать указанный флажок!_

> [AZURE.IMPORTANT] Убедитесь, что флажок **Добавление всех допустимых хранилищ в кластер** для кластера *не* установлен.  

![Рис. 27. Предупреждение о диске кворума можно игнорировать][sap-ha-guide-figure-3018]

_**Рис. 27.** Предупреждение о диске кворума можно игнорировать_

Предупреждения о кворуме и дисках можно игнорировать. Настройка диска кворума и общих дисков будет выполнена позже, как описано в разделе [Установка SIOS DataKeeper Cluster Edition для создания общего диска кластера SAP ASCS/SCS][sap-ha-guide-8.12.3].

#### <a name="a-namee49a452950c94dcfbde715a0c21d21caa-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Настройка файлового ресурса-свидетеля кластера

##### <a name="a-name06260b30d6974c4db1c9d22c0bd64855a-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Создание файлового ресурса

Вместо диска кворума нужно выбрать файловый ресурс-свидетель. SIOS DataKeeper поддерживает такой вариант.

В примерах в этой статье файловый ресурс-свидетель настроен на сервере Active Directory или DNS-сервере, работающем в Azure. Этот файловый ресурс-свидетель называется **domcontr 0**. Так как вам пришлось бы настроить VPN-подключение к Azure (подключение типа "сеть — сеть" или канал ExpressRoute), то службы Active Director и DNS находились бы в локальной среде и поэтому не подошли бы для работы файлового ресурса-свидетеля.

> [AZURE.NOTE] Если службы Active Directory и DNS работают только в локальной среде, не следует настраивать файловый ресурс-свидетель в операционной системе Windows для служб Active Directory и DNS, запущенной локально. Задержка сети между узлами кластера, работающими в Azure, и локальной средой служб Active Directory и DNS, может быть слишком большой и вызвать проблемы с подключением. Файловый ресурс-свидетель следует настроить на виртуальной машине Azure, размещенной близко от узла кластера.  

На диске кворума должно не менее 1024 МБ свободного места. Рекомендуемое значение — 2048 МБ.

Первым шагом является добавление объекта имени кластера.

![Рис. 28. Назначение разрешений для общего ресурса для объекта имени кластера][sap-ha-guide-figure-3019]

_**Рис. 28.** Назначение разрешений для общего ресурса для объекта имени кластера_

Убедитесь, что разрешения позволяют изменять данные в общем ресурсе для объекта имени кластера (в нашем примере это **pr1-ascs-vir$**). Чтобы добавить объект имени кластера в список, нажмите кнопку **Добавить**. Измените фильтр для добавления объектов-компьютеров дополнительно к показанным на рис. 29.

![Рис. 29. Изменение типа объекта для добавления объектов-компьютеров][sap-ha-guide-figure-3020]

_**Рис. 29.** Изменение типа объекта для добавления объектов-компьютеров_

![Рис. 30. Установка флажка для объектов-компьютеров][sap-ha-guide-figure-3021]

_**Рис. 30.** Установка флажка для объектов-компьютеров_

После этого введите объект имени кластера, как показано на рис. 29. Так как запись уже создана, можно изменить разрешения, как показано на рис. 28.

Затем откройте вкладку **Безопасность** общего ресурса и определите более детальные разрешения для объекта имени кластера.

![Рис. 31. Задание атрибутов безопасности объекта имени кластера для кворума на основе файлового ресурса][sap-ha-guide-figure-3022]

_**Рис. 31.** Задание атрибутов безопасности объекта имени кластера для кворума на основе файлового ресурса_

##### <a name="a-name4c08c38778a046b19d27b497b08cac3da-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Настройка кворума на основе файлового ресурса-свидетеля в диспетчере отказоустойчивости кластеров

В диспетчере отказоустойчивости кластеров измените конфигурацию кластера для добавления файлового ресурса-свидетеля.

![Рис. 32. Запуск мастера настройки кворума кластера][sap-ha-guide-figure-3023]

_**Рис. 32.** Запуск мастера настройки кворума кластера_

![Рис. 33. Экран выбора различных конфигураций кворума][sap-ha-guide-figure-3024]

_**Рис. 33.** Экран выбора различных конфигураций кворума_

Выберите пункт **Выбор свидетеля кворума**.

![Рис. 34. Выбор файлового ресурса-свидетеля][sap-ha-guide-figure-3025]

_**Рис. 34.** Выбор файлового ресурса-свидетеля_

Выберите пункт **Настройка файлового ресурса-свидетеля**.

![Рис. 35. Определение расположения файлового ресурса-свидетеля][sap-ha-guide-figure-3026]

_**Рис. 35.** Определение расположения файлового ресурса-свидетеля_

Введите путь UNC к файловому ресурсу (в нашем примере это \\domcontr-0\FSW).

Нажмите кнопку **Далее** для отображения списка изменений, которые можно внести. Выберите нужные изменения и нажмите кнопку **Далее**.

![Рис. 36. Подтверждение перенастройки кластера][sap-ha-guide-figure-3027]

_**Рис. 36.** Подтверждение перенастройки кластера_

На этом последнем шаге конфигурация кластера должна быть успешно перенастроена, как показано на рис. 36.  

### <a name="a-name5c8e5482841e45e1a89da05c0907c868a-install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Установка SIOS DataKeeper Cluster Edition для создания общего диска кластера SAP ASCS/SCS

На данный момент у нас имеется рабочая конфигурация отказоустойчивого кластера Windows Server в Azure. Однако чтобы установить экземпляр SAP ASCS/SCS, требуется общий дисковый ресурс. Azure не позволяет создавать общие дисковые ресурсы. Поэтому для выполнения этой задачи придется воспользоваться SIOS DataKeeper Cluster Edition — сторонним решением для создания общих дисковых ресурсов.

#### <a name="a-name1c2788c336484e829e0de058e475e2a3a-add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Добавление .NET Framework 3.5

Компонент Microsoft .NET Framework 3.5 не установлен и не включен автоматически в Windows Server 2012 R2. Однако для работы SIOS DataKeeper требуется установить .NET Framework на всех узлах, на который устанавливается этот продукт. Поэтому необходимо установить компонент .NET Framework 3.5 в гостевой ОС всех виртуальных машин в кластере.

Существует два способа добавления .NET Framework 3.5. Первый — использовать мастер добавления ролей и компонентов в Windows, как показано на рис. 37.

![Рис 37. Установка .NET Framework 3.5 с помощью мастера добавления ролей и компонентов][sap-ha-guide-figure-3028]

_**Рис 37.** Установка .NET Framework 3.5 с помощью мастера добавления ролей и компонентов_

![Рис. 38. Ход установки .NET Framework 3.5 с помощью мастера добавления ролей и компонентов][sap-ha-guide-figure-3029]

_**Рис. 38.** Ход установки .NET Framework 3.5 с помощью мастера добавления ролей и компонентов_

Второй способ включить компонент .Net Framework 3.5 — использовать программу командной строки dism.exe. Для этого необходим доступ к каталогу SxS на установочном носителе Windows. В командной строке с повышенными привилегиями выполните следующую команду:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="a-namedd41d5a28083415b9878839652812102a-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Установка SIOS DataKeeper

SIOS DataKeeper Cluster Edition нужно установить на обоих узлах в кластере. SIOS DataKeeper позволяет создать виртуальное общее хранилище, создавая синхронизированное зеркало и имитируя общее хранилище кластера.

Перед установкой программного обеспечения SIOS необходимо создать пользователя домена **DataKeeperSvc**.

> [AZURE.NOTE] Добавьте пользователя **DataKeeperSvc** в группу **локальных администраторов** на обоих узлах кластера.

Установите программное обеспечение SIOS на обоих узлах кластера.

![Установщик SIOS][sap-ha-guide-figure-3030]

![Рис. 39. Первый экран установки SIOS DataKeeper][sap-ha-guide-figure-3031]

_**Рис. 39.** Первый экран установки SIOS DataKeeper_

![Рис. 40. DataKeeper информирует об отключении службы][sap-ha-guide-figure-3032]

_**Рис. 40.** DataKeeper информирует об отключении службы_

В диалоговом окне, показанном на рис. 40, выберите **Да**.

![Рис. 41. Выбор пользователя для SIOS DataKeeper][sap-ha-guide-figure-3033]

_**Рис. 41.** Выбор пользователя для SIOS DataKeeper_


На экране, показанном на рис. 41, мы рекомендуем выбрать **Учетная запись домена или сервера**.

![Рис. 42. Ввод имени пользователя и пароля для установки SIOS DataKeeper][sap-ha-guide-figure-3034]

_**Рис. 42.** Ввод имени пользователя и пароля для установки SIOS DataKeeper_

Укажите имя учетной записи пользователя домена и пароли, созданные для SIOS DataKeeper.

![Рис. 43. Указание лицензии на использование SIOS DataKeeper][sap-ha-guide-figure-3035]

_**Рис. 43.** Указание лицензии на использование SIOS DataKeeper_

Установите лицензионный ключ для своего экземпляра SIOS DataKeeper, как показано на рис. 43. В конце установки вам будет предложено перезагрузить виртуальную машину.

#### <a name="a-named9c1fc8e87104dffbec21f535db7b006a-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Настройка SIOS DataKeeper

После установки SIOS DataKeeper на обоих узлах нужно начать настройку параметров. Наша цель — настроить синхронную репликацию данных между дополнительными VHD-дисками, подключенными к каждой виртуальной машине. Ниже приведены шаги для настройки обоих узлов.

![Рис. 44. Инструмент управления и настройки DataKeeper][sap-ha-guide-figure-3036]

_**Рис. 44.** Инструмент управления и настройки DataKeeper_

Запустите инструмент управления и настройки DataKeeper, а затем щелкните **Подключение к серверу**. (На рис. 44 эта ссылка обведена красным.)

![Рис. 45. Вставка имени или TCP/IP-адреса первого узла, к которому должен подключиться инструмент управления. На втором шаге — вставка данных для второго узла.][sap-ha-guide-figure-3037]

_**Рис. 45.** Вставка имени или TCP/IP-адреса первого узла, к которому должен подключиться инструмент управления. На втором шаге — вставка данных для второго узла._

Следующим шагом является создание задания репликации между двумя узлами.

![Рис. 46. Создание задания репликации][sap-ha-guide-figure-3038]

_**Рис. 46.** Создание задания репликации_

Мастер поможет выполнить эту процедуру.

![Рис. 47. Определение имени задания репликации][sap-ha-guide-figure-3039]

_**Рис. 47.** Определение имени задания репликации_

![Рис. 48. Определение базовых данных для узла, который должен быть текущим исходным узлом][sap-ha-guide-figure-3040]

_**Рис. 48.** Определение базовых данных для узла, который должен быть текущим исходным узлом_

На первом шаге необходимо определить имя, TCP/IP-адрес и том диска для исходного узла. На втором шаге следует определить целевой узел. Как говорилось ранее, необходимо определить имя, TCP/IP-адрес и том диска для целевого узла.

![Рис. 49. Определение базовых данных для узла, который должен быть текущим целевым узлом][sap-ha-guide-figure-3041]

_**Рис. 49.** Определение базовых данных для узла, который должен быть текущим целевым узлом_

Следующим шагом является определение применяемых алгоритмов сжатия. Для наших целей рекомендуется сжатие потока репликации. Сжатие потока репликации значительно сокращает время повторной синхронизации (особенно в случаях повторной синхронизации). Имейте в виду, что сжатие использует ресурсы ЦП и памяти виртуальной машины. Чем выше степень сжатия, тем больше ресурсов ЦП будет использоваться. Этот параметр можно будет откорректировать и изменить позже.

Кроме того, нужно проверить параметр, который позволяет включить синхронный или асинхронный режим репликации. *Для защиты конфигураций SAP ASCS/SCS требуется синхронная репликация*.  

![Рис. 50. Определение сведений о репликации][sap-ha-guide-figure-3042]

_**Рис. 50.** Определение сведений о репликации_

На последнем шаге нужно определить, следует ли том, который реплицируется с помощью задания репликации, представить в конфигурации кластера WSFC как общий диск. Для конфигурации SAP ASCS/SCS выберите **Да**, чтобы кластер Windows воспринимал реплицируемый том как общий диск, который можно использовать в качестве тома кластера.

![Рис. 51. Чтобы задать реплицируемый том как том кластера, следует нажать кнопку **Да**][sap-ha-guide-figure-3043]

_**Рис. 51.** Чтобы задать реплицируемый том как том кластера, следует нажать кнопку **Да**_

После создания тома инструмент управления и настройки DataKeeper отобразят задание репликации как активное.

![Рис. 52. Синхронное зеркальное отображение DataKeeper для общего диска SAP ASCS/SCS активно][sap-ha-guide-figure-3044]

_**Рис. 52.** Синхронное зеркальное отображение DataKeeper для общего диска SAP ASCS/SCS активно_

Теперь диск отображается в диспетчере отказоустойчивости кластеров Windows как диск DataKeeper, как показано на рис. 53.

![Рис. 53. Реплицируемый диск DataKeeper отображается в диспетчере отказоустойчивости кластеров][sap-ha-guide-figure-3045]

_**Рис. 53.** Реплицируемый диск DataKeeper отображается в диспетчере отказоустойчивости кластеров_


## <a name="a-namea06f0b498a7a42bf8b0dc12026c5746ba-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Установка системы SAP NetWeaver

Мы не описываем настройку СУБД, так как она зависит от используемой СУБД. Тем не менее мы предполагаем, что задачи обеспечения высокого уровня доступности СУБД решены благодаря возможностям различных поставщиков СУБД, поддерживающих Azure. Например, AlwaysOn или зеркальное отображение базы данных для SQL Server и Oracle Data Guard для Oracle. В нашем примере сценария мы не реализуем дополнительную защиту СУБД.

Кроме того, отсутствуют особые рекомендации по взаимодействию различных СУБД с такой кластеризованной конфигурацией SAP ASCS/SCS в Azure.


> [AZURE.NOTE] Процедуры установки систем SAP NetWeaver ABAP, SAP Java и SAP ABAP с Java практически идентичны. Главное отличие состоит в том, что в системе SAP ABAP всего один экземпляр ASCS. В системе SAP Java имеется один экземпляр SCS. В системе SAP ABAP с Java — один экземпляр ASCS и один экземпляр SCS, которые работают в одной группе отказоустойчивого кластера Майкрософт. Любые отличия в установке для каждого стека установки SAP NetWeaver будут указаны явным образом. Все остальные составляющие считаются одинаковыми.  

### <a name="a-name31c6bd4f51df40579fdf3fcbc619c170a-install-sap-with-a-highavailability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Установка SAP с высокодоступным экземпляром ASCS/SCS

> [AZURE.IMPORTANT] Не размещайте файл подкачки на зеркальных томах DataKeeper. DataKeeper не поддерживает зеркальные тома. Можно оставить файл подкачки на временном диске D виртуальной машины Azure, который задан по умолчанию. Если файла там нет, поместите его на диск D виртуальной машины Azure.

#### <a name="a-namea97ad604909444fea364f89cb39bf097a-create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Создание имени виртуального узла для кластеризованного экземпляра SAP ASCS/SCS

Сначала создайте запись DNS для имени виртуального узла экземпляра ASCS/SCS в диспетчере DNS Windows. Затем укажите IP-адрес виртуального имени узла.

> [AZURE.NOTE]  
Следует помнить, что IP-адрес, назначаемый имени виртуального узла экземпляра ASCS/SCS, должен совпадать с IP-адресом, который назначен Azure Load Balancer (<*SID*>-lb-ascs).  

IP-адрес имени виртуального узла SAP ASCS/SCS (pr1 ascs sap) совпадает с IP-адресом Azure Load Balancer (pr1-lb-ascs).

В одном отказоустойчивом кластере Windows Server в Azure может выполняться только одна роль отказоустойчивого кластера SAP. Например, один экземпляр ASCS для системы ABAP и один экземпляр SCS для системы Java. Для ABAP с Java будет использоваться один экземпляр ASCS и один экземпляр SCS.

> [AZURE.NOTE] Сейчас кластеризация с несколькими ИД безопасности, как описано в руководстве по установке SAP (см. [руководства по установке SAP][sap-installation-guides]), не поддерживается в Azure.

![Рис. 54. Определение записи DNS для виртуального имени и TCP/IP-адреса кластера SAP ASCS/SCS][sap-ha-guide-figure-3046]

_**Рис. 54.** Определение записи DNS для виртуального имени и TCP/IP-адреса кластера SAP ASCS/SCS_

Запись отображается в диспетчере DNS в домене, как показано на рис. 55.

![Рис. 55. Новые виртуальное имя и TCP/IP-адрес для конфигурации кластера SAP ASCS/SCS][sap-ha-guide-figure-3047]

_**Рис. 55.** Новые виртуальное имя и TCP/IP-адрес для конфигурации кластера SAP ASCS/SCS_

#### <a name="a-nameeb5af918b42f4803bb50eff41f84b0b0a-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Установка первого узла кластера SAP

Чтобы установить первый кластер SAP, выполните первый параметр узла кластера на узле кластера A. Например, на узле **pr1-ascs-0**, как в нашем примере.

Если вы хотите сохранить порты по умолчанию для внутреннего балансировщика нагрузки Azure, то выберите:

- для **системы ABAP** — экземпляр **ASCS** с номером **00**;
- для **системы Java** — экземпляр **SCS** с номером **01**;
- для **системы ABAP с JAVA** — экземпляр **ASCS** с номером **00** и экземпляр **SCS** с номером **01**.

Если вы хотите использовать номера экземпляров, отличные от 00, для ABAP ASCS и 01 для Java SCS, то сначала необходимо изменить правила балансировки нагрузки по умолчанию для внутреннего балансировщика нагрузки Azure, как описано в разделе [Изменение правил балансировки нагрузки ASCS/SCS по умолчанию для внутреннего балансировщика нагрузки Azure][sap-ha-guide-8.9].

Затем выполните несколько действий, которые не описаны в обычной документации по установке SAP.

> [AZURE.NOTE] В документации по установке SAP содержатся сведения по установке первого узла кластера ASCS/SCS.

#### <a name="a-namee4caaab2e90f4f2cbc842cd2e12a9556a-modify-the-sap-profile-of-the-ascsscs-ipowershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Изменение профиля SAP экземпляра ASCS/SCS i```powershellnstance

Необходимо добавить новый параметр профиля. Этот параметр профиля запрещает закрытие подключений между рабочими процессами SAP и сервером постановки в очередь, если они простаивают слишком долго. Мы уже упоминали проблему в разделе [Добавление записей реестра для обоих узлов кластера, используемых для экземпляра SAP ASCS/SCS][sap-ha-guide-8.11] данного документа. В этом разделе мы также внесли два изменения в некоторые основные параметры подключения по TCP/IP. На втором шаге необходимо настроить сервер постановки в очередь для отправки сигнала **keep_alive**, чтобы простой подключений не превышал пороговое значение, заданное во внутреннем балансировщике нагрузки Azure.

Добавьте этот параметр профиля в профиль экземпляра SAP ASCS/SCS:
```
enque/encni/set_so_keepalive = true
```
В нашем примере путь следующий:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

Например, ниже приведен профиль экземпляра SAP SCS и соответствующий путь.

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="a-name10822f4f32e74871b63a9b86c76ce761a-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Добавление порта пробы

Чтобы вся конфигурация кластера работала с Azure Load Balancer, нужно использовать функцию проб внутреннего балансировщика нагрузки. Обычно внутренний балансировщик нагрузки Azure распределяет входящую рабочую нагрузку поровну между участвующими виртуальными машинами. Однако это не будет работать в некоторых конфигурациях кластера, так как активен только один экземпляр. Другой экземпляр пассивен и не может принимать рабочую нагрузку. Функция проб полезна в случаях, когда внутренний балансировщик нагрузки Azure назначает работу только активным экземплярам. С помощью этой функции внутренний балансировщик нагрузки может определить, какой из экземпляров активен, и затем направлять рабочую нагрузку только на него.

Сначала проверьте текущий параметр **ProbePort** с помощью приведенной ниже команды PowerShell. Выполните ее на одной из виртуальных машин в конфигурации кластера.

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Рис. 56. По умолчанию в конфигурации кластера для проб используется порт 0][sap-ha-guide-figure-3048]

_**Рис. 56.** По умолчанию в конфигурации кластера для проб используется порт 0_

Затем укажите порт пробы. По умолчанию для проб задан порт 0. В нашем примере мы используем порт пробы **62300**.

Номер порта определен в шаблонах Azure Resource Manager для SAP. Назначить номер порта можно в PowerShell.

Сначала необходимо получить **IP-адрес кластерного ресурса SAP WAC** для имени виртуального узла SAP,

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

а затем задать порт пробы **62300**.

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Чтобы изменения вступили в силу, необходимо остановить, а затем запустить роль кластера **SAP PR1**.

После подключения роли кластера **SAP PR1** к сети убедитесь, что для **ProbePort** задано новое значение.

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Рис. 57. Порт пробы кластера после изменения][sap-ha-guide-figure-3049]

_**Рис. 57.** Порт пробы кластера после изменения_

**ProbePort** имеет значение **62300**. Теперь доступ к файловому ресурсу **\\\\ascsha-clsap\sapmnt** можно получить из других узлов, например **ascsha-dbas**.

### <a name="a-name85d78414b21d409792b634d8bcb724b7a-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Установка экземпляра базы данных

Чтобы установить экземпляр базы данных, следуйте инструкциям в документации по установке SAP.

### <a name="a-name8a276e16f5074071b829cdc0a4d36748a-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Установка второго узла кластера

Чтобы установить второй кластер, выполните действия, описанные в руководстве по установке SAP.

### <a name="a-name094bc89531d4447191cc1513b64e406aa-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Изменение типа запуска службы Windows для экземпляра SAP ERS

На обоих узлах кластера измените тип запуска служб Windows SAP ERS на **Автоматически (отложенный запуск)**.

![Рис. 58. Изменение типа службы для экземпляра SAP ERS на "Автоматически (отложенный запуск)"][sap-ha-guide-figure-3050]

_**Рис. 58.** Изменение типа службы для экземпляра SAP ERS на "Автоматически (отложенный запуск)"_

### <a name="a-name2477e58fc5a74a5d9ae37b91022cafb5a-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Установка основного сервера приложений SAP

Установите экземпляр основного сервера приложений (PAS) <*SID*>-di-0> на виртуальную машину, предназначенную для размещения PAS. Какие-либо специальные зависимости от Azure или DataKeeper отсутствуют.

### <a name="a-name0ba4a6c1cc374bcfa8dc025de4263772a-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Установка дополнительного сервера приложений SAP

Установите дополнительный сервер приложений SAP (AAS) на все виртуальные машины, предназначенные для размещения сервера приложений SAP. Например, с <*SID*>-di-1 по <*SID*>-di-<n>.

## <a name="a-name18aa2b9d92d24c0e8ddd5acaabda99e9a-test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Тестирование отработки отказа экземпляра SAP ASCS/SCS и репликации SIOS

С помощью диспетчера отказоустойчивости кластеров и пользовательского интерфейса SIOS DataKeeper можно легко выполнять тестирование и мониторинг отработки отказа экземпляров SAP ASCS/SCS и репликации дисков SIOS.

### <a name="a-name65fdef0f9f9441f9b314ea45bbfea445a-sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Экземпляр SAP ASCS/SCS выполняется на узле A кластера

Кластерная группа **SAP WAC** работает на узле A кластера, например **ascsha-clna**. Назначьте узлу A кластера общий диск S:, принадлежащий кластерной группе **SAP WAC** и используемый экземпляром ASCS/SCS.

![Рис. 59. Диспетчер отказоустойчивости кластеров: кластерная группа SAP <*ИД безопасности*> работает на узле A кластера][sap-ha-guide-figure-5000]

_**Рис. 59.** Диспетчер отказоустойчивости кластеров: кластерная группа SAP <*ИД безопасности*> работает на узле A кластера_

С помощью пользовательского интерфейса SIOS DataKeeper можно увидеть, что данные общего диска синхронно реплицируются с исходного тома S: на узле А кластера (например, **ascsha-clna [10.0.0.41]**) на целевой том S: на узле B кластера (например, **ascsha-clnb [10.0.0.42]**).

![Рис. 60. SIOS DataKeeper: репликация локального тома с узла A на узел B кластера][sap-ha-guide-figure-5001]

_**Рис. 60.** SIOS DataKeeper: репликация локального тома с узла A на узел B кластера_


### <a name="a-name5e959fa98fcd49e5a12c37f6ba07b916a-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Отработка отказа с узла A на узел B

Вы можете инициировать отработку отказа кластерной группы SAP <*ИД безопасности*> с узла A на узел B кластера следующими способами:

- воспользовавшись диспетчером отказоустойчивости кластеров;  
- воспользовавшись командами PowerShell отказоустойчивого кластера;
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
- перезапустив узел A кластера в гостевой ОС Windows (это инициирует автоматическую отработку отказа кластерной группы SAP <*ИД безопасности*> с узла A на узел B кластера);  
- перезапустив узел A кластера на портале Azure (это инициирует автоматическую отработку отказа кластерной группы SAP <*ИД безопасности*> с узла A на узел B кластера);  
- перезапустив узел A кластера с помощью Azure PowerShell (это инициирует автоматическую отработку отказа кластерной группы SAP <*ИД безопасности*> с узла A на узел B кластера).

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

После отработки отказа кластерная группа SAP <*SID*> работает на узле B кластера, например **ascsha-clnb**.

![Рис. 61. Диспетчер отказоустойчивости кластеров: кластерная группа SAP <*ИД безопасности*> работает на узле B кластера][sap-ha-guide-figure-5002]

_**Рис. 61.** Диспетчер отказоустойчивости кластеров: кластерная группа SAP <*ИД безопасности*> работает на узле B кластера_

Теперь общий диск подключен к узлу B кластера. SIOS DataKeeper реплицирует данные с исходного тома S: на узле B кластера (например, **ascsha-clnb [10.0.0.42]**) на целевой том S: на узле А кластера (например, **ascsha-clna [10.0.0.41]**).


![Рис. 62. SIOS DataKeeper: репликация локального тома с узла B на узел A кластера][sap-ha-guide-figure-5003]

_**Рис. 62.** SIOS DataKeeper: репликация локального тома с узла B на узел A кластера_



<!-----HONumber=Oct16_HO2-->


