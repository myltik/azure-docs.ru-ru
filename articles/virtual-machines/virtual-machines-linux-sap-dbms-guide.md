<properties
   pageTitle="SAP NetWeaver на виртуальных машинах Linux — руководство по развертыванию СУБД | Microsoft Azure"
   description="SAP NetWeaver на виртуальных машинах Linux — руководство по развертыванию СУБД"
   services="virtual-machines-linux,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="sedusch"/>

# SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2243692]: https://service.sap.com/sap/support/notes/2243692
[2233094]: https://service.sap.com/sap/support/notes/2233094

[azure-portal]: https://portal.azure.com
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017

[planning-guide]: virtual-machines-linux-sap-planning-guide.md "SAP NetWeaver на виртуальных машинах Linux — руководство по планированию и реализации"
[planning-guide-classic]: virtual-machines-windows-classic-sap-planning-guide.md
[deployment-guide]: virtual-machines-linux-sap-deployment-guide.md "SAP NetWeaver на виртуальных машинах Linux — руководство по развертыванию"
[deployment-guide-classic]: virtual-machines-windows-classic-sap-deployment-guide.md
[dbms-guide]: virtual-machines-linux-sap-dbms-guide.md "SAP NetWeaver на виртуальных машинах Linux — руководство по развертыванию СУБД"
[dbms-guide-classic]: virtual-machines-windows-classic-sap-dbms-guide.md
[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971
[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[getting-started]: virtual-machines-linux-sap-getting-started-arm.md
[getting-started-windows-classic]: virtual-machines-windows-classic-sap-getting-started.md

[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-getting-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-getting-started.md#f2a5e9d8-49e4-419e-9900-af783173481c
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-getting-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-getting-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-getting-started.md#4bb7512c-0fa0-4227-9853-4004281b1037

[getting-started-planning]: virtual-machines-linux-sap-getting-started-arm.md#3da0389e-708b-4e82-b2a2-e92f132df89c
[getting-started-deployment]: virtual-machines-linux-sap-getting-started-arm.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-dbms]: virtual-machines-linux-sap-getting-started-arm.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82

[deployment-guide-2.2]: virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Ресурсы SAP"
[deployment-guide-3]: virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Сценарии развертывания виртуальных машин для SAP в Microsoft Azure"
[deployment-guide-3.1.2]: virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Развертывание виртуальной машины с помощью пользовательского образа"
[deployment-guide-3.2]: virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Сценарий 1. Развертывание виртуальной машины для SAP из Azure Marketplace"
[deployment-guide-3.3]: virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Сценарий 2. Развертывание виртуальной машины для SAP с помощью пользовательского образа"
[deployment-guide-3.4]: virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Сценарий 3. Перемещение виртуальной машины из локальной среды с помощью специализированного виртуального жесткого диска Azure с SAP"
[deployment-guide-4.1]: virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Развертывание командлетов Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Скачивание и импорт соответствующих командлетов PowerShell для SAP"
[deployment-guide-4.3]: virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Присоединение виртуальной машины к локальному домену (только для Windows)"
[deployment-guide-4.4]: virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Скачивание, установка и включение агента виртуальной машины Azure"
[deployment-guide-4.4.2]: virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.5]: virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Расширенный мониторинг Azure для SAP: настройка расширения"
[deployment-guide-4.5.1]: virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Инфраструктура CLI Azure"
[deployment-guide-5.1]: virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Расширенный мониторинг Azure для SAP: проверка готовности"
[deployment-guide-5.2]: virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Конфигурация инфраструктуры мониторинга Azure: проверка работоспособности"
[deployment-guide-5.3]: virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Инфраструктура мониторинга Azure для SAP: дальнейшее устранение неполадок"
[deployment-guide-install-vm-agent-windows]: virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-configure-proxy]: virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Настройка прокси-сервера"
[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Настройка мониторинга"
[deployment-guide-troubleshooting-chapter]: virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Проверки и устранение неполадок при комплексной настройке мониторинга для SAP в Azure"
[deployment-guide-figure-100]: ./media/virtual-machines-linux-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-300]: ./media/virtual-machines-linux-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-linux-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-500]: ./media/virtual-machines-linux-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-600]: ./media/virtual-machines-linux-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-700]: ./media/virtual-machines-linux-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-linux-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-50]: ./media/virtual-machines-linux-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-900]: ./media/virtual-machines-linux-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-1000]: ./media/virtual-machines-linux-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]: ./media/virtual-machines-linux-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-linux-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-linux-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]: ./media/virtual-machines-linux-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-5]: virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-6]: virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-7]: virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-11]: virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-14]: virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-azure-cli-installed]: virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda

[planning-guide-1.2]: virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Ресурсы"
[planning-guide-2.1]: virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Только облако. Развертывание виртуальных машин в Azure без зависимостей в локальной сети клиента"
[planning-guide-2.2]: virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Распределенная сеть. Развертывание одной или нескольких виртуальных машин SAP в Azure с необходимостью полной интеграции с локальной сетью"
[planning-guide-3.1]: virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Регионы Azure"
[planning-guide-3.2]: virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Концепция виртуальных машин Microsoft Azure"
[planning-guide-3.2.1]: virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Домены сбоя"
[planning-guide-3.2.2]: virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Домены обновления"
[planning-guide-3.2.3]: virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Группы доступности Azure"
[planning-guide-3.3.2]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Хранилище Azure Premium"
[planning-guide-5.1.1]: virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Перемещение виртуальной машины из локальной среды в Azure с помощью специализированного диска"
[planning-guide-5.1.2]: virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Развертывание виртуальной машины с помощью пользовательского образа"
[planning-guide-5.2]: virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Подготовка виртуальных машин с SAP для Azure"
[planning-guide-5.2.1]: virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Подготовка виртуальной машины к перемещению из локальной среды в Azure с помощью специализированного диска"
[planning-guide-5.2.2]: virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Подготовка виртуальной машины к развертыванию с помощью пользовательского образа для SAP"
[planning-guide-5.3.1]: virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Разница между диском Azure и образом Azure"
[planning-guide-5.3.2]: virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Отправка VHD-диска из локальной среды в Azure"
[planning-guide-5.4.2]: virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Копирование дисков между учетными записями хранения Azure"
[planning-guide-5.5.1]: virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Структура виртуальной машины и VHD для развертываний SAP"
[planning-guide-5.5.3]: virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Настройка автоподключения для подключенных дисков"
[planning-guide-7]: virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Концепция полностью облачного развертывания экземпляров SAP"
[planning-guide-7.1]: virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Обучающий сценарий с демонстрацией одной виртуальной машины с SAP NetWeaver"
[planning-guide-9.1]: virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Решение мониторинга Azure для SAP"
[planning-guide-11.4.1]: virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Высокая доступность серверов приложений SAP"
[planning-guide-11.5]: virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Использование автозапуска для экземпляров SAP"
[planning-guide-microsoft-azure-networking]: virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Сеть Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Хранилище. Служба хранилища Microsoft Azure и диски данных"
[planning-guide-azure-premium-storage]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Хранилище Azure Premium"
[planning-guide-figure-100]: ./media/virtual-machines-linux-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-200]: ./media/virtual-machines-linux-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-300]: ./media/virtual-machines-linux-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-400]: ./media/virtual-machines-linux-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-linux-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-linux-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-linux-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-figure-1300]: ./media/virtual-machines-linux-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-linux-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-linux-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-linux-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-linux-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-2100]: ./media/virtual-machines-linux-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-linux-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-linux-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-linux-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-linux-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-linux-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-linux-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-linux-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-linux-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-3000]: ./media/virtual-machines-linux-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-linux-sap-planning-guide/3200-sap-ha-with-sql.png

[dbms-guide-2]: virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Структура развертывания реляционной СУБД"
[dbms-guide-2.1]: virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Кэширование для виртуальных машин и виртуальных жестких дисков"
[dbms-guide-2.2]: virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "Программный RAID-массив"
[dbms-guide-2.3]: virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Хранилище Microsoft Azure"
[dbms-guide-3]: virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Высокая доступность и аварийное восстановление с использованием виртуальных машин Azure"
[dbms-guide-5]: virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Особенности реляционных СУБД SQL Server"
[dbms-guide-5.5.1]: virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 4 (CU4) и последующие выпуски"
[dbms-guide-5.5.2]: virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3) и предыдущие выпуски"
[dbms-guide-5.6]: virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Использование образов SQL Server из Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Общие сведения об SQL Server для SAP в Azure"
[dbms-guide-8.4.1]: virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Конфигурация хранилища"
[dbms-guide-8.4.2]: virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Архивация и восстановление"
[dbms-guide-8.4.3]: virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Рекомендации по ускорению резервного копирования и восстановления"
[dbms-guide-8.4.4]: virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Другие"
[dbms-guide-figure-100]: ./media/virtual-machines-linux-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-linux-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-linux-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-linux-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-linux-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-linux-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-linux-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-linux-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-linux-sap-dbms-guide/900-sap-cache-server-on-premises.png

[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[Logo_Windows]: ./media/virtual-machines-linux-sap-shared/Windows.png
[Logo_Linux]: ./media/virtual-machines-linux-sap-shared/Linux.png

[vm-size-specs]: virtual-machines-linux-sizes.md
[azure-subscription-service-limits-subscription]: azure-subscription-service-limits.md#subscription
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-cross-premises-options.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[virtual-machines-deploy-rmtemplates-azure-cli]: virtual-machines-deploy-rmtemplates-azure-cli.md "Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-deploy-rmtemplates-powershell.md "Управление виртуальными машинами с помощью диспетчера ресурсов Azure и PowerShell"
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image-resource-manager.md
[virtual-machines-manage-availability]: virtual-machines-linux-manage-availability.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-classic-attach-disk.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[virtual-networks-manage-dns-in-vnet]: virtual-networks-manage-dns-in-vnet.md
[resource-groups-networking]: resource-groups-networking.md
[virtual-networks-static-private-ip-arm-pportal]: virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-multiple-nics]: virtual-networks-multiple-nics.md
[virtual-network-deploy-multinic-arm-template]: virtual-network-deploy-multinic-arm-template.md
[virtual-network-deploy-multinic-arm-ps]: virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-cli]: virtual-network-deploy-multinic-arm-cli.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-about-vpn-devices]: vpn-gateway-about-vpn-devices.md
[vpn-gateway-vpn-faq]: vpn-gateway-vpn-faq.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[powershell-install-configure]: powershell-install-configure.md
[xplat-cli]: xplat-cli.md
[virtual-machines-deploy-rmtemplates-azure-cli]: virtual-machines-deploy-rmtemplates-azure-cli.md
[xplat-cli-azure-resource-manager]: xplat-cli-azure-resource-manager.md
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-create-upload-vhd-suse.md
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image-resource-manager.md
[storage-use-azcopy]: storage-use-azcopy.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image-resource-manager.md#capture-the-vm
[storage-azure-cli]: storage-azure-cli.md
[storage-powershell-guide-full-copy-vhd]: storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-azure-cli-copy-blobs]: storage-azure-cli.md#copy-blobs
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-size-specs]: virtual-machines-linux-sizes.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-alwayson-availability-groups-powershell]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md
[azure-subscription-service-limits]: azure-subscription-service-limits.md
[virtual-machines-configuring-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-attach-disk-preview]: virtual-machines-attach-disk-preview.md
[virtual-machines-workload-template-sql-alwayson]: virtual-machines-windows-portal-sql-alwayson-availability-groups.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-how-to-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux
[resource-group-authoring-templates]: resource-group-authoring-templates.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-linux-create-upload-vhd-step-1]: virtual-machines-linux-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[deploy-template-powershell]: resource-group-template-deploy.md#deploy-with-powershell
[deploy-template-cli]: resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: resource-group-template-deploy.md#deploy-with-the-preview-portal
[virtual-networks-udr-overview]: virtual-networks-udr-overview.md
[resource-group-overview]: resource-group-overview.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image-resource-manager.md
[virtual-networks-udr-overview]: virtual-networks-udr-overview.md
[virtual-networks-nsg]: virtual-networks-nsg.md
[storage-premium-storage-preview-portal]: storage-premium-storage-preview-portal.md
[storage-introduction]: storage-introduction.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-upload-image-windows-resource-manager.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-tutorial.md
[virtual-networks-create-vnet-arm-pportal]: virtual-networks-create-vnet-arm-pportal.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[virtual-machines-linux-tutorial]: virtual-machines-linux-tutorial.md

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[virtual-machines-azurerm-versus-azuresm]: virtual-machines-linux-compare-deployment-models.md

[install-extension-cli]: https://github.com/Azure/azure-linux-extensions/blob/master/AzureEnhancedMonitor/README.md

[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd

[sap-pam]: https://support.sap.com/pam "Матрица доступности продуктов SAP"


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

Это руководство — часть документации по внедрению и развертыванию программного обеспечения SAP в Microsoft Azure. Перед ознакомлением с этим документом прочитайте [Руководство по планированию и внедрению][planning-guide]. В руководстве рассматривается развертывание различных реляционных систем управления базами данных (РСУБД) и связанных продуктов в сочетании с SAP на виртуальных машинах Microsoft Azure с использованием возможностей инфраструктуры Azure как услуги (IaaS).

Это руководство дополняет документацию по установке SAP и комментарии по SAP, которые являются основными ресурсами по установке и развертыванию SAP на упомянутых платформах.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## Общие рекомендации
В этой главе рассматриваются вопросы запуска связанных с SAP систем СУБД на виртуальных машинах Azure. Также здесь приводятся ссылки на конкретные СУБД. Конкретные СУБД рассматриваются в руководстве после этой главы.

### Определения
В этом документе мы будем использовать следующие термины.

* IaaS: инфраструктура как услуга.
* PaaS: платформа как услуга.
* SaaS: программное обеспечение как услуга.
* Компонент SAP: отдельное приложение SAP, например ECC, BW, Solution Manager или EP. Компоненты SAP могут основываться на традиционной технологии ABAP или Java или быть приложениями не на основе NetWeaver, например бизнес-объектами.
* Среда SAP: один или несколько компонентов SAP, логически сгруппированных для выполнения бизнес-функции (разработка, оценка качества, обучение, аварийное восстановление или производство).
* Ландшафт SAP: термин обозначает все ресурсы SAP, доступные в клиентском ИТ-ландшафте. Ландшафт SAP включает все рабочие и нерабочие среды.
* Система SAP: сочетание уровня СУБД и уровня приложений, например система разработки SAP ERP, тестовая система SAP BW, производственная система SAP CRM и т. д. В развертываниях Azure не поддерживается разделение этих двух уровней между локальной средой и Azure. Таким образом, система SAP должна быть развернута полностью в локальной среде или полностью в Azure. Но при этом вы можете развертывать разные системы ландшафта SAP как в Azure, так и локально. Например, системы разработки и тестирования SAP CRM можно развернуть в Azure, а производственную систему SAP CRM — в локальной среде.
* Развертывание только в облаке: это развертывание, в котором подписка Azure не подключена к инфраструктуре локальной сети через подключение типа "сеть — сеть" или ExpressRoute. В документации Azure развертывания такого рода традиционно называются полностью облачными. Доступ к виртуальным машинам, развернутым с помощью этого метода, осуществляется через Интернет по общедоступным конечным точкам, которые назначаются виртуальным машинам в Azure. В этих типах развертываний Active Directory (AD) и DNS из локальной сети не расширяется в Azure. Таким образом, эти виртуальные машины не входят в локальный каталог Active Directory. Примечание. Развертывания только в облаке в этом документе определяются как полные ландшафты SAP, которые работают исключительно в Azure. Для них службы Active Directory и служба разрешения имен не распространяются из локальной сети в общедоступное облако. Полностью облачные конфигурации не поддерживаются для производственных систем SAP и конфигураций, в которых требуется наличие SAP STMS или других локальных ресурсов между размещенными в Azure системами SAP и локальными ресурсами.
* Распределенное развертывание: описывает сценарии, при которых в подписке Azure развернуты виртуальные машины с подключениями типа "сеть — сеть" или ExpressRoute между локальными центрами обработки данных и Azure. В документации Azure развертывания такого рода также называются распределенными развертываниями. Такое подключение используется для расширения в Azure локальных доменов, локальной службы Active Directory и локальной службы DNS. Локальный ландшафт распространяется на ресурсы Azure, входящие в подписку. При таком расширении виртуальные машины могут быть частью локального домена. Пользователи локального домена имеют доступ к серверам и могут запускать службы на этих виртуальных машинах (например, службы СУБД). Возможно взаимодействие и разрешение имен между виртуальными машинами, развернутыми в локальной сети и Azure. Ожидается, что это будет наиболее распространенным сценарием развертывания ресурсов SAP в Azure. Дополнительные сведения см. в [этой][vpn-gateway-cross-premises-options] и [этой][vpn-gateway-site-to-site-create] статьях.

> [AZURE.NOTE] Для производственных систем SAP поддерживаются распределенные развертывания систем SAP, в которых системы SAP выполняются на виртуальных машинах Azure, являющихся членами локального домена. Распределенная конфигурация предполагает развертывание в Azure полного ландшафта SAP или его частей. Даже если весь ландшафт SAP работает в Azure, эти виртуальные машины должны входить в локальный домен и каталог ADS. В прежних версиях этого документа мы говорили о гибридных сценариях ИТ-систем, где термин "гибридные" применялся на том основании, что между локальной сетью и Azure существует распределенное подключение. Кроме того, этот термин также обозначает, что развернутые в Azure виртуальные машины являются частью локальной системы Active Directory.

В некоторой документации Майкрософт сценарии распределенного подключения описываются иначе. В частности, это касается высокодоступных конфигураций СУБД. В документации по SAP сценарий распределенного подключения сводится к двум моментам: наличию подключения типа "сеть — сеть" или частного (ExpressRoute) подключения, а также распределению ландшафта SAP между локальной сетью и Azure.

### Ресурсы
По теме развертывания SAP в Azure доступны следующие руководства.

* [SAP NetWeaver на виртуальных машинах Azure. Руководство по планированию и реализации][planning-guide]
* [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД (этот документ)][dbms-guide]

Следующие примечания по SAP актуальны для развертывания SAP в Azure.

| Номер примечания | Название
|------------|--------
| [1928533] | Приложения SAP в Azure: поддерживаемые продукты и типы виртуальных машин Azure
| [2015553] | SAP в Microsoft Azure: требования
| [1999351] | Устранение неполадок, связанных с расширенным мониторингом Azure для SAP
| [2178632] | Ключевые метрики мониторинга для SAP в Microsoft Azure
| [1409604] | Виртуализация в Windows: расширенный мониторинг
| [2191498] | SAP на платформе Linux в Azure: расширенный мониторинг
| [2039619] | Приложения SAP в Microsoft Azure с использованием базы данных Oracle: поддерживаемые продукты и версии
| [2233094] | DB6: приложения SAP в Azure с использованием IBM DB2 для Linux, UNIX и Windows — дополнительные сведения
| [2243692] | Linux на виртуальной машине Microsoft Azure (IaaS): проблемы с лицензированием SAP


Также см. раздел [вики-сайта SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), где представлены все примечания по SAP для Linux.

Вы узнаете об архитектуре Microsoft Azure и о том, как развертываются и эксплуатируются виртуальные машины Microsoft Azure. Дополнительные сведения см. здесь: <https://azure.microsoft.com/documentation/>
 
> [AZURE.NOTE] Мы **не** обсуждаем предложения платформы Microsoft Azure как услуги (PaaS) на платформе Microsoft Azure. Этот документ посвящен запуску системы управления базами данных (СУБД) на виртуальных машинах Microsoft Azure (IaaS) — так же, как СУБД запускается в локальной среде. Возможности и функции базы данных в этих двух предложениях существенно отличаются, и их не следует путать друг с другом. См. также: <https://azure.microsoft.com/services/sql-database/>

Для IaaS установка и настройка Windows, Linux и СУБД в целом выполняются так же, как и для любой виртуальной или физической машины, устанавливаемой локально. Но некоторые решения в плане архитектуры и реализации управления системой все же отличаются при использовании IaaS. В этом документе объясняются конкретные различия в архитектуре и управлении системой, которые следует учитывать при использовании IaaS.

В целом в документе рассматриваются такие области различий:

* Планирование надлежащей структуры виртуальных машин и виртуальных жестких дисков для систем SAP, чтобы обеспечить необходимую структуру файлов данных и достичь достаточного количества операций ввода-вывода для рабочей нагрузки.
* Сетевые аспекты при использовании IaaS.
* Возможности конкретной базы данных, используемые для оптимизации ее структуры.
* Вопросы резервного копирования и восстановления в IaaS.
* Использование разных типов образов для развертывания.
* Высокий уровень доступности в Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Структура развертывания реляционной СУБД
Прежде чем продолжать чтение, давайте вспомним, о чем шла речь в [этой][deployment-guide-3] главе [руководства по развертыванию][deployment-guide]. Вы должны разбираться в сериях виртуальных машин и понимать разницу между ними, а также понимать разницу между службами хранилища Azure классов Standard и Premium.

До марта 2015 г. виртуальные жесткие диски Azure, содержащие операционную систему, были ограничены размером 127 ГБ. Это ограничение было снято в марте 2015 г. (дополнительные сведения см. в статье <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Теперь виртуальные жесткие диски с операционной системой могут иметь такой же размер, как и любой другой виртуальный жесткий диск. Но мы все равно предпочитаем структуру развертывания, в которой операционная система, СУБД и конечные двоичные файлы SAP отделены от файлов базы данных. Следовательно, мы ожидаем, что в системах SAP, выполняемых на виртуальных машинах Azure, будут установлены операционная система, исполняемые файлы СУБД и исполняемые файлы SAP на базовой виртуальной машине (или виртуальном жестком диске). Файлы данных СУБД и журналов будут храниться в службе хранилища Azure (класса Standard или Premium) в отдельных файлах виртуальных жестких дисков и присоединяться в качестве логических дисков к исходной виртуальной машине с образом операционной системы Azure.

В зависимости от того, какой класс службы хранилища Azure используется — Standard или Premium (например, при использовании виртуальных машин серии DS или серии GS), в Azure предусмотрены другие квоты, описанные [здесь][virtual-machines-size-specs]. При планировании виртуальных жестких дисков Azure вам нужно найти оптимальный баланс между следующими квотами:

* Количество файлов данных.
* Количество виртуальных жестких дисков, которые содержат файлы.
* Количество операций ввода-вывода для одного виртуального жесткого диска.
* Пропускная способность каждого виртуального жесткого диска.
* Количество дополнительных доступных виртуальных жестких дисков в зависимости от размера виртуальной машины.
* Общая пропускная способность хранилища, которую может предоставить виртуальная машина.
 
Azure будет обеспечивать квоту на количество операций ввода-вывода для каждого виртуального жесткого диска. Эти квоты различаются для виртуальных жестких дисков, размещенных в службе хранилища Azure классов Standard и Premium. Также между двумя типами хранилищ будут существенно различаться задержки при операциях ввода-вывода (например, хранилище класса Premium обеспечивает улучшенные показатели). Каждый из типов виртуальных машин поддерживает присоединение ограниченного количества виртуальных жестких дисков. Другое ограничение состоит в том, что только некоторые типы виртуальных машин могут использовать службу хранилища Azure класса Premium. Это означает, что решение для определенного типа виртуальной машины может приниматься не только на основе требований к ЦП и памяти, но и требований к операциям ввода-вывода, задержке и пропускной способности диска, которые обычно масштабируются в зависимости от количества виртуальных жестких дисков или типа дисков хранилища класса Premium. В частности, для хранилища класса Premium размер виртуального жесткого диска также может зависеть от количества операций ввода-вывода и пропускной способности, которая должна достигаться каждым виртуальным жестким диском.

Так как общая частота операций ввода-вывода, количество подключенных виртуальных жестких дисков и размер виртуальной машины взаимосвязаны, конфигурация Azure для системы SAP будет отличаться от конфигурации при развертывании в локальной среде. Ограничения для операций ввода-вывода на каждый номер логического устройства (LUN) обычно настраиваются в локальных развертываниях. Для службы хранилища Azure эти ограничения фиксированы, а для хранилища класса Premium они зависят от типа диска. Поэтому при локальных развертываниях мы наблюдаем пользовательские конфигурации серверов баз данных, в которых используется много разных томов для специальных исполняемых файлов, включая SAP и СУБД, или специальных томов для временных баз данных или табличных пространств. Перемещение такой локальной системы в Azure может привести к потере потенциальной пропускной способности для операций ввода-вывода, так как ресурсы виртуального жесткого диска будет расходоваться на исполняемые файлы или базы данных, которые не выполняют операции ввода-вывода вообще или выполняют незначительное их количество. Таким образом, по возможности исполняемые файлы СУБД и SAP на виртуальных машинах Azure рекомендуется устанавливать на диск с ОС.

Расположение файлов баз данных и файлов журналов, а также тип используемой службы хранилища Azure должны определяться требованиями к операциям ввода-вывода, задержкам и пропускной способности. Чтобы получить достаточное количество операций ввода-вывода для журнала транзакций, иногда необходимо использовать несколько виртуальных жестких дисков для файла журнала транзакций или более крупный диск хранилища класса Premium. В этом случае можно просто создать программный RAID-массив (например, пул хранения Windows для ОС Windows или MDADM для Linux) с виртуальными жесткими дисками, которые будут содержать журнал транзакций.

___

> ![Windows][Logo_Windows] Windows
>
> D:\\ на виртуальной машине Azure — это несохраняемый диск, поддерживаемый некоторыми локальными дисками на вычислительном узле Azure. Несохраняемый означает, что любые изменения содержимого диска D:\\ теряются при перезагрузке виртуальной машины. Под любыми изменениями подразумевается сохранение файлов, создание каталогов, установка приложений и т. д.
>
> ![Linux][Logo_Linux] Linux
>
> Виртуальные машины Azure под управлением Linux автоматически подключают диск к каталогу /mnt/resource — несохраняемому диску, который поддерживается локальными дисками на вычислительном узле Azure. Несохраняемый обозначает, что любые изменения содержимого каталога /mnt/resource теряются при перезагрузке виртуальной машины. Под любыми изменениями подразумевается сохранение файлов, создание каталогов, установка приложений и т. д.

___

В зависимости от серии виртуальной машины Azure локальные диски на вычислительном узле имеют разные показатели производительности; категории приведены ниже.

* A0–A7: крайне ограниченная производительность. Используется только для файла подкачки Windows.
* A8–A11: очень хорошая производительность на уровне 10 тыс. операций ввода-вывода в секунду и более 1 ГБ/с пропускной способности.
* Серия D: очень хорошая производительность на уровне 10 тыс. операций ввода-вывода в секунду и более 1 ГБ/с пропускной способности.
* Серия DS: очень хорошая производительность на уровне 10 тыс. операций ввода-вывода в секунду и более 1 ГБ/с пропускной способности.
* Серия G: очень хорошая производительность на уровне 10 тыс. операций ввода-вывода в секунду и более 1 ГБ/с пропускной способности.
* Серия GS: очень хорошая производительность на уровне 10 тыс. операций ввода-вывода в секунду и более 1 ГБ/с пропускной способности.

Эти сведения относятся к типам виртуальных машин, которые сертифицированы для использования с SAP. Серии виртуальных машин с высокими показателями операций ввода-вывода и пропускной способности позволяют использовать некоторые возможности СУБД, включая tempdb и временное табличное пространство.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Кэширование для виртуальных машин и виртуальных жестких дисков
При создании дисков или виртуальных жестких дисков через портал, а также при подключении отправленных виртуальных жестких дисков к виртуальным машинам мы можем выбрать, будет ли кэшироваться трафик ввода-вывода между виртуальной машиной и этими виртуальными жесткими дисками, размещенными в хранилище Azure. В службе хранилища Azure классов Standard и Premium для этого типа кэша используются две разные технологии. В обоих случаях сам кэш поддерживается теми же дисками, которые используются для временного диска виртуальной машины (D:\\ в Windows или /mnt/resource в Linux).
 
Для службы хранилища Azure класса Standard возможны следующие типы кэша:

* Без кэширования
* Кэширование чтения
* Кэширование чтения и записи

Согласованную и предсказуемую производительность можно получить, задав параметру кэширования в службе хранилища Azure класса Standard для всех виртуальных жестких дисков, содержащих **файлы данных, связанные с СУБД, файлы журналов и табличное пространство, значение NONE**. Кэширование виртуальной машины можно оставить по умолчанию.

Для службы хранилища Azure класса Premium существуют следующие режимы кэширования:

* Без кэширования
* Кэширование чтения

Для службы хранилища Azure класса Premium рекомендуется использовать параметр **Read caching for data files** (Кэширование чтения файлов данных) базы данных SAP и выбрать **No caching for the VHD(s) of log file(s)** (Без кэширования виртуальных жестких дисков файлов журнала).

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Программный RAID-массив
Как уже говорилось выше, вам потребуется сбалансировать настраиваемое количество операций ввода-вывода, необходимых для файлов базы данных, между виртуальными жесткими дисками. Также это нужно сделать в отношении максимального количества операций ввода-вывода, которое будет обеспечивать виртуальная машина Azure для каждого виртуального жесткого диска или типа диска в хранилище класса Premium. Самый простой способ справиться с нагрузкой операций ввода-вывода — создать программный RAID-массив для разных виртуальных жестких дисков. Затем следует поместить ряд файлов данных СУБД SAP в LUN, полученные из программного RAID-массива. В зависимости от требований иногда рекомендуется использовать хранилище класса Premium, так как два из трех разных дисков хранилища этого класса предоставляют более высокую квоту операций ввода-вывода, чем виртуальные жесткие диски в хранилище класса Standard. Кроме того, служба хранилища Azure класса Premium характеризуется улучшенной задержкой при операциях ввода-вывода.

То же применяется и к журналу транзакций разных систем СУБД. При большом их количестве простое добавление дополнительных файлов Tlog не помогает, так как системы СУБД одновременно записывают данные только в один файл. Если требуются более высокие скорости операций ввода-вывода, чем может обеспечить отдельный виртуальный жесткий диск в хранилище класса Standard, можно создать чередующийся массив из нескольких таких дисков или использовать более крупный тип диска хранилища класса Premium (этот тип помимо высокоскоростных операций ввода-вывода также обеспечивает намного меньшую задержку при операциях записи в журнал транзакций).
 
При развертывании Azure программный RAID-массив предпочтительно использовать в следующих ситуациях:

* Журнал транзакций или журнал повтора операций требует больше операций ввода-вывода, чем обеспечивает Azure для одного виртуального жесткого диска. Как упоминалось выше, это можно решить, создав LUN для нескольких виртуальных жестких дисков с помощью программного RAID-массива.
* Неравномерное распределение рабочей нагрузки ввода-вывода между разными файлами данных в базе данных SAP. В таких случаях может возникнуть ситуация, когда один файл данных будет часто превышать квоту. При этом другие файлы данных даже не приблизятся к квоте на количество операций ввода-вывода для одного виртуального жесткого диска. В этом случае самым простым решением будет создать один LUN для нескольких виртуальных жестких дисков с помощью программного RAID-массива. 
* Вы не знаете точную рабочую нагрузку ввода-вывода на каждый файл данных, и вы только примерно знаете общую рабочую нагрузку операций ввода-вывода для СУБД. Проще всего будет создать один LUN с помощью программного RAID-массива. После этого сумма квот для нескольких виртуальных жестких дисков, входящих в этот LUN, будет соответствовать известной скорости операций ввода-вывода.

___

> ![Windows][Logo_Windows] Windows
>
> Использование дисковых пространств под управлением Windows Server 2012 и выше предпочтительнее, так как это более эффективно, чем функция Windows Striping из более ранних версий Windows. Следует учитывать, что при использовании операционной системы Windows Server 2012 иногда необходимо создавать пулы хранения и дисковые пространства Windows с помощью команд PowerShell. Команды PowerShell описаны здесь: <https://technet.microsoft.com/library/jj851254.aspx>

> 
> ![Linux][Logo_Linux] Linux
>
> Для создания программного RAID-массива в Linux поддерживается только MDADM. Дополнительные сведения об использовании программного RAID-массива см. в [этой][virtual-machines-linux-configure-raid] статье.

___

Рекомендации по использованию серий виртуальных машин, которые поддерживают службу хранилища Azure класса Premium:

* Требования к задержкам при операциях ввода-вывода, близкие к обеспечиваемым сетями SAN и устройствами NAS.
* Потребность в улучшенной задержке при операциях ввода-вывода, чем обеспечивает служба хранилища Azure класса Standard.
* Больше операций ввода-вывода на виртуальную машину, чем можно достичь с помощью нескольких виртуальных жестких дисков хранилища класса Standard в отношении определенного типа виртуальной машины.

Так как базовая служба хранилища Azure реплицирует каждый виртуальный жесткий диск минимум на три узла хранилища, можно просто использовать технологию чередующегося массива (RAID 0). В реализации RAID5 или RAID1 нет необходимости.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Хранилище Microsoft Azure
В службе хранилища Microsoft Azure будет храниться базовая виртуальная машина (с ОС) и виртуальные жесткие диски или большие двоичные объекты минимум для трех отдельных узлов хранилища. При создании учетной записи хранения можно выбрать уровень защиты, как показано ниже:

![Георепликация включена для учетной записи хранения Azure][dbms-guide-figure-100]

Локальная репликация службы хранилища Azure (локально избыточная) обеспечивает такие уровни защиты от потери данных вследствие сбоя инфраструктуры, которые мало кто из клиентов может себе позволить развернуть. Как показано выше, существуют четыре варианта; также доступен пятый — как разновидность одного из первых трех. Различия состоят в следующем:

* **Локально избыточное хранилище (LRS) класса Premium**. Служба хранилища Azure класса Premium обеспечивает поддержку дисков с высокой производительностью и малой задержкой для виртуальных машин с интенсивной рабочей нагрузкой ввода-вывода. Предусматриваются три реплики данных в пределах одного и того же центра обработки данных Azure в том или ином регионе Azure. Копии будут располагаться в различных доменах отказа и обновления (основные понятия см. в [этой][deployment-guide-3] главе [руководства по развертыванию][deployment-guide]). В случае выхода из строя реплики данных из-за сбоя узла хранилища или диска автоматически создается новая реплика.
* **Локально избыточное хранилище (LRS)**. В этом случае предусматриваются три реплики данных в пределах одного и того же центра обработки данных Azure в том или ином регионе Azure. Копии будут располагаться в различных доменах отказа и обновления (основные понятия см. в [этой][deployment-guide-3] главе [руководства по развертыванию][deployment-guide]). В случае выхода из строя реплики данных из-за сбоя узла хранилища или диска автоматически создается новая реплика. 
* **Географически избыточное хранилище (GRS)**. В этом случае реализуется асинхронная репликация, которая подает три дополнительные реплики данных в другом регионе Azure, который в большинстве случаев находится в том же географическом регионе (например, Северная Европа и Западная Европа). В результате получатся три дополнительные реплики, так что всего суммарно реплик будет шесть. Может быть вариант, когда данные в геореплицированном регионе Azure используются для чтения (геоизбыточность с доступом на чтение).
* **Хранилище, избыточное в пределах зоны (ZRS)**. В этом случае три реплики данных хранятся в том же регионе Azure. Как описано в [этой][deployment-guide-3] главе [руководства по развертыванию][deployment-guide], один регион Azure могут составлять несколько центров обработки данных, расположенных в непосредственной близости. В случае LRS реплики будут распределяться между разными центрами обработки данных, которые входят в один регион Azure.

Дополнительную информацию см. [здесь][storage-redundancy].
 
> [AZURE.NOTE] Для развертываний СУБД не рекомендуется использовать географически избыточное хранилище.
>
> Георепликация хранилища Azure является асинхронной. Репликация отдельных виртуальных жестких дисков, подключенных к одной виртуальной машине, не синхронизируется в связке. Таким образом, это не подходит для репликации файлов СУБД, которые распределены между разными виртуальными жесткими дисками или развернуты в программном RAID-массиве из нескольких виртуальных жестких дисков. Для программного обеспечения СУБД требуется, чтобы постоянное дисковое хранилище в точности синхронизировалось между разными LUN и базовыми дисками, виртуальными жесткими дисками и шпинделями. Программное обеспечение СУБД использует различные механизмы для последовательной организации операций записи ввода-вывода. СУБД будет сообщать о повреждении целевого дискового хранилища репликации, даже если эти показатели будут различаться всего на несколько миллисекунд. Следовательно, если действительно требуется конфигурация базы данных, распределенной между несколькими виртуальными жесткими дисками с георепликацией, такую репликацию следует выполнять с использованием средств и функций базы данных. При выполнении этого задания не следует полагаться на георепликацию хранилища Azure.
>
> Проблему проще всего объяснить на примере системы. Предположим, что имеется система SAP, переданная в Azure, в которой имеется восемь виртуальных жестких дисков, содержащих файлы данных СУБД, а также один виртуальный жесткий диск, содержащий файл журнала транзакций. Каждый из этих девяти виртуальных жестких дисков будет содержать данные, записанные на него согласованным методом в соответствии с СУБД, будь то в файлы данных или файлы журнала транзакций.
>
> Для правильной георепликации данных и поддержания целостного образа базы данных необходимо геореплицировать содержимое всех девяти виртуальных жестких дисков в том порядке, в котором выполнялись операции ввода-вывода для девяти разных виртуальных жестких дисков. Однако георепликация хранилища Azure не позволяет объявлять зависимости между виртуальными жесткими дисками. Это означает, что функция георепликации хранилища Microsoft Azure не располагает информацией о том, как содержимое этих девяти разных дисков связано друг с другом, и что изменения данных согласуются только при репликации в порядке, в котором выполнялись операции ввода-вывода для всех девяти виртуальных жестких дисков.
>
> Помимо высокой вероятности того, что геореплицированные образы в этом сценарии не обеспечат согласованного образа базы данных, есть вероятность существенного нежелательного влияния на производительность геореплицированного хранилища. Иными словами, этот тип избыточности хранилища не следует использовать для рабочих нагрузок типа СУБД.
 
#### Сопоставление виртуальных жестких дисков с учетными записями хранения службы виртуальных машин Azure
Учетная запись хранения Azure является не только административной конструкцией, но и предметом ограничений. Ограничения отличаются для учетных записей службы хранилища Azure классов Standard и Premium. Подробные возможности и ограничения перечислены [здесь][storage-scalability-targets].
 
Поэтому для службы хранилища Azure класса Standard важно отметить ограничение на количество операций ввода-вывода для учетной записи хранения (см. блок "Общая частота запросов" в [этой статье][storage-scalability-targets]). Кроме того, существует изначальное ограничение в 100 учетных записей хранения на одну подписку Azure (начиная с июля 2015 г.). Таким образом, при использовании службы хранилища Azure класса Standard рекомендуется сбалансировать количество операций ввода-вывода для виртуальных машин между несколькими учетными записями хранения. При этом отдельная виртуальная машина в идеале использует по возможности одну учетную запись хранения. Поэтому, если говорить о развертываниях СУБД, где каждый виртуальный жесткий диск, размещенный в службе хранилища Azure класса Standard, может достичь предела квоты, следует развертывать не более 30–40 виртуальных жестких дисков на одну учетную запись службы хранилища Azure класса Standard. С другой стороны, если используется служба хранилища Azure класса Premium и есть необходимость хранить крупные тома базы данных, показатели операций ввода-вывода будут адекватными. Однако учетная запись службы хранилища Azure класса Premium накладывает существенно большие ограничения на объем данных, чем аналогичная учетная запись хранилища класса Standard. Поэтому в учетной записи службы хранилища Azure класса Premium можно развернуть только ограниченное количество виртуальных жестких дисков, прежде чем лимит объема данных будет исчерпан. В конечном итоге учетную запись службы хранилища Azure можно рассматривать как виртуальную сеть SAN с ограниченными возможностями в показателях операций ввода-вывода и (или) емкости. В результате остается та же задача, что и при локальном развертывании, — определить структуру виртуальных жестких дисков различных систем SAP для разных воображаемых устройств SAN или учетных записей службы хранилища Azure.
 
Для службы хранилища Azure класса Standard по возможности не рекомендуется предоставлять хранилище, состоящее из разных учетных записей хранения, одной виртуальной машине.

А при использовании виртуальных машин Azure серии DS возможно подключить виртуальные жесткие диски с использованием учетных записей службы хранилища Azure классов Standard и Premium. Такое разнородное хранилище может быть полезно, например, при записи резервных копий на виртуальные жесткие диски хранилища класса Standard, когда файлы данных и журналов СУБД содержатся в хранилище класса Premium.

Опыт развертывания и тестирования систем клиентами показывает, что для одной учетной записи службы хранилища Azure класса Standard с приемлемой производительностью можно подготовить около 30–40 виртуальных жестких дисков, содержащих файлы данных и журнала базы данных. Как упоминалось ранее, для учетной записи службы хранилища Azure класса Premium более вероятны ограничения на объем данных, которые могут в нем содержаться, а не на операции ввода-вывода.

Как и в случае с локальными устройствами SAN, для совместного использования требуется определенный мониторинг, позволяющий своевременно обнаруживать проблемы, связанные с учетной записью службы хранилища Azure. Такие средства, как расширение мониторинга Azure для SAP и портал Azure, позволяют обнаруживать загруженные учетные записи хранения Azure, которые могут демонстрировать неоптимальную производительность ввода-вывода. В таком случае рекомендуется переместить загруженные виртуальные машины в другую учетную запись службы хранилища Azure. Подробные сведения о том, как активировать возможности мониторинга узла SAP, см. в [руководстве по развертыванию][deployment-guide].

Другую статью со сводкой практических рекомендаций по службе хранилища Azure класса Standard и связанным учетным записям см. здесь: <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>
 
#### Перемещение развернутых виртуальных машин СУБД из службы хранилища Azure класса Standard в службу хранилища Azure класса Premium
Существует немало сценариев, когда заказчику необходимо переместить развернутую виртуальную машину между службами хранилища Azure разных классов (Standard и Premium). Это невозможно без физического перемещения данных. Это можно сделать несколькими способами.

* Можно просто скопировать все виртуальные жесткие диски — базовый диск и диски с данными — в новую учетную запись службы хранилища Azure класса Premium. Очень часто количество виртуальных жестких дисков в службе хранилища Azure класса Standard выбирается не на основе определенного объема данных, а на основе количества операций ввода-вывода, соответствующих определенному количеству виртуальных жестких дисков. Теперь при перемещении в службу хранилища Azure класса Premium можно обеспечить требуемую пропускную способность для операций ввода-вывода, используя гораздо меньше виртуальных жестких дисков. Учитывая тот факт, что в хранилище Azure Standard вы платите за используемые данные, а не за номинальный размер дисков, количество виртуальных жестких дисков не имеет значения с точки зрения затрат. Однако при использовании службы хранилища Azure класса Premium за номинальный размер дисков взимается плата. Поэтому большинство клиентов стараются поддерживать количество виртуальных жестких дисков Azure в хранилище класса Premium на уровне, необходимом для достижения требуемой пропускной способности для операций ввода-вывода. Следовательно, большинство пользователей отказываются от простого способа копирования в соотношении 1:1.
* Если это еще не сделано, следует подключить один виртуальный жесткий диск, на котором можно разместить резервную копию базы данных SAP. После создания резервной копии следует отключить все виртуальные жесткие диски, включая диск с резервной копией, и скопировать базовый диск и диск с резервной копией в учетную запись службы хранилища Azure класса Premium. Затем нужно развернуть виртуальную машину на основе базового виртуального жесткого диска и подключить диск с резервной копией. Теперь следует создать дополнительные пустые диски хранилища класса Premium для виртуальной машины, на которые восстанавливается база данных. При этом предполагается, что СУБД позволяет изменять пути к файлам данных и журналов в ходе восстановления.
* Еще одна возможность — разновидность предыдущего процесса, когда виртуальный жесткий диск с резервной копией просто копируется в службу хранилища Azure класса Premium и присоединяется к только что развернутой и установленной виртуальной машине.
* Четвертый вариант следует выбирать, если нужно изменить количество файлов данных в базе данных. В таком случае следует выполнить копирование однородной системы SAP с помощью экспорта и импорта. Поместите файлы экспорта на виртуальный жесткий диск, который копируется в учетную запись службы хранилища Azure класса Premium, и подключите его к виртуальной машине, используемой для запуска импорта. Клиенты преимущественно используют эту возможность, когда нужно уменьшить количество файлов данных.

### Развертывание виртуальных машин для SAP в Azure
Microsoft Azure предусматривает несколько способов развертывания виртуальных машин и связанных дисков. Важно понимать различия между ними, так как подготовка виртуальной машины может отличаться в зависимости от способа развертывания. В целом мы рассмотрим сценарии, описанные в следующих главах.

#### Развертывание виртуальной машины из Azure Marketplace
Вы можете использовать для развертывания виртуальной машины образ из Azure Marketplace, предоставленный корпорацией Майкрософт или сторонним поставщиком. Когда вы развернете виртуальную машину в Azure, следуйте тем же рекомендациям и инструкциям средств для установки программного обеспечения SAP на виртуальной машине, что и в локальной среде. Чтобы установить программное обеспечение SAP на виртуальную машину Azure, специалисты SAP и Майкрософт рекомендуют скачать и сохранить установочный носитель SAP на виртуальных жестких дисках Azure или создать виртуальную машину Azure в качестве файлового сервера, содержащего все необходимые установочные носители SAP.

#### Развертывание виртуальной машины с помощью конкретного универсального пользовательского образа
Если для вашей версии ОС или СУБД существуют особые требования по установке исправлений, представленные в Azure Marketplace образы могут не соответствовать вашим потребностям. В этом случае следует создать виртуальную машину с помощью собственного пользовательского образа ОС/СУБД для виртуальной машины, который можно развертывать многократно. Чтобы подготовить такой пользовательский образ к дублированию, на локальной виртуальной машине необходимо создать универсальный образ ОС. Для получения подробных сведений об обобщении виртуальной машины см. [руководство по развертыванию][deployment-guide].

Если вы уже установили содержимое SAP на локальную виртуальную машину (особенно важно для двухуровневых систем), параметры системы SAP можно изменить после развертывания виртуальной машины в Azure. Для этого выполните процедуру переименования экземпляра, которая поддерживается диспетчером подготовки программного обеспечения SAP (см. примечание SAP [1619720]). В противном случае программное обеспечение SAP можно установить позже после развертывания виртуальной машины Azure.
 
Что касается содержимого базы данных, используемого приложением SAP, можно либо создать это содержимое заново с помощью установки SAP, либо импортировать содержимое в Azure, используя виртуальный жесткий диск с резервной копией базы данных СУБД или задействовав возможности СУБД для прямого резервного копирования в хранилище Microsoft Azure. В этом случае можно также подготовить виртуальные жесткие диски с файлами данных и журналов СУБД локально, а затем импортировать их как диски в Azure. Однако передача данных СУБД, которые загружаются из локальной среды в Azure, будет осуществляться через виртуальные жесткие диски, которые необходимо подготовить локально.

#### Перемещение виртуальной машины из локальной среды в Azure с помощью специализированного диска
Предположим, вы хотите переместить определенную систему SAP из локальной среды в Azure (методика Lift and Shift — перенос с сохранением структуры). Для этого можно передать в Azure виртуальный жесткий диск, который содержит ОС, двоичные файлы SAP и конечные двоичные файлы СУБД, а также дополнительные виртуальные жесткие диски с файлами данных и журналов СУБД. В отличие от описанного выше сценария 2, вы сохраните на виртуальной машине Azure те же имя узла, идентификатор безопасности SAP и учетные записи пользователей SAP, которые были настроены в локальной среде. В этом случае создание универсального образа не требуется. Этот случай главным образом касается сценариев распределенного развертывания, при которых одна часть ландшафта SAP выполняется локально, а другая часть — в Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Высокая доступность и аварийное восстановление с использованием виртуальных машин Azure
Azure предоставляет следующие функции высокой доступности (HA) и аварийного восстановления (DR) для различных компонентов, которые могут использоваться для развертываний SAP и СУБД.

### Виртуальные машины, развернутые на узлах Azure
Для развернутых виртуальных машин платформа Azure не предоставляет таких функций, как динамическая миграция. Это означает, что при необходимости в техническом обслуживании на серверном кластере, где развернута виртуальная машина, эту машину потребуется остановить и перезапустить. Техническое обслуживание в Azure выполняется с помощью так называемых доменов обновления в кластерах серверов. Одновременно поддерживается обслуживание только одного домена обновления. В ходе такой перезагрузки обслуживание будет прервано на время выключения виртуальной машины, выполнения технического обслуживания и перезапуска виртуальной машины. Однако большинство поставщиков СУБД предоставляют функциональные возможности высокой доступности и аварийного восстановления, которые позволяют быстро перезапустить службы СУБД на другом узле в случае недоступности основного узла. Платформа Azure предоставляет функциональные возможности для распределения виртуальных машин, службы хранилища и других служб Azure между доменами обновления, чтобы гарантировать, что плановое техническое обслуживание или сбой инфраструктуры повлияют только на небольшое подмножество виртуальных машин или служб. Благодаря тщательному планированию можно добиться уровней доступности, сравнимых с локальными инфраструктурами.

Группы доступности Microsoft Azure — это логические группы виртуальных машин или служб, которые обеспечивают их распределение по разным доменам сбоя и обновления в кластере таким образом, чтобы в любой момент времени происходило завершение работы только одного узла (дополнительные сведения см. в [этой][virtual-machines-manage-availability] статье).

Их необходимо настроить в соответствии с назначением при развертывании виртуальных машин, как показано ниже.

![Определение группы доступности для конфигураций высокой доступности СУБД][dbms-guide-figure-200]

Если требуется создать высокодоступные конфигурации развертываний СУБД (независимо от используемых функций высокой доступности отдельных СУБД), для виртуальных машин СУБД потребуется следующее:

* Добавление виртуальных машин в ту же виртуальную сеть Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>).
* Виртуальные машины в высокодоступной конфигурации также должны находиться в той же подсети. Разрешение имен между разными подсетями невозможно в развертываниях только в облаке. В таком случае будет работать только разрешение IP-адресов. Для распределенного развертывания с использованием подключения "сеть — сеть" или ExpressRoute будет уже установлена сеть по крайней мере с одной подсетью. Разрешение имен будет выполняться в соответствии с локальными политиками AD и сетевой инфраструктурой.
[комментарий]: <> (пользователь MSSedusch: проверить, актуально ли до сих пор в ARM)

#### IP-адреса
Настоятельно рекомендуем использовать гибкую настройку виртуальных машин для высокодоступных конфигураций. Использовать IP-адреса для обращения к партнерам по высокой доступности в высокодоступной конфигурации в Azure рекомендуется только в том случае, если используются статические IP-адреса. В Azure существует два понятия "завершение работы".

* Завершение работы с помощью портала Azure или командлета Azure PowerShell Stop-AzureRmVM. В этом случае выполняется завершение работы виртуальной машины и отмена распределения. За учетную запись Azure для этой виртуальной машины больше не взимается плата. Оплачиваться будет только используемое хранилище. Однако если частный IP-адрес сетевого интерфейса не является статическим, этот IP-адрес освобождается, и нет никакой гарантии, что сетевой интерфейс снова получит старый IP-адрес после перезапуска виртуальной машины. При завершении работы через портал Azure или с помощью командлета Stop-AzureRmVM распределение памяти автоматически отменяется. Если распределение не следует отменять, используйте командлет Stop-AzureRmVM -StayProvisioned. 
* При завершении работы на уровне операционной системы эта виртуальная машина выключается, но распределение НЕ отменяется. Однако в этом случае с учетной записи Azure продолжает взиматься плата за виртуальную машину, несмотря на то что она выключена. В таком случае назначение IP-адреса остановленной виртуальной машины останется без изменений. Завершение работы виртуальной машины изнутри не приводит к автоматической отмене распределения.

Даже в распределенных сценариях завершение работы и отмена распределения по умолчанию означают отмену назначения IP-адресов виртуальной машине, даже если локальные политики в параметрах DHCP настроены иначе.

* Исключением является ситуация, когда сетевому интерфейсу назначается статический IP-адрес, как описано [здесь][virtual-networks-reserved-private-ip].
* В таком случае IP-адрес остается неизменным при условии, что сетевой интерфейс не удаляется.

> [AZURE.IMPORTANT] Чтобы поддерживать всю развернутую службу в простом и управляемом состоянии, настоятельно рекомендуется настроить партнерство виртуальных машин в конфигурации СУБД высокой доступности или аварийного восстановления в Azure таким образом, чтобы функционировало разрешение имен между разными задействованными виртуальными машинами.
 
## Развертывание мониторинга узлов
Для использования приложений SAP на виртуальных машинах Azure в производственных целях системе SAP требуется возможность получения данных мониторинга узлов с физических узлов, на которых работают виртуальные машины Azure. Требуется определенный уровень исправлений SAP HostAgent, включающий эту возможность в SAPOSCOL и SAP HostAgent. Точный уровень исправлений описан в примечании SAP [1409604].

Подробные сведения о развертывании компонентов, поставляющих данные узлов в SAPOSCOL и SAPHostAgent, и об управлении жизненным циклом этих компонентов см. в [руководстве по развертыванию][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Особенности Microsoft SQL Server

### IaaS в SQL Server
Начиная с Microsoft Azure, можно легко переносить существующие приложения SQL Server, построенные на платформе Windows Server, на виртуальные машины Azure. SQL Server на виртуальной машине позволяет снизить общую стоимость владения для операций развертывания и обслуживания корпоративных приложений, а также управления ими благодаря простому переносу этих приложений в Microsoft Azure. С помощью SQL Server на виртуальной машине Azure администраторы и разработчики могут по-прежнему использовать те же средства разработки и администрирования, которые доступны в локальной среде.

> [AZURE.IMPORTANT] Обратите внимание, что мы не обсуждаем базу данных SQL Microsoft Azure, которая представляет собой предложение в формате "платформа как услуга" на платформе Microsoft Azure. В этом документе рассматривается запуск продукта SQL Server в том виде, в каком он традиционно используется для локальных развертываний на виртуальных машинах Azure, с использованием функций инфраструктуры как услуги в Azure. Возможности и функции базы данных в этих двух предложениях различаются, и их не следует путать друг с другом. См. также: <https://azure.microsoft.com/services/sql-database/>
 
Прежде чем продолжить, настоятельно рекомендуем просмотреть [эту][virtual-machines-sql-server-infrastructure-services] документацию.

В следующих разделах упоминаются различные фрагменты документации по указанной выше ссылке. Также упоминаются особенности, связанные с SAP. Некоторые основные понятия описаны более подробно. Однако настоятельно рекомендуем сначала поработать с упомянутой выше документацией, прежде чем приступать к чтению документации, касающейся SQL Server.

Существуют некоторые специальные сведения об SQL Server в IaaS, которые следует знать перед продолжением:

* **Соглашение об уровне обслуживания для виртуальных машин**. Существует соглашение об уровне обслуживания для виртуальных машин, работающих в Azure, которое можно найти здесь: <https://azure.microsoft.com/support/legal/sla/>  
* **Поддержка версий SQL**. Для клиентов SAP на виртуальной машине Microsoft Azure поддерживается SQL Server 2008 R2 и более поздних версий. Более ранние выпуски не поддерживаются. Для получения дополнительных сведений просмотрите это общее [заявление о поддержке](https://support.microsoft.com/kb/956893). Обратите внимание, что в целом SQL Server 2008 также поддерживается корпорацией Майкрософт. Однако в SQL Server 2008 R2 были представлены важные функциональные возможности для SAP, поэтому SQL Server 2008 R2 является минимальной поддерживаемой версией для SAP. Помните, что в версиях SQL Server 2012 и 2014 были добавлены функции углубленной интеграции в сценарий IaaS (например, резервное копирование непосредственно в хранилище Azure). Поэтому в данном документе мы ограничиваемся версиями SQL Server 2012 и 2014 с последним уровнем исправления для Azure.
* **Поддержка функций SQL**. Большинство функций SQL Server поддерживаются на виртуальных машинах Microsoft Azure с некоторыми исключениями. **Создание отказоустойчивых кластеров SQL Server с использованием общих дисков не поддерживается**. Распределенные технологии, такие как зеркальное отображение базы данных, группы доступности AlwaysOn, репликация, доставка журналов и Service Broker, поддерживаются в пределах одного региона Azure. Также SQL Server AlwaysOn поддерживается между разными регионами Azure, как описано здесь: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>. Для получения дополнительных сведений просмотрите [заявление о поддержке](https://support.microsoft.com/kb/956893). Пример развертывания конфигурации AlwaysOn показан в [этой][virtual-machines-workload-template-sql-alwayson] статье. Кроме того, ознакомьтесь с практическими рекомендациями, описанными [здесь][virtual-machines-sql-server-infrastructure-services]. 
* **Производительность SQL**. Мы уверены, что размещенные в Microsoft Azure виртуальные машины будут работать очень хорошо по сравнению с другими общедоступными облачными предложениями виртуализации, но отдельные результаты могут отличаться. Ознакомьтесь с [этой][virtual-machines-sql-server-performance-best-practices] статьей.
* **Использование образов из Azure Marketplace**. Самый быстрый способ развертывания новой виртуальной машины Microsoft Azure — использование образа из Azure Marketplace. В Azure Marketplace имеются образы, содержащие SQL Server. Образы, в которых уже установлен SQL Server, нельзя сразу же использовать для приложений SAP NetWeaver. Причина в том, что в таких образах заданы параметры сортировки по умолчанию для SQL Server, которые отличаются от параметров, требующихся для систем SAP NetWeaver. Чтобы использовать такие образы, выполните действия, описанные в разделе [Использование образов SQL Server из Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Дополнительную информацию см. на странице [сведений о ценах](https://azure.microsoft.com/pricing/). Также важными ресурсами являются [руководство по лицензированию SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) и [руководство по лицензированию SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf).
 
### Рекомендации по конфигурации SQL Server для связанных с SAP установок SQL Server на виртуальных машинах Azure

#### Рекомендации по структуре виртуальных машин и виртуальных жестких дисков для связанных с SAP развертываний SQL Server
В соответствии с общим описанием исполняемые файлы SQL Server должны быть расположены или установлены на системном диске базового виртуального жесткого диска виртуальной машины (диск C:). Как правило, большая часть системных баз данных SQL Server не используется на высоком уровне рабочей нагрузкой SAP NetWeaver. Поэтому системные базы данных SQL Server (master, msdb и model) могут также оставаться на диске C:\\. Исключением может быть tempdb, для которой в случае некоторых рабочих нагрузок SAP ERP и любых рабочих нагрузок BW может потребоваться больший объем данных или объем операций ввода-вывода, с которым не справится исходная виртуальная машина. Для таких систем необходимо выполнить следующие действия.

* Переместите основные файлы данных tempdb на тот же логический диск, где находятся основные файлы данных базы данных SAP.
* Добавьте все дополнительные файлы данных tempdb на каждый из остальных логических дисков, содержащих файл данных базы данных пользователя SAP.
* Добавьте файл журнала базы данных tempdb на логический диск, содержащий файл журнала базы данных пользователя.
* **Исключительно для типов виртуальных машин, использующих локальные твердотельные накопители**: на вычислительном узле файлы данных и журналов tempdb могут располагаться на диске D:\\. Тем не менее можно рекомендовать использовать несколько файлов данных базы данных tempdb. Учтите, что тома диска D:\\ различаются в зависимости от типа виртуальной машины.
 
Эти конфигурации позволяют tempdb использовать больше пространства, чем может предоставить системный диск. Чтобы определить необходимый размер tempdb, можно проверить размеры tempdb в существующих системах, работающих локально. Кроме того, такая конфигурация обеспечит такие показатели операций ввода-вывода в tempdb, которые не могут быть предоставлены системным диском. Опять же, системы, работающие в локальной среде, можно использовать для мониторинга рабочей нагрузки ввода-вывода в отношении базы данных tempdb, чтобы определить показатели операций ввода-вывода, которые ожидаются в базе данных tempdb.

Конфигурация виртуальной машины, на которой выполняется SQL Server с базой данных SAP, а данные tempdb и файл журнала tempdb располагаются на диске D:\\, выглядит следующим образом.
 
![Эталонная конфигурация виртуальной машины Azure IaaS для SAP][dbms-guide-figure-300]

Учтите, что размеры диска D:\\ различаются в зависимости от типа виртуальной машины. В зависимости от требований к размеру базы данных tempdb может потребоваться связать файлы данных и журналов tempdb с файлами данных и журналов базы данных SAP в случаях, когда диск D:\\ слишком мал.

#### Форматирование виртуальных жестких дисков
Для SQL Server размер блока NTFS для виртуальных жестких дисков, содержащих файлы данных и журналов SQL Server, должен быть равен 64 КБ. Форматировать диск D:\\ нет необходимости. Он предварительно отформатирован.

Чтобы при восстановлении или создании баз данных предотвратить инициализацию файлов данных путем обнуления их содержимого, следует убедиться, что контекст пользователя, в котором выполняется служба SQL Server, имеет определенное разрешение. Обычно эти разрешения имеют пользователи из группы администраторов Windows. Если служба SQL Server выполняется в контексте пользователя без прав администратора Windows, необходимо назначить этому пользователю право "Выполнение задач по обслуживанию томов". Дополнительные сведения см. в следующей статье базы знаний Майкрософт: <https://support.microsoft.com/kb/2574695>
 
#### Влияние сжатия базы данных
В конфигурациях, в которых пропускная способность ввода-вывода может стать ограничивающим фактором, каждая мера, позволяющая сократить объем операций ввода-вывода, помогает растянуть рабочую нагрузку, которую можно запускать в сценарии IaaS, например Azure. Таким образом, если это еще не сделано, применение сжатия PAGE для SQL Server настоятельно рекомендуется как SAP, так и корпорацией Майкрософт перед отправкой существующих баз данных SAP в Azure.
 
Выполнять сжатие базы данных перед отправкой в Azure рекомендуется по следующим двум причинам:

* Объем отправляемых данных уменьшается.
* Длительность выполнения сжатия сокращается, при условии, что локально можно использовать более мощное оборудование с большим числом ЦП, более высокой пропускной способностью ввода-вывода или меньшими задержками ввода-вывода.
* Уменьшение размеров баз данных может сократить затраты на выделение места на дисках.

Сжатие баз данных работает на виртуальных машинах Azure так же, как и в локальной среде. Дополнительные сведения о том, как сжать существующую базу данных SQL Server для SAP, см. здесь: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>
  
### SQL Server 2014: хранение файлов базы данных непосредственно в хранилище BLOB-объектов Azure
SQL Server 2014 открывает возможность хранить файлы базы данных непосредственно в хранилище BLOB-объектов Azure без создания вокруг них "оболочки" виртуального жесткого диска. В частности, при использовании виртуальных машин хранилища Azure класса "Стандартный" или меньшего размера это позволяет реализовать сценарии, где можно обойти ограничения операций ввода-вывода, устанавливаемые ограниченным числом виртуальных жестких дисков, которые могут быть подключены к некоторым типам небольших виртуальных машин. Это возможно в случае с пользовательскими базами данных, но не с системными базами данных SQL Server. Также это возможно для файлов данных и журналов SQL Server. Если вы предпочитаете развернуть базу данных SQL Server SAP таким образом, не заключая ее в "оболочку" виртуальных жестких дисков, помните следующее.

* Используемая учетная запись хранения должна располагаться в том же регионе Azure, что и учетная запись, которая использовалась для развертывания виртуальной машины, на которой работает SQL Server.
* К этому методу развертывания также применяются перечисленные выше рекомендации, касающиеся распределения виртуальных жестких дисков по разным учетным записям хранения Azure. Это означает количество операций ввода-вывода относительно ограничений учетной записи хранения Azure.
[комментарий]: <> (пользователь MSSedusch: но при этом будет использоваться пропускная способность сети, а не хранилища, не так ли?)

Подробные сведения об этом типе развертывания приведены здесь: <https://msdn.microsoft.com/library/dn385720.aspx>
 
Для хранения файлов данных SQL Server непосредственно в хранилище Azure класса Premium необходима минимальная версия исправления SQL Server 2014, которая описана здесь: <https://support.microsoft.com/kb/3063054>. Хранение файлов данных SQL Server в хранилище Azure класса "Стандартный" работает с выпущенной версией SQL Server 2014. Однако те же пакеты исправлений содержат еще один ряд исправлений, которые делают прямое использование хранилища BLOB-объектов Azure для файлов данных и резервных копий SQL Server более надежным. Поэтому мы в целом рекомендуем использовать эти исправления.

### Расширение буферного пула SQL Server 2014
В SQL Server 2014 появилась новая функция, которая называется "Расширение буферного пула". Эта функция расширяет буферный пул SQL Server, который хранится в памяти, с помощью кэша второго уровня, поддерживаемого локальными твердотельными накопителями сервера или виртуальной машины. Это позволяет поддерживать в памяти рабочий набор данных большего размера. По сравнению с доступом к хранилищу Azure класса "Стандартный" доступ к расширению буферного пула, который хранится на локальных твердотельных накопителях виртуальной машины Azure, предоставляется во много раз быстрее. Таким образом, использование локального диска D:\\ таких типов виртуальных машин, которые обладают отличными показателями операций ввода-вывода и пропускной способности, может быть весьма разумным способом снизить нагрузку операций ввода-вывода на хранилище Azure и значительно улучшить время отклика на запросы. Это применимо особенно в том случае, если хранилище класса Premium не используется. В случае с хранилищем класса Premium и использованием кэша чтения Azure Premium на вычислительном узле (что рекомендуется для файлов данных) существенных различий быть не должно. Причина в том, что оба кэша (расширение буферного пула для SQL Server и кэш чтения хранилища класса Premium) используют локальные диски вычислительных узлов. Дополнительные сведения об этих возможностях см. в следующей документации: <https://msdn.microsoft.com/library/dn133176.aspx>

### Рекомендации по резервному копированию и восстановлению для SQL Server
При развертывании SQL Server в Azure методику резервного копирования необходимо пересмотреть. Даже если система не используется для производственных задач, необходимо периодически производить резервное копирование базы данных SAP, размещенной на сервере SQL Server. Так как в службе хранилища Azure хранятся три образа, резервное копирование становится менее важным с точки зрения восстановления хранилища после сбоя. Основная причина соблюдения надлежащего плана резервного копирования и восстановления состоит в том, что, имея возможность восстановить состояние на определенный момент времени, вы можете исправлять логические или пользовательские ошибки. Следовательно, резервные копии используются для восстановления состояния базы данных на определенный момент времени или для создания другой системы в Azure путем копирования существующей базы данных. Например, с помощью восстановления из резервной копии вы можете перейти от двухуровневой конфигурации SAP к трехуровневой для той же системы.

Существует три разных способа резервного копирования SQL Server в хранилище Azure.
 
1. SQL Server 2012 CU4 и более поздних версий может собственными средствами выполнять резервное копирование баз данных на URL-адрес. Подробное описание см. в блоге [New functionality in SQL Server 2014 — Part 5 — Backup/Restore Enhancements](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx) (Новые функциональные возможности SQL Server 2014. Часть 5: усовершенствования резервного копирования и восстановления). См. раздел [SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 4 (CU4) и более поздние версии][dbms-guide-5.5.1].
1. Выпуски SQL Server до SQL 2012 CU4 могут использовать функцию перенаправления для резервного копирования на виртуальный жесткий диск и по сути перемещать поток записи к расположению хранилища Azure, которое было настроено. См. раздел [SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3) и более ранние версии][dbms-guide-5.5.2].
1. Последний метод — применение обычной команды резервного копирования SQL Server на диск к устройству виртуального жесткого диска. Это идентично схеме локального развертывания и не рассматривается в данном документе подробно.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 4 (CU4) и последующие выпуски
Эта функция позволяет выполнять резервное копирование непосредственно в хранилище BLOB-объектов Azure. Без этого метода резервное копирование следует выполнять на другие виртуальные жесткие диски Azure. В таком случае будет использоваться емкость операций ввода-вывода и виртуальных жестких дисков. Идея заключается в следующем:
 
 ![Резервное копирование с SQL Server 2012 в хранилище BLOB-объектов Microsoft Azure][dbms-guide-figure-400]

Преимущество в этом случае состоит в том, что не нужно тратить пространство виртуальных жестких дисков на хранение резервных копий SQL Server. Поэтому можно выделить меньше виртуальных жестких дисков и использовать всю пропускную способность их операций ввода-вывода для файлов данных и журналов. Обратите внимание, что максимальный размер резервной копии ограничен 1 ТБ, как описано в разделе, посвященном ограничениям, следующей статьи: <https://msdn.microsoft.com/library/dn435916.aspx#limitations>. Если размер резервной копии, несмотря на использование сжатия резервных копий SQL Server, превышает 1 ТБ, необходимо использовать функциональные возможности, описанные в разделе [SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3) и более ранние версии][dbms-guide-5.5.2] этого документа.

В [связанной документации](https://msdn.microsoft.com/library/dn449492.aspx), в которой описано восстановление баз данных из резервных копий в хранилище BLOB-объектов Azure, рекомендуется не выполнять восстановление непосредственно из хранилища BLOB-объектов Azure, если размер резервных копий превышает 25 ГБ. Рекомендации в данной статье основаны просто на соображениях производительности, а не на функциональных ограничениях. Поэтому для разных случаев могут применяться разные условия.

Документацию по настройке и использованию преимуществ этого типа резервного копирования можно найти в [этом](https://msdn.microsoft.com/library/dn466438.aspx) руководстве.
 
Пример последовательности действий см. [здесь](https://msdn.microsoft.com/library/dn435916.aspx).

При автоматизации резервного копирования важнее всего обеспечить различия в именовании BLOB-объектов для каждой резервной копии. В противном случае они будут перезаписаны, и цепочка восстановления разорвется.
 
Чтобы не перепутать три разных типа резервных копий, рекомендуется создать разные контейнеры в учетной записи хранения, используемой для резервных копий. Тип контейнера может предполагать хранение только виртуальных машин или виртуальных машин и резервных копий. Схема может выглядеть следующим образом:
 
 ![Резервное копирование с SQL Server 2012 в хранилище BLOB-объектов Microsoft Azure. Разные контейнеры в отдельной учетной записи хранения][dbms-guide-figure-500]

В приведенном выше примере резервное копирование не будет выполняться в ту же учетную запись хранения, где развернуты виртуальные машины. Для резервных копий будет специально создана новая учетная запись хранения. В учетной записи хранения будет создано несколько контейнеров для разных типов резервных копий. Эти контейнеры будут связаны с определенной виртуальной машиной. Такая сегментация упрощает администрирование резервных копий разных виртуальных машин.

Большие двоичные объекты, в которые непосредственно записываются резервные копии, не увеличивают количество виртуальных жестких дисков виртуальной машины. Следовательно, можно использовать максимум виртуальных жестких дисков, подключенных к конкретной SKU виртуальной машины, для файлов данных и журнала транзакций и при этом выполнять резервное копирование в контейнер хранилища.

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3) и предыдущие выпуски
Первый шаг, который необходимо выполнить для резервного копирования непосредственно в хранилище Azure, — скачать MSI-файл, который связан с [этой](https://www.microsoft.com/download/details.aspx?id=40740) статьей базы знаний.
 
Скачайте 64-разрядный файл установки и документацию. Этот файл устанавливает программу под названием "Microsoft SQL Server Backup to Microsoft Azure Tool". Внимательно прочитайте документацию по продукту. По сути это средство работает следующим образом.

* Со стороны SQL Server определяется расположение резервной копии SQL Server на диске (не используйте для этого диск D:\\).
* Это средство позволяет определить правила, которые могут использоваться для направления разных типов резервных копий в разные контейнеры хранилища Azure.
* После определения правил средство будет направлять поток записи резервной копии на один из дисков или виртуальных жестких дисков в расположение хранилища Azure, заданное ранее.
* Средство оставит небольшой файл-заглушку размером в несколько килобайт на диске или виртуальном жестком диске, который был определен для резервной копии SQL Server. **Этот файл следует оставить в хранилище, так как он потребуется для восстановления из хранилища Azure.**
	* Если вы потеряли файл-заглушку (например, утрачен носитель, содержащий файл-заглушку) и выбрали вариант резервного копирования в учетную запись хранилища Microsoft Azure, файл-заглушку можно восстановить из хранилища Microsoft Azure, загрузив его из контейнера хранилища, в который он был помещен. Затем файл-заглушку следует поместить в папку на локальном компьютере, в которой данное средство настроено на обнаружение и отправку того же контейнера с тем же паролем шифрования, если в исходном правиле применялось шифрование. 

Это означает, что описанная выше схема для последних версий SQL Server может быть реализована и для тех версий SQL Server, которые не разрешают непосредственно обращаться к расположению хранилища Azure.
 
Этот способ не следует использовать с более новыми версиями SQL Server, которые изначально поддерживают резервное копирование в хранилище Azure. Исключениями являются случаи, когда ограничения встроенного резервного копирования в Azure блокируют его выполнение.

#### Альтернативные способы резервного копирования баз данных SQL Server
Альтернативным способом резервного копирования баз данных является присоединение дополнительных виртуальных жестких дисков к виртуальной машине, которая используется для хранения резервных копий. В таком случае необходимо убедиться, что виртуальные жесткие диски не переполняются. Если это так, необходимо отсоединить виртуальный жесткий диск, "архивировать" его и заменить новым пустым виртуальным жестким диском. При использовании этого способа возникнет необходимость хранить такие виртуальные жесткие диски в отдельных учетных записях хранения Azure, отличающихся от учетных записей, содержащих виртуальные жесткие диски с файлами базы данных.

Еще один способ — использовать виртуальную машину большого размера, к которой можно подключить множество виртуальных жестких дисков. Например, виртуальную машину D14 с 32 виртуальными жесткими дисками. Дисковые пространства позволяют построить гибкую среду, где можно создавать общие папки, которые затем будут использоваться как целевые папки резервного копирования для разных серверов СУБД.
 
Некоторые практические рекомендации также описаны [здесь](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx).

#### Рекомендации по ускорению резервного копирования и восстановления
Как и при развертывании на сервер без операционной системы, скорость резервного копирования и восстановления зависит от количества томов, которые могут считываться параллельно, а также от пропускной способности этих томов. Кроме того, загрузка ЦП при сжатии резервной копии может играть значительную роль на виртуальных машинах, имеющих не более восьми потоков ЦП. Исходя из сказанного выше, можно сделать следующие выводы.

* Чем меньше виртуальных жестких дисков используется для хранения файлов данных, тем меньше общая пропускная способность при чтении.
* Чем меньше потоков ЦП на виртуальной машине, тем сильнее влияние сжатия резервной копии.
* Чем меньше целевых объектов (BLOB-объектов или виртуальных жестких дисков), на которые записываются резервные копии, тем меньше пропускная способность.
* Чем меньше размер виртуальной машины, тем меньше квота пропускной способности хранилища для записи и чтения из хранилища Azure. Это не зависит от того, где хранятся резервные копии — непосредственно в BLOB-объектах Azure или на виртуальных жестких дисках, которые в свою очередь хранятся в BLOB-объектах Azure.

При использовании BLOB-объекта хранилища Microsoft Azure в качестве целевого объекта резервного копирования в последних версиях можно назначить только один целевой URL-адрес для каждой конкретной резервной копии.
 
Однако при использовании средства Microsoft SQL Server Backup to Microsoft Azure Tool в предыдущих версиях можно определить несколько целевых расположений для файлов. При наличии нескольких целевых папок резервное копирование можно масштабировать, а его пропускная способность увеличивается. В результате в учетной записи службы хранилища Azure также появится несколько файлов. В ходе тестирования оказалось, что использование нескольких целевых папок для файлов определенно позволяет достичь пропускной способности, которая достигается с помощью расширений резервного копирования, впервые реализованных в версии SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом 4 (CU4). Также отсутствует ограничение в 1 ТБ, присущее встроенной функции резервного копирования Azure.

При этом следует помнить, что пропускная способность также зависит от расположения учетной записи службы хранения Azure, которая используется для резервного копирования. Хорошей идеей будет использовать учетную запись хранения, расположенную не в том же регионе, в котором запущены виртуальные машины. Например, виртуальная машина может быть расположена в Западной Европе, а учетная запись хранения, используемая для резервного копирования, — в Северной Европе. Безусловно, это окажет влияние на пропускную способность резервного копирования: вам, скорее всего, не удастся достичь пропускной способности в 150 МБ/с, которая возможна, когда целевое хранилище и виртуальные машины работают в одном и том же региональном центре обработки данных.

#### Управление BLOB-объектами при резервном копировании
Резервными копиями пользователь должен управлять самостоятельно. При частом резервном копировании журнала транзакций создается множество больших двоичных объектов, поэтому администрирование этих объектов может легко перегрузить портал Azure. Поэтому рекомендуется использовать обозреватель хранилищ Azure. Доступно несколько хороших обозревателей, которые помогают управлять учетной записью хранения Azure.

* Microsoft Visual Studio с установленным пакетом SDK для Azure (<https://azure.microsoft.com/downloads/>)
* Обозреватель службы хранилища Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* Средства сторонних производителей

[комментарий]: <> (Еще не поддерживается для ARM) [комментарий]: <> (#### резервная копия виртуальной машины Azure) [комментарий]: <> (Резервное копирование виртуальных машин в системе SAP можно осуществлять с помощью функции резервного копирования виртуальных машин Azure. Функция резервного копирования виртуальных машин Azure появилась в начале 2015 г.; сейчас это стандартный способ создания полных резервных копий виртуальных машин в Azure. Служба архивации Azure хранит резервные копии в Azure, позволяя заново восстанавливать виртуальные машины.) [комментарий]: <> (Виртуальные машины с запущенными базами данных также подлежат аналогичному резервному копированию, если СУБД поддерживает Windows VSS (службу теневого копирования томов — <https://msdn.microsoft.com/library/windows/desktop/bb968832.aspx>), как, например, SQL Server. Поэтому использование резервного копирования виртуальных машин Azure позволяет получить восстанавливаемую резервную копию базы данных SAP. При этом следует помнить, что восстановление баз данных до определенной точки во времени невозможно, если резервная копия была создана с помощью службы архивации виртуальных машин Azure. Поэтому мы рекомендуем создавать резервные копии баз данных средствами СУБД, а не использовать службу архивации виртуальных машин Azure.) [комментарий]: <> (Чтобы ознакомиться с резервным копированием виртуальных машин Azure, начните с этой страницы: <https://azure.microsoft.com/documentation/services/backup/>)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Использование образов SQL Server из Microsoft Azure Marketplace
Корпорация Майкрософт размещает в Azure Marketplace виртуальные машины, которые уже содержат версии SQL Server. Следовательно, клиенты SAP, которым нужны лицензии на SQL Server и Windows, могут удовлетворить потребность в лицензиях, развертывая виртуальные машины с уже установленной средой SQL Server. Чтобы использовать такие образы для SAP, необходимо учитывать следующие факторы.

* Неознакомительные версии SQL Server стоят дороже, чем обычная виртуальная машина "только для Windows", развернутая из Azure Marketplace. Сравнение цен см. здесь: <https://azure.microsoft.com/pricing/details/virtual-machines/> и здесь: <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql>. 
* Использовать можно только те выпуски SQL Server, которые поддерживаются SAP, например SQL Server 2012.
* Параметры сортировки экземпляра SQL Server, который устанавливается на виртуальных машинах из Azure Marketplace, не соответствуют параметрам, необходимым SAP NetWeaver для запуска экземпляра SQL Server. Параметры сортировки можно изменить, выполнив инструкции из следующего раздела.

#### Изменение параметров сортировки SQL Server на виртуальной машине с Microsoft Windows и SQL Server
Так как в образах SQL Server в Azure Marketplace не настроено использование параметров сортировки, необходимых для приложений SAP NetWeaver, их необходимо изменить сразу же после развертывания. Для SQL Server 2012 это можно сделать с помощью следующих действий сразу после того, как виртуальная машина будет развернута и администратор сможет выполнить в нее вход.

* Откройте окно командной строки Windows с правами администратора.
* Перейдите в каталог C:\\Program Files\\Microsoft SQL Server\\110\\Setup Bootstrap\\SQLServer2012.
* Выполните команду: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=<local_admin_account_name> /SQLCOLLATION=SQL\_Latin1\_General\_Cp850\_BIN2   
	* `<local_admin_account_name`> — это учетная запись, которая определена как учетная запись администратора при развертывании виртуальной машины в первый раз через коллекцию.

Процесс должен занять только несколько минут. Чтобы проверить, завершился ли этот шаг правильным результатом, выполните следующие действия.

* Откройте среду SQL Server Management Studio.
* Откройте окно запроса.
* Выполните команду sp\_helpsort в базе данных master SQL Server.

Правильный результат должен выглядеть следующим образом:

	Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Если результат выглядит иначе, ОСТАНОВИТЕ развертывание SAP и выясните, почему команда установки сработала не так, как ожидалось. Развертывание приложений SAP NetWeaver в экземпляре SQL Server с кодовыми страницами SQL Server, отличающимися от упомянутой выше, **НЕ** поддерживается.

### Высокая доступность SQL Server для SAP в Azure
Как упоминалось ранее в данном документе, невозможно создать общее хранилище, которое необходимо для использования наиболее старой функции высокой доступности SQL Server. Эта функция предусматривала установку двух или более экземпляров SQL Server в отказоустойчивом кластере Windows Server (WSFC) с использованием общего диска для пользовательских баз данных (и в конечном счете tempdb). Этот способ обеспечения высокой доступности долгое время был стандартом, который также поддерживается в SAP. Поскольку Azure не поддерживает общее хранилище, невозможно реализовать конфигурации высокого уровня доступности SQL Server с конфигурацией кластера общих дисков. Однако многие другие методы обеспечения высокой доступности остаются возможными и описаны в следующих разделах.

[комментарий]: <> (Статья по-прежнему ссылается на ASM) [комментарий]: <> (Перед чтением информации о различных конкретных технологиях высокой доступности, пригодных для SQL Server в Azure, можно ознакомиться с документом, который предоставляет дополнительные сведения и указатели [здесь][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions])

#### Доставка журналов SQL Server
Одним из способов обеспечения высокого уровня доступности (HA) является доставка журналов SQL Server. Если в конфигурации высокой доступности виртуальные машины имеют работающее разрешение имен, проблемы нет и настройка в Azure не будет отличаться от любой настройки, которая выполняется локально. Мы не рекомендуем полагаться только на разрешение IP-адресов. Дополнительную информацию о настройке доставки журналов и принципах их доставки см. в следующей документации:

<https://technet.microsoft.com/library/ms187103.aspx>
 
Чтобы в действительности добиться высокой доступности, необходимо развернуть виртуальные машины, находящиеся в такой конфигурации доставки журналов в одной группе доступности Azure.

#### Зеркальное отображение базы данных
Зеркальное отображение базы данных в поддерживаемом SAP виде (см. примечание SAP [965908]) полагается на определение партнера по отработке отказа в строке подключения SAP. При подключении между организациями предполагается, что две виртуальные машины находятся в одном и том же домене, а пользовательский контекст, в котором запускаются два экземпляра SQL Server, является также пользователями домена, которые имеют достаточно прав в двух задействованных экземплярах SQL Server. Таким образом, настройка зеркального отображения базы данных в Azure не отличается от обычной локальной установки и конфигурации.

Самый простой способ полностью облачного развертывания — настроить другой домен в Azure так, чтобы эти виртуальные машины СУБД (а в идеале выделенные виртуальные машины SAP) находились в пределах одного домена.

Если домен невозможен, можно также использовать сертификаты для конечных точек зеркального отображения базы данных, как описано здесь: <https://technet.microsoft.com/library/ms191477.aspx>

Руководство по настройке зеркального отображения базы данных в Azure можно найти здесь: <https://technet.microsoft.com/library/ms189852.aspx>

#### AlwaysOn
Поскольку AlwaysOn поддерживается для SAP в локальной среде (см. примечание SAP [1772688]), поддерживается использование этой функции в сочетании с SAP в Azure. То, что вы не можете создавать общие диски в Azure, не означает, что нельзя создать конфигурацию отказоустойчивого кластера Windows Server (WSFC) с AlwaysOn между разными виртуальными машинами. Это только означает отсутствие возможности использовать общий диск в качестве кворума в конфигурации кластера. Таким образом, можно создать конфигурацию WSFC AlwaysOn в Azure и просто не выбирать тип кворума, в котором используется общий диск. Среда Azure, в которой развертываются эти виртуальные машины, должна распознавать виртуальные машины по имени, и эти виртуальные машины должны находиться в одном и том же домене. Это касается развертываний только в Azure и распределенных развертываний. Существуют особые рекомендации по развертыванию прослушивателя группы доступности SQL Server (не путать с группой доступности Azure), так как Azure на данный момент не разрешает просто создать объект AD/DNS, как это возможно в локальной среде. Таким образом, необходимо выполнить ряд отдельных шагов по установке для преодоления характерного поведения Azure.

Ниже приведены некоторые рекомендации по использованию прослушивателя группы доступности.

* Использование прослушивателя группы доступности возможно, только если в качестве гостевой ОС виртуальной машины используется Windows Server 2012 или Windows Server 2012 R2. Если вы используете Windows Server 2012 убедитесь, что применено следующее исправление: <https://support.microsoft.com/kb/2854082> 
* Для Windows Server 2008 R2 такое исправление отсутствует и AlwaysOn необходимо использовать таким же образом, как зеркальное отображение базы данных, указав партнера по отработке отказа в строке подключений (осуществляется через параметр SAP default.pfl со значением dbs/mss/server — см. примечание SAP [965908]).
* При использовании прослушивателя группы доступности виртуальные машины баз данных необходимо подключить к выделенному Load Balancer. Разрешение имен в полностью облачных развертываниях требует, чтобы все виртуальные машины системы SAP (серверы приложений, сервер СУБД и сервер (A)SCS) находились в одной виртуальной сети или чтобы на уровне приложений SAP осуществлялась поддержка файла etc\\host для разрешения имен виртуальных машин SQL Server. Во избежание назначения системой Azure новых IP-адресов при случайном завершении работы обеих виртуальных машин следует назначить статические IP-адреса сетевым интерфейсам этих виртуальных машин в конфигурации AlwaysOn (определение статического IP-адреса описано в [этой][virtual-networks-reserved-private-ip] статье)  

[комментарий]: <> (Старые блоги)
[комментарий]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 

* Особые действия требуются при построении конфигурации кластера WSFC, где кластеру требуется назначить специальный IP-адрес, так как Azure с текущими функциональными возможностями назначит имени кластера тот же IP-адрес, что и у узла, на котором создается кластер. Это означает, что необходимо вручную назначить кластеру другой IP-адрес.
* Прослушиватель группы доступности будет создан в Azure с конечными точками TCP/IP, назначенными виртуальным машинам, на которых запускаются первичная и вторичная реплики группы доступности.
* Может возникнуть необходимость обеспечить безопасность этих конечных точек с помощью списков управления доступом.

[комментарий]: <> (TODO — старый блог)
[комментарий]: <> (Подробные инструкции и потребности установки конфигурации AlwaysOn в Azure лучше всего реализовывать после прохождения руководства, которое доступно [здесь][virtual-machines-sql-server-alwayson-availability-groups-powershell])
[комментарий]: <> (Установка AlwaysOn с предварительной настройкой через коллекцию Azure <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[комментарий]: <> (Создание прослушивателя группы доступности лучше всего описано в [этом][virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener] руководстве)
[комментарий]: <> (Защита сетевых конечных точек с помощью списков управления доступом лучше всего объясняется здесь:)
[комментарий]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[комментарий]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[комментарий]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)
[комментарий]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>)

Группу доступности AlwaysOn SQL Server можно также развернуть в разных регионах Azure. В этой функции будет задействовано подключение Azure между виртуальными сетями ([подробнее][virtual-networks-configure-vnet-to-vnet-connection]).
[комментарий]: <> (TODO старый блог)
[комментарий]: <> (Настройка групп доступности AlwaysOn SQL Server при таком сценарии описана здесь: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>)

#### Сводка по высокой доступности SQL Server в Azure
Учитывая тот факт, что хранилище Azure осуществляет защиту содержимого, существует на одну причину меньше настаивать на образе горячей замены. Это означает, что в вашем сценарии обеспечения высокой доступности необходимо реализовать защиту только от следующих ситуаций.

* Недоступность виртуальной машины в целом из-за технического обслуживания в серверном кластере Azure или по другим причинам
* Проблемы с программным обеспечением в экземпляре SQL Server
* Защита от ошибок пользователей при удалении данных, когда требуется восстановление на определенный момент времени

При рассмотрении похожих технологий можно утверждать, что первые две ситуации может решить зеркальное отображение базы данных или AlwaysOn, а третью ситуацию — только доставка журналов.

Вам нужно будет сбалансировать более сложную настройку AlwaysOn (по сравнению с зеркальным отображением базы данных) преимуществами AlwaysOn. Эти преимущества перечислены ниже.

*	Вторичные реплики для чтения.
*	Резервные копии из вторичных реплик.
*	Улучшенная масштабируемость.
*	Несколько вторичных реплик.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Общие сведения об SQL Server для SAP в Azure
Это руководство содержит много рекомендаций, и мы советуем прочитать его несколько раз перед планированием развертывания в Azure. В целом необходимо учитывать десять основных рекомендаций, касающихся СУБД в Azure.

[комментарий]: <> (в 2,3 раза более высокая пропускная способность, чем что? Чем один виртуальный жесткий диск?)
1. Используйте новейшую версию СУБД, например SQL Server 2014, которая обладает максимумом преимуществ в Azure. Для SQL Server это версия SQL Server 2012 SP1 CU4, включающая функцию резервного копирования в хранилище Azure. Однако в сочетании с SAP мы бы рекомендовали минимум SQL Server 2014 SP1 CU1 или SQL Server 2012 SP2 и новейший пакет CU.
1. Тщательно планируйте ландшафт системы SAP в Azure, чтобы сбалансировать структуру файлов данных и ограничения Azure.
	* Не устанавливайте слишком много виртуальных жестких дисков, но обеспечьте достаточное количество для достижения необходимого числа операций ввода-вывода.
	* Помните, что количество операций ввода-вывода также ограничено для каждой учетной записи хранения Azure и что количество учетных записей хранения ограничено для каждой подписки Azure ([дополнительная информация][azure-subscription-service-limits]). 
	* Создавайте stripe-массив из виртуальных жестких дисков только при необходимости обеспечить более высокую пропускную способность.
1. Никогда не устанавливайте программное обеспечение и не помещайте файлы, которые требуется сохранять на постоянной основе, на диск D:\\, так как он является непостоянным, и любое содержимое этого диска будет потеряно при перезагрузке Windows.
1. Не используйте кэширование виртуальных жестких дисков Azure VHD для хранилища Azure Standard.
1. Не используйте геореплицированные учетные записи хранилища Azure. Используйте локальную избыточность для рабочих нагрузок СУБД.
1. Используйте решение HA/DR поставщика СУБД для репликации данных из базы данных.
1. Всегда используйте разрешение имен, не полагайтесь на IP-адреса.
1. Используйте наибольшую возможную степень сжатия базы данных. Для SQL Server это сжатие страниц.
1. Соблюдайте осторожность при использовании образов SQL Server из Azure Marketplace. При использовании образа SQL Server необходимо изменить параметры сортировки экземпляра, прежде чем устанавливать на него систему SAP NetWeaver.
1. Установите и настройте мониторинг узла SAP для Azure, как описано в [руководстве по развертыванию][deployment-guide].

## Особенности SAP ASE в Windows
Начиная с Microsoft Azure, можно легко переносить существующие приложения SAP ASE на виртуальные машины Azure. SAP ASE на виртуальной машине позволяет снизить общую стоимость владения для развертывания и обслуживания приложений уровня организации, а также управления ими благодаря переносу этих приложений в Microsoft Azure. С помощью SAP ASE на виртуальной машине Azure администраторы и разработчики могут продолжать использовать те же средства разработки и администрирования, которые доступны в локальной среде.

Существует соглашение об уровне обслуживания для виртуальных машин Azure, которое можно найти здесь: <https://azure.microsoft.com/support/legal/sla>

Мы уверены, что размещенные в Microsoft Azure виртуальные машины будут работать очень хорошо по сравнению с другими предложениями виртуализации в общедоступном облаке, но некоторые результаты могут быть разными. Номера SAPS размеров SAP для различных сертифицированных SAP SKU виртуальных машин будут предоставлены в отдельном примечании SAP [1928533].

Инструкции и рекомендации по использованию службы хранилища Azure, развертыванию виртуальных машин SAP или мониторингу SAP применяются к развертываниям SAP ASE в сочетании с приложениями SAP, как указано в первых четырех главах этого документа.

### Поддержка версий SAP ASE 
SAP в настоящее время поддерживает версию SAP ASE 16.0 для использования с продуктами SAP Business Suite. Все обновления для сервера SAP ASE, а также драйверы JDBC и ODBC для продуктов SAP Business Suite доступны только в SAP Service Marketplace по адресу <https://support.sap.com/swdc>.

Для установки в локальной среде не следует загружать обновления для сервера SAP ASE или драйверы JDBC и ODBC непосредственно с веб-сайтов Sybase. Подробные сведения об исправлениях, которые поддерживаются для использования с продуктами SAP Business Suite в локальной среде и на виртуальных машинах Azure, см. в следующих примечаниях SAP:

* [1590719]
* [1973241]
 
Общие сведения о запуске SAP Business Suite в SAP ASE можно найти на сайте [SCN](https://scn.sap.com/community/ase)

### Рекомендации по конфигурации SAP ASE для связанных с SAP установок SAP ASE на виртуальных машинах Azure

#### Структура развертывания SAP ASE
В соответствии с общим описанием исполняемые файлы SAP ASE должны быть расположены или установлены на системном диске базового виртуального жесткого диска виртуальной машины (диск C:). Как правило, большинство баз данных системы и средств SAP ASE не особо активно используются рабочей нагрузкой SAP NetWeaver. Поэтому базы данных системы и средств (master, model, saptools, sybmgmtdb, sybsystemdb) могут также оставаться на диске C:\\.

Исключением может быть временная база данных, содержащая все рабочие таблицы и временные таблицы, созданные SAP ASE, что в случае некоторых рабочих нагрузок SAP ERP и всех рабочих нагрузок BW может потребовать большего объема данных или объема операций ввода-вывода, с которым не справится базовый виртуальный жесткий диск исходной виртуальной машины (диск C:).
 
В зависимости от версии SAPInst или SWPM, использованной для установки системы, база данных может содержать следующие компоненты.

* Отдельную базу tempdb SAP ASE, которая создается при установке SAP ASE.
* Базу tempdb SAP ASE, созданную при установке SAP ASE, и дополнительную базу saptempdb, созданную процедурой установки SAP.
* Базу tempdb SAP ASE, созданную при установке SAP ASE, и дополнительную базу tempdb, созданную вручную (например, согласно примечанию SAP [1752266]) для соответствия особым требованиям к tempdb ERP/BW.

При определенных рабочих нагрузках ERP или всех рабочих нагрузках BW имеет смысл по соображениям производительности поддерживать работу устройств tempdb дополнительно созданной базы данных tempdb (с помощью SWPM или вручную) на любом диске, кроме C:. При отсутствии дополнительной базы данных tempdb рекомендуется ее создать (примечание SAP [1752266]).

Для таких систем необходимо выполнить следующие действия над дополнительно созданной базой данных tempdb.

* Переместите первое устройство tempdb на первое устройство базы данных SAP.
* Добавьте устройства tempdb на каждый виртуальный жесткий диск, содержащий устройства базы данных SAP.

Эта конфигурация позволяет tempdb потреблять больше пространства, чем может предоставить системный диск. Для справки можно проверить размеры устройств tempdb в существующих системах, работающих локально. Или же такая конфигурация обеспечит такие показатели операций ввода-вывода в tempdb, которые не могут быть предоставлены системным диском. Опять же, системы, работающие в локальной среде, можно использовать для мониторинга рабочей нагрузки ввода-вывода на базу данных tempdb.

Никогда не помещайте устройства SAP ASE на диск D:\\ виртуальной машины. Это правило также применяется к tempdb, даже если содержащиеся в tempdb объекты являются временными.

#### Влияние сжатия базы данных
В конфигурациях, в которых пропускная способность ввода-вывода может стать ограничивающим фактором, каждая мера, позволяющая сократить объем операций ввода-вывода, помогает растянуть рабочую нагрузку, которую можно запускать в сценарии IaaS, например Azure. Таким образом, настоятельно рекомендуется обязательно использовать сжатие SAP ASE перед отправкой существующей базы данных SAP в Azure.

Рекомендация выполнять сжатие перед отправкой в Azure, если оно еще не реализовано, дается по нескольким причинам.

* Объем отправляемых в Azure данных уменьшается.
* Длительность выполнения сжатия короче, при условии, что локально можно использовать более мощное оборудование с большим числом ЦП, более высокой пропускной способностью ввода-вывода или меньшими задержками ввода-вывода.
* Уменьшение размеров баз данных может сократить затраты на выделение места на дисках.

Сжатие данных и LOB работает на виртуальных машинах Azure так же, как и в локальной среде. Дополнительные сведения о том, как проверить, используется ли уже сжатие в существующей базе данных SAP ASE, см. в примечании SAP [1750510].

#### Использование DBACockpit для мониторинга экземпляров баз данных
Для систем SAP, в которых используется SAP ASE в качестве платформы баз данных, средство DBACockpit доступно в виде встроенных окон браузера в DBACockpit для транзакции или в виде Webdynpro. Однако полный набор функций для мониторинга и администрирования базы данных доступен только в реализации Webdynpro средства DBACockpit.

Как и для локальных систем, требуется выполнить ряд действий, чтобы включить все функциональные возможности SAP NetWeaver, используемые в реализации Webdynpro средства DBACockpit. См. информацию в примечании SAP [1245200], чтобы включить использование средств Webdynpro и создать необходимые. При соблюдении инструкций в упомянутых выше примечаниях будет также выполнена настройка Internet Communication Manager (icm) вместе с портами, используемыми для подключений http и https. Настройка по умолчанию для http выглядит следующим образом:

> icm/server\_port\_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server\_port\_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600

Ссылки, создаваемые в DBACockpit для транзакции, будут выглядеть примерно так:

> https://`<полное\_имя\_узла`>:44300/sap/bc/webdynpro/sap/dba\_cockpit
> 
> http://`<полное\_имя\_узла`>:8000/sap/bc/webdynpro/sap/dba\_cockpit

В зависимости от того, подключена ли виртуальная машина Azure, на которой размещается система SAP, в режиме "сеть — сеть", в режиме многосетевого подключения или ExpressRoute (распределенное развертывание), а также от способа этого подключения, необходимо убедиться, что ICM использует полное имя узла, которое может быть разрешено на компьютере, с которого вы пытаетесь открыть DBACockpit. Чтобы узнать, как ICM определяет полное имя узла в зависимости от параметров профиля, а также явным образом задать параметр icm/host\_name\_full (если необходимо), см. примечание SAP [773830].

Если вы развернули виртуальную машину только в облачной среде (без подключения между локальной средой и Azure), вам необходимо определить общедоступный IP-адрес и метку домена. Тогда формат общедоступного DNS-имени виртуальной машины будет выглядеть так:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com

Дополнительную информацию о DNS-имени см. [здесь][virtual-machines-azurerm-versus-azuresm].

Если для параметра профиля SAP icm/host\_name\_full задать DNS-имя виртуальной машины Azure, ссылка может выглядеть так:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit

> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

В таком случае необходимо:

* добавить правила для входящего трафика в группу безопасности сети на портале Azure для портов TCP/IP, используемых для связи с ICM;
* добавить правила для входящего трафика в конфигурацию брандмауэра Windows для портов TCP/IP, используемых для связи с ICM.

Для автоматического импорта всех доступных исправлений рекомендуется периодически применять примечания SAP, содержащие наборы исправлений, которые относятся к вашей версии SAP:

* [1558958];
* [1619967];
* [1882376].

Дополнительные сведения о панели DBA для SAP ASE можно найти в следующих примечаниях SAP:

* [1605680];
* [1757924];
* [1757928];
* [1758182];
* [1758496];	
* [1814258];
* [1922555];
* [1956005].

#### Рекомендации по резервному копированию и восстановлению для SAP ASE
При развертывании SAP ASE в Azure методику резервного копирования необходимо пересмотреть. Даже если система не используется для производственных задач, необходимо периодически выполнять резервное копирование базы данных SAP, размещенной в SAP ASE. Так как в службе хранилища Azure хранятся три образа, резервное копирование становится менее важным с точки зрения восстановления хранилища после сбоя. Основная причина соблюдения надлежащего плана резервного копирования и восстановления следующая. Благодаря возможности восстанавливать состояние на определенный момент времени вы можете исправлять логические и пользовательские ошибки. Следовательно, резервные копии используются для восстановления состояния базы данных на определенный момент времени или для создания другой системы в Azure путем копирования существующей базы данных. Например, с помощью восстановления из резервной копии вы можете перейти от двухуровневой конфигурации SAP к трехуровневой для той же системы.

Резервное копирование и восстановление базы данных в Azure работает так же, как и в локальных системах. См. следующие примечания SAP:

* [1588316];
* [1585981].

В них вы найдете дополнительные сведения о создании конфигураций дампов и планировании резервного копирования. В зависимости от стратегии и потребностей можно настроить запись дампов базы данных и журнала на диск на одном из существующих виртуальных жестких дисков (VHD) или добавить дополнительный VHD-диск для резервных копий. Чтобы снизить риск потери данных в случае возникновения ошибки, рекомендуется использовать виртуальный жесткий диск, на котором нет устройства базы данных.

Помимо сжатия данных и больших объектов система SAP ASE также предлагает сжатие резервных копий. Чтобы дампы базы данных и журнала занимали меньше места, рекомендуется использовать сжатие резервных копий. Дополнительные сведения см. в примечании SAP [1588316]. Сжатие резервных копий также позволяет существенно уменьшить объем данных при передаче. Это актуально, если вы планируете скачивать резервные копии или содержимое виртуальных дисков с дампами резервных копий с виртуальных машин Azure в локальную систему.

Не используйте диск D:\\ в качестве назначения дампа базы данных или журнала.

#### Рекомендации по ускорению резервного копирования и восстановления
Как и при развертывании на сервер без операционной системы, скорость резервного копирования и восстановления зависит от количества томов, которые могут считываться параллельно, а также от пропускной способности этих томов. Кроме того, загрузка ЦП при сжатии резервной копии может играть значительную роль на виртуальных машинах, имеющих не более восьми потоков ЦП. Исходя из сказанного выше, можно сделать следующие выводы.

* Чем меньше виртуальных жестких дисков используется для хранения устройств базы данных, тем меньше общая пропускная способность при чтении.
* Чем меньше потоков ЦП на виртуальной машине, тем сильнее влияние сжатия резервной копии.
* Чем меньше целевых объектов (чередующиеся каталоги, виртуальные жесткие диски), на которые записываются резервные копии, тем меньше пропускная способность.

Увеличить количество целевых объектов для записи можно двумя способами. Выбор того или иного варианта (и даже их сочетания) зависит от ваших потребностей.

* Распределить содержимое целевого тома, на котором хранятся резервные копии, между несколькими виртуальными дисками, чтобы увеличить на чередующемся томе количество операций ввода-вывода в секунду.
* Создать конфигурацию дампов на уровне SAP ASE, в которой дампы записываются в несколько целевых каталогов.

Сведения о распределении тома между несколькими виртуальными дисками см. выше в этом руководстве. Дополнительные сведения об использовании нескольких каталогов в конфигурации дампов в SAP ASE см. в документации по хранимой процедуре sp\_config\_dump, которая используется для создания конфигурации дампов, в [справочном центре Sybase](http://infocenter.sybase.com/help/index.jsp).

### Аварийное восстановление с помощью виртуальных машин Azure

#### Репликация данных с помощью сервера репликации SAP Sybase
Сервер репликации SAP Sybase (SRS) в SAP ASE — это "теплый" резервный сервер, используемый для асинхронной передачи транзакций базы данных в удаленное расположение.

Установка и эксплуатация сервера SRS на виртуальной машине, размещенной в службах виртуальных машин Azure, функционально ничем не отличается от аналогичных операций в локальной среде.

Поддержка функций HADR ASE через сервер репликации SAP запланирована в будущем выпуске. Это решение будет протестировано и выпущено для платформ Microsoft Azure, как только оно станет доступно.

## Особенности SAP ASE на платформе Linux

Начиная с Microsoft Azure, можно легко переносить существующие приложения SAP ASE на виртуальные машины Azure. SAP ASE на виртуальной машине позволяет снизить общую стоимость владения для развертывания и обслуживания приложений уровня организации, а также управления ими благодаря переносу этих приложений в Microsoft Azure. С помощью SAP ASE на виртуальной машине Azure администраторы и разработчики могут продолжать использовать те же средства разработки и администрирования, которые доступны в локальной среде.

Для развертывания виртуальных машин Azure важно знать официальные соглашения об уровне обслуживания, которые можно найти здесь: <https://azure.microsoft.com/support/legal/sla>

Сведения о размерах SAP и перечень сертифицированных SAP SKU виртуальных машин будут предоставлены в примечании SAP [1928533]. Дополнительные документы о размерах SAP для виртуальных машин Azure можно найти здесь <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> и здесь <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Инструкции и рекомендации по использованию службы хранилища Azure, развертыванию виртуальных машин SAP или мониторингу SAP применяются к развертываниям SAP ASE в сочетании с приложениями SAP, как указано в первых четырех главах этого документа.

Общие сведения об ASE на Linux и ASE в облаке содержатся в следующих двух примечаниях SAP:

* [2134316]
* [1941500]

### Поддержка версий SAP ASE 
SAP в настоящее время поддерживает версию SAP ASE 16.0 для использования с продуктами SAP Business Suite. Все обновления для сервера SAP ASE, а также драйверы JDBC и ODBC для продуктов SAP Business Suite доступны только в SAP Service Marketplace по адресу <https://support.sap.com/swdc>.

Для установки в локальной среде не следует загружать обновления для сервера SAP ASE или драйверы JDBC и ODBC непосредственно с веб-сайтов Sybase. Подробные сведения об исправлениях, которые поддерживаются для использования с продуктами SAP Business Suite в локальной среде и на виртуальных машинах Azure, см. в следующих примечаниях SAP:

* [1590719]
* [1973241]
 
Общие сведения о запуске SAP Business Suite в SAP ASE можно найти на сайте [SCN](https://scn.sap.com/community/ase)

### Рекомендации по конфигурации SAP ASE для связанных с SAP установок SAP ASE на виртуальных машинах Azure

#### Структура развертывания SAP ASE
В соответствии с общим описанием исполняемые файлы SAP ASE должны быть расположены или установлены в корневой файловой системе виртуальной машины ( /sybase ). Как правило, большинство баз данных системы и средств SAP ASE не особо активно используются рабочей нагрузкой SAP NetWeaver. Поэтому базы данных системы и средств (master, model, saptools, sybmgmtdb, sybsystemdb) могут также оставаться в корневой файловой системе.

Исключением может быть временная база данных, содержащая все рабочие таблицы и временные таблицы, созданные SAP ASE, что в случае некоторых рабочих нагрузок SAP ERP и всех рабочих нагрузок BW может потребовать большего объема данных или объема операций ввода-вывода, с которым не справится диск операционной системы исходной виртуальной машины.
 
В зависимости от версии SAPInst или SWPM, использованной для установки системы, база данных может содержать следующие компоненты.

* Отдельную базу tempdb SAP ASE, которая создается при установке SAP ASE.
* Базу tempdb SAP ASE, созданную при установке SAP ASE, и дополнительную базу saptempdb, созданную процедурой установки SAP.
* Базу tempdb SAP ASE, созданную при установке SAP ASE, и дополнительную базу tempdb, созданную вручную (например, согласно примечанию SAP [1752266]) для соответствия особым требованиям к tempdb ERP/BW.

При определенных рабочих нагрузках ERP или всех рабочих нагрузках BW имеет смысл по соображениям производительности поддерживать работу устройств tempdb дополнительно созданной базы данных tempdb (с помощью SWPM или вручную) в отдельной файловой системе, которая может быть представлена одним диском данных Azure или RAID-массивом Linux, охватывающим несколько дисков данных Azure. При отсутствии дополнительной базы данных tempdb рекомендуется ее создать (примечание SAP [1752266]).

Для таких систем необходимо выполнить следующие действия над дополнительно созданной базой данных tempdb.

* Переместить первый каталог базы данных tempdb в первую файловую систему базы данных SAP.
* Добавить каталоги tempdb на каждый виртуальный жесткий диск, содержащий файловую систему базы данных SAP.

Эта конфигурация позволяет tempdb потреблять больше пространства, чем может предоставить системный диск. Для справки можно проверить размеры каталогов tempdb в существующих системах, работающих локально. Или же такая конфигурация обеспечит такие показатели операций ввода-вывода в tempdb, которые не могут быть предоставлены системным диском. Опять же, системы, работающие в локальной среде, можно использовать для мониторинга рабочей нагрузки ввода-вывода на базу данных tempdb.

Никогда не помещайте каталоги SAP ASE в каталог /mnt или /mnt/resource виртуальной машины. Это правило также касается базы данных tempdb, даже если объекты, содержащиеся в tempdb, являются только временными, так как /mnt или /mnt/resource — это стандартное временное пространство виртуальной машины Azure, которое не сохраняется на постоянной основе. Дополнительные сведения о временном пространстве виртуальной машины Azure можно найти в [этой статье][virtual-machines-linux-how-to-attach-disk].

#### Влияние сжатия базы данных
В конфигурациях, в которых пропускная способность ввода-вывода может стать ограничивающим фактором, каждая мера, позволяющая сократить объем операций ввода-вывода, помогает растянуть рабочую нагрузку, которую можно запускать в сценарии IaaS, например Azure. Таким образом, настоятельно рекомендуется обязательно использовать сжатие SAP ASE перед отправкой существующей базы данных SAP в Azure.

Рекомендация выполнять сжатие перед отправкой в Azure, если оно еще не реализовано, дается по нескольким причинам.

* Объем отправляемых в Azure данных уменьшается.
* Длительность выполнения сжатия короче, при условии, что локально можно использовать более мощное оборудование с большим числом ЦП, более высокой пропускной способностью ввода-вывода или меньшими задержками ввода-вывода.
* Уменьшение размеров баз данных может сократить затраты на выделение места на дисках.

Сжатие данных и LOB работает на виртуальных машинах Azure так же, как и в локальной среде. Дополнительные сведения о том, как проверить, используется ли уже сжатие в существующей базе данных SAP ASE, см. в примечании SAP [1750510]. Также см. примечание SAP [2121797] для получения дополнительных сведений о сжатии баз данных.

#### Использование DBACockpit для мониторинга экземпляров баз данных
Для систем SAP, в которых используется SAP ASE в качестве платформы баз данных, средство DBACockpit доступно в виде встроенных окон браузера в DBACockpit для транзакции или в виде Webdynpro. Однако полный набор функций для мониторинга и администрирования базы данных доступен только в реализации Webdynpro средства DBACockpit.

Как и для локальных систем, требуется выполнить ряд действий, чтобы включить все функциональные возможности SAP NetWeaver, используемые в реализации Webdynpro средства DBACockpit. См. информацию в примечании SAP [1245200], чтобы включить использование средств Webdynpro и создать необходимые. При соблюдении инструкций в упомянутых выше примечаниях будет также выполнена настройка Internet Communication Manager (icm) вместе с портами, используемыми для подключений http и https. Настройка по умолчанию для http выглядит следующим образом:

> icm/server\_port\_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server\_port\_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600

Ссылки, создаваемые в DBACockpit для транзакции, будут выглядеть примерно так:

> https://`<полное\_имя\_узла`>:44300/sap/bc/webdynpro/sap/dba\_cockpit
> 
> http://`<полное\_имя\_узла`>:8000/sap/bc/webdynpro/sap/dba\_cockpit

В зависимости от того, подключена ли виртуальная машина Azure, на которой размещается система SAP, в режиме "сеть — сеть", в режиме многосетевого подключения или ExpressRoute (распределенное развертывание), а также от способа этого подключения, необходимо убедиться, что ICM использует полное имя узла, которое может быть разрешено на компьютере, с которого вы пытаетесь открыть DBACockpit. Чтобы узнать, как ICM определяет полное имя узла в зависимости от параметров профиля, а также явным образом задать параметр icm/host\_name\_full (если необходимо), см. примечание SAP [773830].

Если вы развернули виртуальную машину только в облачной среде (без подключения между локальной средой и Azure), вам необходимо определить общедоступный IP-адрес и метку домена. Тогда формат общедоступного DNS-имени виртуальной машины будет выглядеть так:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com

Дополнительную информацию о DNS-имени см. [здесь][virtual-machines-azurerm-versus-azuresm].

Если для параметра профиля SAP icm/host\_name\_full задать DNS-имя виртуальной машины Azure, ссылка может выглядеть так:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

В таком случае необходимо:

* добавить правила для входящего трафика в группу безопасности сети на портале Azure для портов TCP/IP, используемых для связи с ICM;
* добавить правила для входящего трафика в конфигурацию брандмауэра Windows для портов TCP/IP, используемых для связи с ICM.

Для автоматического импорта всех доступных исправлений рекомендуется периодически применять примечания SAP, содержащие наборы исправлений, которые относятся к вашей версии SAP:

* [1558958];
* [1619967];
* [1882376].

Дополнительные сведения о панели DBA для SAP ASE можно найти в следующих примечаниях SAP:

* [1605680];
* [1757924];
* [1757928];
* [1758182];
* [1758496];	
* [1814258];
* [1922555];
* [1956005].

#### Рекомендации по резервному копированию и восстановлению для SAP ASE
При развертывании SAP ASE в Azure методику резервного копирования необходимо пересмотреть. Даже если система не используется для производственных задач, необходимо периодически выполнять резервное копирование базы данных SAP, размещенной в SAP ASE. Так как в службе хранилища Azure хранятся три образа, резервное копирование становится менее важным с точки зрения восстановления хранилища после сбоя. Основная причина соблюдения надлежащего плана резервного копирования и восстановления следующая. Благодаря возможности восстанавливать состояние на определенный момент времени вы можете исправлять логические и пользовательские ошибки. Следовательно, резервные копии используются для восстановления состояния базы данных на определенный момент времени или для создания другой системы в Azure путем копирования существующей базы данных. Например, с помощью восстановления из резервной копии вы можете перейти от двухуровневой конфигурации SAP к трехуровневой для той же системы.

Резервное копирование и восстановление базы данных в Azure работает так же, как и в локальных системах. См. следующие примечания SAP:

* [1588316];
* [1585981].

В них вы найдете дополнительные сведения о создании конфигураций дампов и планировании резервного копирования. В зависимости от стратегии и потребностей можно настроить запись дампов базы данных и журнала на диск на одном из существующих виртуальных жестких дисков (VHD) или добавить дополнительный VHD-диск для резервных копий. Чтобы снизить риск потери данных в случае возникновения ошибки, рекомендуется использовать виртуальный жесткий диск, на котором нет каталога или файлов базы данных.

Помимо сжатия данных и больших объектов система SAP ASE также предлагает сжатие резервных копий. Чтобы дампы базы данных и журнала занимали меньше места, рекомендуется использовать сжатие резервных копий. Дополнительные сведения см. в примечании SAP [1588316]. Сжатие резервных копий также позволяет существенно уменьшить объем данных при передаче. Это актуально, если вы планируете скачивать резервные копии или содержимое виртуальных дисков с дампами резервных копий с виртуальных машин Azure в локальную систему.

Не сохраняйте дампы базы данных или журналов во временные пространства виртуальной машины Azure /mnt и /mnt/resource.

#### Рекомендации по ускорению резервного копирования и восстановления
Как и при развертывании на сервер без операционной системы, скорость резервного копирования и восстановления зависит от количества томов, которые могут считываться параллельно, а также от пропускной способности этих томов. Кроме того, загрузка ЦП при сжатии резервной копии может играть значительную роль на виртуальных машинах, имеющих не более восьми потоков ЦП. Исходя из сказанного выше, можно сделать следующие выводы.

* Чем меньше виртуальных жестких дисков используется для хранения устройств базы данных, тем меньше общая пропускная способность при чтении.
* Чем меньше потоков ЦП на виртуальной машине, тем сильнее влияние сжатия резервной копии.
* Чем меньше целевых объектов (программные RAID-массивы Linux, виртуальные жесткие диски), на которые записываются резервные копии, тем меньше пропускная способность.

Увеличить количество целевых объектов для записи можно двумя способами. Выбор того или иного варианта (и даже их сочетания) зависит от ваших потребностей.

* Распределить содержимое целевого тома, на котором хранятся резервные копии, между несколькими виртуальными дисками, чтобы увеличить на чередующемся томе количество операций ввода-вывода в секунду.
* Создать конфигурацию дампов на уровне SAP ASE, в которой дампы записываются в несколько целевых каталогов.

Сведения о распределении тома между несколькими виртуальными дисками см. выше в этом руководстве. Дополнительные сведения об использовании нескольких каталогов в конфигурации дампов в SAP ASE см. в документации по хранимой процедуре sp\_config\_dump, которая используется для создания конфигурации дампов, в [справочном центре Sybase](http://infocenter.sybase.com/help/index.jsp).

### Аварийное восстановление с помощью виртуальных машин Azure

#### Репликация данных с помощью сервера репликации SAP Sybase
Сервер репликации SAP Sybase (SRS) в SAP ASE — это "теплый" резервный сервер, используемый для асинхронной передачи транзакций базы данных в удаленное расположение.

Установка и эксплуатация сервера SRS на виртуальной машине, размещенной в службах виртуальных машин Azure, функционально ничем не отличается от аналогичных операций в локальной среде.

Сейчас работа ASE HADR через сервер репликации SAP не поддерживается. Это решение может быть протестировано и выпущено для платформ Microsoft Azure в будущем.

## Особенности базы данных Oracle в Windows
С середины 2013 г. программное обеспечение Oracle поддерживает работу с Microsoft Windows Hyper-V и Azure. Дополнительные сведения об общей поддержке Windows Hyper-V и Azure в продуктах Oracle см. здесь: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces>.

Кроме общей поддержки, также поддерживаются определенные сценарии приложений SAP, использующих базы данных Oracle. Подробности приведены в этой части документа.

### Поддерживаемые версии Oracle
Все сведения о версиях Oracle и соответствующих версиях ОС, которые поддерживаются для работы SAP в Oracle на виртуальных машинах Azure, см. в примечании SAP [2039619].

Общую информацию о работе SAP Business Suite в Oracle см. на сайте SCN: <https://scn.sap.com/community/oracle>.

### Рекомендации по конфигурации Oracle для установки SAP на виртуальные машины Azure

#### Конфигурация хранилища
Поддерживается только один экземпляр Oracle, использующий диски, отформатированные в NTFS. Все файлы базы данных должны храниться на виртуальных жестких дисках (VHD) с файловой системой NTFS. Эти VHD-диски должны быть подключены к виртуальной машине Azure и созданы на основе хранилища страничных BLOB-объектов Azure. Подробности см. здесь: <https://msdn.microsoft.com/library/azure/ee691964.aspx>. Любые сетевые диски и удаленные общие ресурсы, включая файловые службы Azure
 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>
 
**нельзя** использовать для хранения файлов базы данных Oracle.

Если используются виртуальные жесткие диски, созданные на основе хранилища страничных BLOB-объектов Azure, сведения, приведенные в этом документе в разделах [Кэширование для виртуальных машин и виртуальных жестких дисков][dbms-guide-2.1] и [Служба хранилища Microsoft Azure][dbms-guide-2.3], также относятся и к развертываниям с базой данных Oracle.

Как упомянуто в общей части этого документа, для виртуальных жестких дисков Azure существуют квоты на количество операций ввода-вывода в секунду. Квоты зависят от типа используемой виртуальной машины. Список типов виртуальных машин с соответствующими квотами см. [здесь][virtual-machines-size-specs].

Чтобы определить поддерживаемые типы виртуальных машин Azure, см. примечание SAP [1928533].

Если текущего количества операций ввода-вывода в секунду для каждого диска достаточно, все файлы базы данных можно хранить на одном подключенном виртуальном жестком диске Azure.

Если требуется большее количество операций ввода-вывода в секунду, настоятельно рекомендуем использовать пулы носителей Window (доступны только в Windows Server 2012 и более поздних версиях) или функцию чередования дисков (для Windows 2008 R2). Таким образом из нескольких подключенных виртуальных жестких дисков вы сможете создать одно большое логическое устройство. Также см. раздел [Программный RAID-массив][dbms-guide-2.2] в этом документе. Такой подход позволяет сократить административные расходы на управление дисковым пространством и избежать необходимости вручную распределять файлы между несколькими подключенными виртуальными жесткими дисками.

#### Резервное копирование и восстановление
Для резервного копирования и восстановления можно использовать средства SAP BR* для Oracle. Они поддерживаются так же, как и в стандартных операционных системах Windows Server и Hyper-V. Для резервного копирования на диски и восстановления с дисков также можно использовать диспетчер восстановления Oracle (RMAN).

#### Высокая доступность
[comment]: <> (link refers to ASM)
Для обеспечения высокой доступности и аварийного восстановления можно использовать Oracle Data Guard. Подробные сведения см. в [этой документации][virtual-machines-configuring-oracle-data-guard].

#### Другое
Все другие общие темы, такие как группы доступности Azure и мониторинг SAP, касаются также развертывания виртуальных машин с базой данных Oracle, как описано в первых трех разделах этого документа.

## Особенности базы данных SAP MaxDB в Windows

### Поддерживаемые версии SAP MaxDB
Сейчас с продуктами на основе SAP NetWeaver в Azure можно использовать SAP MaxDB версии 7.9. Все обновления для сервера SAP MaxDB и драйверов JDBC и ODBC для продуктов на основе SAP NetWeaver предоставляются исключительно через SAP Service Marketplace по адресу <https://support.sap.com/swdc>. Общие сведения о работе с SAP NetWeaver в SAP MaxDB см. здесь: <https://scn.sap.com/community/maxdb>.

### Поддерживаемые версии Microsoft Windows и типы виртуальных машин Azure для СУБД SAP MaxDB
Сведения о поддерживаемых версиях Microsoft Windows для СУБД SAP MaxDB в Azure, см. по следующим ссылкам:

* [SAP Product Availability Matrix (PAM)][sap-pam] \(Матрица доступности продуктов SAP);
* примечание SAP [1928533].

Мы настоятельно рекомендуем использовать последнюю версию операционной системы Microsoft Windows — Microsoft Windows 2012 R2.

### Доступная документация по SAP MaxDB
Обновленный список документации по SAP MaxDB см. в примечании SAP [767598].
	
### Рекомендации по конфигурации SAP MaxDB для установки SAP на виртуальные машины Azure

#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Конфигурация хранилища
Рекомендации для службы хранилища Azure и SAP MaxDB соответствуют общим рекомендациям, упомянутым в разделе [Структура развертывания реляционной СУБД][dbms-guide-2].

> [AZURE.IMPORTANT] Как и в других базах данных, в SAP MaxDB тоже есть файлы данных и файлы журналов, но в терминологии SAP MaxDB вместо понятия "файл" используется "том". Например, в SAP MaxDB существуют тома данных и тома журналов. Не путайте их с томами жестких дисков.

Вкратце повторим рекомендации.

* Настройте учетную запись хранения Azure, в которой будут храниться тома (т. е. файлы) данных и журналов SAP MaxDB, как **локально избыточное хранилище**, как описано в разделе [Служба хранилища Microsoft Azure][dbms-guide-2.3].
* Разделите пути ввода-вывода для томов (т. е. файлов) данных и томов журналов SAP MaxDB. Это означает, что тома (т. е. файлы) данных SAP MaxDB должны быть установлены на один логический диск, а тома (т. е. файлы) журналов SAP MaxDB — на другой логический диск.
* Правильно настройте кэширование файлов для каждого BLOB-объекта Azure. Настройки кэширования зависят от того, что будет храниться в BLOB-объекте (тома (т. е. файлы) данных или журналов SAP MaxDB), а также от класса службы хранилища Azure (Standard или Premium), как описано в разделе [Кэширование для виртуальных машин и виртуальных жестких дисков][dbms-guide-2.1].
* Если текущего количества операций ввода-вывода в секунду для каждого диска достаточно, все тома данных можно хранить на одном подключенном виртуальном жестком диске Azure, а все тома журналов базы данных — на другом подключенном виртуальном жестком диске Azure.
* Если требуется дополнительное пространство или большее количество операций ввода-вывода в секунду, настоятельно рекомендуем использовать пулы носителей Microsoft Window (доступны только в Microsoft Windows Server 2012 и более поздних версиях) или функцию чередования дисков (для Microsoft Windows 2008 R2). Таким образом из нескольких подключенных виртуальных жестких дисков вы сможете создать одно большое логическое устройство. Также см. раздел [Программный RAID-массив][dbms-guide-2.2] в этом документе. Такой подход позволяет сократить административные расходы на управление дисковым пространством и избежать необходимости вручную распределять файлы между несколькими подключенными виртуальными жесткими дисками.
* Если вам требуется максимальное количество операций ввода-вывода в секунду, используйте службу хранилища Azure класса Premium, доступную на виртуальных машинах серий DS и GS.

![Эталонная конфигурация виртуальной машины Azure IaaS для СУБД SAP MaxDB][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Резервное копирование и восстановление
При развертывании SAP MaxDB в Azure методику резервного копирования необходимо пересмотреть. Даже если система не используется для производственных задач, необходимо периодически производить резервное копирование базы данных SAP, размещенной в SAP MaxDB. Так как в службе хранилища Azure хранятся три образа, резервное копирование представляется менее важным в контексте защиты системы от сбоев хранилища и более важных эксплуатационных и административных сбоев. Основная причина соблюдения надлежащего плана резервного копирования и восстановления следующая. Благодаря возможности восстанавливать состояние на определенный момент времени вы можете исправлять логические или пользовательские ошибки. Следовательно, резервные копии используются для восстановления состояния базы данных на определенный момент времени или для создания другой системы в Azure путем копирования существующей базы данных. Например, с помощью восстановления из резервной копии вы можете перейти от двухуровневой конфигурации SAP к трехуровневой для той же системы.

Резервное копирование и восстановление базы данных в Azure работает так же, как и в локальных системах, поэтому вы можете использовать стандартные средства резервного копирования и восстановления SAP MaxDB, описанные в одном из перечисленных в примечании SAP [767598] документов по SAP MaxDB.

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Рекомендации по ускорению резервного копирования и восстановления
Как и при развертывании на сервер без операционной системы, скорость резервного копирования и восстановления зависит от количества томов, которые могут считываться параллельно, а также от пропускной способности этих томов. Кроме того, загрузка ЦП при сжатии резервной копии может играть значительную роль на виртуальных машинах, имеющих не более восьми потоков ЦП. Исходя из сказанного выше, можно сделать следующие выводы.

* Чем меньше виртуальных жестких дисков используется для хранения устройств базы данных, тем меньше общая пропускная способность при чтении.
* Чем меньше потоков ЦП на виртуальной машине, тем сильнее влияние сжатия резервной копии.
* Чем меньше целевых объектов (чередующиеся каталоги, виртуальные жесткие диски), на которые записываются резервные копии, тем меньше пропускная способность.

Увеличить количество целевых объектов для записи можно двумя способами. Выбор того или иного варианта (и даже их сочетания) зависит от ваших потребностей.

* Выделить для резервных копий отдельные тома.
* Распределить содержимое целевого тома, на котором хранятся резервные копии, между несколькими виртуальными дисками, чтобы увеличить на чередующемся дисковом томе количество операций ввода-вывода в секунду.
* Выделить отдельные логические диски для:
    * томов (т. е. файлов) резервных копий SAP MaxDB;
    * томов (т. е. файлов) данных SAP MaxDB;
    * томов (т. е. файлов) журналов SAP MaxDB.

Сведения о распределении тома между несколькими виртуальными дисками см. в разделе [Программный RAID-массив][dbms-guide-2.2] этого документа.

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Другое
Все другие общие темы, включая как группы доступности Azure и мониторинг SAP, касаются также развертывания виртуальных машин с базой данных SAP MaxDB, как описано в первых трех разделах этого документа. Другие параметры SAP MaxDB прозрачны для виртуальных машин Azure и описаны в различных документах, перечисленных в примечании SAP [767598]. Дополнительные сведения см. также в таких примечаниях SAP:

* [826037]; 
* [1139904];
* [1173395].

## Особенности SAP liveCache в Windows

### Поддерживаемые версии SAP liveCache
Минимальной поддерживаемой версией SAP liveCache в службе виртуальных машинах Azure является **SAP LC/LCAPPS 10.0 SP 25** (включая **liveCache 7.9.08.31** и **LCA-Build 25**), выпущенная для **EhP 2 для SAP SCM 7.0** и более новых версий.

### Поддерживаемые версии Microsoft Windows и типы виртуальных машин Azure для СУБД SAP liveCache
Сведения о поддерживаемых версиях Microsoft Windows для СУБД SAP liveCache в Azure, см. по следующим ссылкам:

* [SAP Product Availability Matrix (PAM)][sap-pam] (Матрица доступности продуктов SAP);
* примечание SAP [1928533].

Мы настоятельно рекомендуем использовать последнюю версию операционной системы Microsoft Windows — Microsoft Windows 2012 R2.

### Рекомендации по конфигурации SAP liveCache для установки SAP на виртуальные машины Azure

#### Рекомендуемые типы виртуальных машин Azure
Так как SAP liveCache является приложением, выполняющим огромный объем вычислений, основное влияние на его производительность оказывают скорость и объем оперативной памяти и ЦП.

Для типов виртуальных машин Azure, поддерживаемых в SAP (примечание SAP [1928533]), все ресурсы виртуального ЦП, выделенного для виртуальной машины, обеспечиваются ресурсами выделенного физического ЦП гипервизора. Из-за отсутствия избыточного выделения ресурсов отсутствует конкуренция за ресурсы ЦП.

Аналогично для всех типов виртуальных машин Azure, поддерживаемых в SAP, память виртуальной машины на 100 % соответствует физической памяти. Избыточное выделение (перерасход) не используется.

В этом контексте мы настоятельно рекомендуем использовать новый тип виртуальных машин Azure серии D или DS (в сочетании с хранилищем Azure класса Premium), так как они имеют на 60 % более быстрые процессоры, чем машины серии A. Если предполагается очень высокая загрузка ОЗУ и ЦП, можно использовать виртуальные машины серий G и GS с новейшими процессорами Intel® Xeon® семейства E5 v3 (в сочетании с хранилищем Azure класса Premium). Эти машины имеют в два раза больше памяти и в четыре раза более объемные твердотельные диски (SSD), чем машины серий D и DS.

#### Конфигурация хранилища
Так как в основе SAP liveCache лежат технологии SAP MaxDB, все рекомендации относительно службы хранилища Azure, данные для SAP MaxDB в разделе [Конфигурация хранилища][dbms-guide-8.4.1], также относятся к SAP liveCache.

#### Выделенная виртуальная машина Azure для liveCache
Так как SAP liveCache интенсивно использует вычислительные мощности, для достижения максимальной производительности мы настоятельно рекомендуем развертывать СУБД на выделенной виртуальной машине Azure.
 
![Виртуальная машина Azure, выделенная для liveCache, для производственных целей][dbms-guide-figure-700]

#### Резервное копирование и восстановление
Вопросы резервного копирования и восстановления, включая рекомендации по повышению производительности, уже раскрыты в соответствующих разделах, посвященных SAP MaxDB: [Резервное копирование и восстановление][dbms-guide-8.4.2] и [Рекомендации по повышению скорости резервного копирования и восстановления][dbms-guide-8.4.3].

#### Другое
Все другие общие темы уже описаны в [этом разделе][dbms-guide-8.4.4] о SAP MaxDB.

## Особенности сервера содержимого SAP в Windows
Сервер содержимого SAP (SAP Content Server) — это отдельный серверный компонент для хранения содержимого, например электронных документов в различных форматах. Сервер содержимого SAP предоставляется благодаря развитию технологий и используется для всех приложений SAP. Он устанавливается на отдельной системе. Стандартное содержимое — это учебные материалы и документация из базы знаний или технические чертежи из системы управления документами mySAP PLM.

### Поддерживаемые версии сервера содержимого SAP
В настоящее время SAP поддерживает:

* **сервер содержимого SAP** версии **6.50 (и более поздние)**;
* **SAP MaxDB версии 7.9**;
* **службы Microsoft IIS версии 8.0 (и более поздние версии)**.

Мы настоятельно рекомендуем использовать последнюю версию сервера содержимого SAP — **6.50 SP4** на момент написания этого документа, а также самую последнюю версию **служб Microsoft IIS — 8.5**.

Узнать последние версии сервера содержимого SAP и служб Microsoft IIS можно в [матрице доступности продуктов SAP][sap-pam].

### Поддерживаемые версии Microsoft Windows и типы виртуальных машин Azure для сервера содержимого SAP
Сведения о поддерживаемых версиях Microsoft Windows для сервера содержимого SAP в Azure см. по следующим ссылкам:

* [SAP Product Availability Matrix (PAM)][sap-pam] (Матрица доступности продуктов SAP);
* примечание SAP [1928533].

Мы настоятельно рекомендуем использовать последнюю версию операционной системы Microsoft Windows — **Microsoft Windows 2012 R2**.

### Рекомендации по конфигурации сервера содержимого SAP для установки SAP на виртуальные машины Azure

#### Конфигурация хранилища 
При настройке сервера содержимого SAP для хранения файлов в базе данных SAP MaxDB все рекомендации относительно хранилища Azure, данные для SAP MaxDB в разделе [Конфигурация хранилища][dbms-guide-8.4.1], также относятся к серверу содержимого SAP.

При настройке сервера контента SAP для хранения файлов в файловой системе мы рекомендуем использовать выделенный логический диск. Кроме того, использование дисковых пространств позволяет увеличить размер логического диска и количество операций ввода-вывода в секунду, как описано в разделе [Программный RAID-массив][dbms-guide-2.2].

#### Расположение сервера содержимого SAP
Сервер содержимого SAP должен быть развернут в том же регионе Azure и в той же виртуальной сети Azure, где развернута система SAP. Компоненты сервера содержимого SAP можно развернуть как на выделенной виртуальной машине Azure, так и на той, где работает система SAP.
 
![Виртуальная машина Azure, выделенная для сервера содержимого SAP][dbms-guide-figure-800]

#### Расположение сервера кэширования SAP
Сервер кэширования SAP — это дополнительный серверный компонент для предоставления локального доступа к (кэшированным) документам. Сервер кэширования SAP кэширует документы с сервера содержимого SAP. Это позволяет оптимизировать сетевой трафик, если обращения к документу происходят более одного раза из разных расположений. Общее правило состоит в том, что сервер кэширования SAP должен располагаться физически близко к клиенту, который к нему обращается.

В этом случае существует два варианта.

1. **Клиентом выступает внутренняя система SAP**. Если во внутренней системе SAP настроен доступ к серверу содержимого SAP, клиентом выступает система SAP. Так как система SAP и сервер содержимого SAP развернуты в одном и том же регионе Azure (в одном центре обработки данных Azure), они находятся физически близко друг к другу. В этом случае вам не нужен выделенный сервер кэширования SAP. Клиенты пользовательского интерфейса SAP (графического пользовательского интерфейса SAP или веб-браузера) обращаются к системе SAP напрямую, и система SAP извлекает документы с сервера содержимого SAP.
1. **Клиентом выступает локальный веб-браузер**. Сервер содержимого SAP можно настроить так, чтобы к нему можно было обращаться напрямую из веб-браузера. В таком случае клиентом сервера содержимого SAP выступает локальный веб-браузер. Локальный центр обработки данных и центр обработки данных Azure физически расположены в разных местах (в идеальном случае вблизи друг от друга). Ваш локальный ЦОД соединен с Azure через канал ExpressRoute или VPN-подключение типа "сайт — сайт". Хотя оба варианта предлагают безопасное VPN-подключение к Azure, подключение типа "сайт — сайт" не обеспечивает высокую пропускную способность и низкие задержки для соединения между локальным ЦОД и ЦОД Azure. Ускорить доступ к документам можно двумя способами:
    1. установить сервер кэширования SAP локально, недалеко от локального веб-браузера (см. [рисунок ниже][dbms-guide-900-sap-cache-server-on-premises]);
    1. настроить канал Azure ExpressRoute, который обеспечивает выделенное высокоскоростное сетевое подключение с низкой задержкой между локальным ЦОД и ЦОД Azure.
 
![Вариант локальной установки сервера кэширования SAP][dbms-guide-figure-900] <a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### Резервное копирование и восстановление
Если вы настраиваете сервер содержимого SAP для хранения файлов в базе данных SAP MaxDB, процедуры резервного копирования и восстановления и вопросы производительности уже описаны в соответствующих разделах, посвященных SAP MaxDB: [Резервное копирование и восстановление][dbms-guide-8.4.2] и [Рекомендации по повышению скорости резервного копирования и восстановления][dbms-guide-8.4.3].

Если вы настраиваете сервер содержимого SAP для хранения файлов в файловой системе, резервное копировании и восстановление выполняется вручную для всей файловой структуры, в которой хранятся документы. Как и в случае с резервным копированием и восстановлением SAP MaxDB, для целей резервного копирования мы рекомендуем выделить отдельный том диска.

#### Другое
Другие параметры сервера содержимого SAP прозрачны для виртуальных машин Azure и описаны в различных документах и примечаниях SAP:

* <https://service.sap.com/contentserver>; 
* примечание SAP [1619726].  

## Особенности IBM DB2 для LUW в Windows
Microsoft Azure позволяет легко перенести существующие приложения SAP, работающие в IBM DB2 для Linux, UNIX и Windows (LUW), на виртуальные машины Azure. Для решений SAP на базе IBM DB2 для LUW администраторы и разработчики могут по-прежнему использовать те же средства разработки и администрирования, которые доступны локально. Общую информацию о работе SAP Business Suite в IBM DB2 для LUW см. на сайте сообщества SAP (SCN): <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Дополнительные сведения и новости о SAP в DB2 для LUW в Azure см. в примечании SAP [2233094].

### Поддерживаемые версии IBM DB2 для Linux, UNIX и Windows
SAP в IBM DB2 для LUW поддерживается в службах виртуальных машин Microsoft Azure начиная с версии DB2 10.5.

Дополнительные сведения о поддерживаемых продуктах SAP и типах виртуальных машин Azure см. в примечании SAP [1928533].

### Рекомендации по конфигурации IBM DB2 для Linux, UNIX и Windows для установки SAP на виртуальные машины Azure

#### Конфигурация хранилища
Все файлы базы данных должны храниться на виртуальных жестких дисках (VHD) с файловой системой NTFS. Эти VHD-диски должны быть подключены к виртуальной машине Azure и созданы на основе хранилища страничных BLOB-объектов Azure. Подробности см. здесь: <https://msdn.microsoft.com/library/azure/ee691964.aspx>. Сетевые диски всех типов и удаленные общие ресурсы, включая следующие файловые службы Azure, **нельзя** использовать для хранения файлов базы данных.

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>
 
Если вы используете виртуальные жесткие диски, созданные на основе хранилища страничных BLOB-объектов Azure, сведения, приведенные в этом документе в разделе [Структура развертывания реляционной СУБД][dbms-guide-2], также относятся к развертываниям с базой данных IBM DB2 для LUW.

Как упомянуто в общей части этого документа, для виртуальных жестких дисков Azure существуют квоты на количество операций ввода-вывода в секунду. Квоты зависят от типа используемой виртуальной машины. Список типов виртуальных машин с соответствующими квотами см. [здесь][virtual-machines-size-specs].

Если текущего количества операций ввода-вывода в секунду для каждого диска достаточно, все файлы базы данных можно хранить на одном подключенном виртуальном жестком диске Azure.

Рекомендации по повышению производительности можно также найти в руководствах по установке SAP в разделе о рекомендациях по безопасности данных и повышению производительности в каталогах баз данных.

Кроме того, с помощью пулов носителей Windows (только в Windows Server 2012 и более поздних версиях) или функции чередования дисков Windows (в Windows 2008 R2) из нескольких подключенных виртуальных дисков можно создать одно большое логическое устройство. Сведения об этом см. в разделе [Программный RAID-массив][dbms-guide-2.2] в этом документе. Для дисков, на которых хранятся данные (sapdata) и временные каталоги (saptmp) SAP DB2, в качестве размера сектора физического диска необходимо указать 512 КБ. При использовании пулов носителей Windows эти пулы необходимо создать вручную с помощью интерфейса командной строки, используя параметр -LogicalSectorSizeDefault. Дополнительные сведения см. здесь: <https://technet.microsoft.com/library/hh848689.aspx>.

#### Резервное копирование и восстановление
Для резервного копирования и восстановления можно использовать средства IBM DB2 для LUW. Они поддерживаются так же, как в стандартных операционных системах Windows Server и Hyper-V.

У вас обязательно должна быть надежная стратегия резервного копирования базы данных.

Как и при развертывании на сервер без операционной системы, скорость резервного копирования и восстановления зависит от количества томов, которые могут считываться параллельно, а также от пропускной способности этих томов. Кроме того, загрузка ЦП при сжатии резервной копии может играть значительную роль на виртуальных машинах, имеющих не более восьми потоков ЦП. Исходя из сказанного выше, можно сделать следующие выводы.

* Чем меньше виртуальных жестких дисков используется для хранения устройств базы данных, тем меньше общая пропускная способность при чтении.
* Чем меньше потоков ЦП на виртуальной машине, тем сильнее влияние сжатия резервной копии.
* Чем меньше целевых объектов (чередующиеся каталоги, виртуальные жесткие диски), на которые записываются резервные копии, тем меньше пропускная способность.

Увеличить количество целевых объектов для записи можно двумя способами. Выбор того или иного варианта (и даже их сочетания) зависит от ваших потребностей.

* Распределить содержимое целевого тома, на котором хранятся резервные копии, между несколькими виртуальными дисками, чтобы увеличить на чередующемся томе количество операций ввода-вывода в секунду.
* Использовать несколько целевых каталогов, в которые будут записываться резервные копии.

#### Высокая доступность и аварийное восстановление
Microsoft Cluster Server (MSCS) не поддерживается.

Поддерживается аварийное восстановление высокой доступности (HADR) DB2. Если в конфигурации высокой доступности виртуальные машины имеют работающее разрешение имен, настройка в Azure не будет отличаться от настройки, которая выполняется локально. Мы не рекомендуем полагаться только на разрешение IP-адресов.

Не используйте георепликацию хранилища Azure. Дополнительные сведения см. в разделах [Служба хранилища Microsoft Azure][dbms-guide-2.3] и [Высокая доступность и аварийное восстановление с использованием виртуальных машин Azure][dbms-guide-3].

#### Другое
Все другие общие темы, включая группы доступности Azure и мониторинг SAP, касаются также развертывания виртуальных машин с IBM DB2 для LUW, как описано в первых трех разделах этого документа.

Также см. раздел [Общие сведения об SQL Server для SAP в Azure][dbms-guide-5.8].

<!---HONumber=AcomDC_0518_2016-->
