---
title: "SAP HANA операций в Azure | Документы Microsoft"
description: "Руководство по работе с для систем SAP HANA, развернутых на виртуальных машинах Azure."
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Руководство по использованию SAP HANA в Azure
Этот документ содержит инструкции для операционных систем SAP HANA, развернутых в Azure собственный виртуальных машин (ВМ). В этом документе не предназначен для замены Стандартная документации SAP, включая следующее содержимое:

- [Руководство по администрированию SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Руководства по установке SAP](https://service.sap.com/instguides)
- [Примечания по SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Технические условия
Для использования в этом руководстве, вы должны базовых знаний о следующие компоненты Azure:

- [Виртуальные машины Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Сети Azure и виртуальные сети](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Хранилище Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Дополнительные сведения о SAP NetWeaver и другими компонентами SAP в Azure см. в разделе [SAP в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) раздел [документации Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Рекомендации по основной настройке
В следующих разделах описаны основные настройки вопросы развертывания систем SAP HANA на виртуальных машинах Azure.

### <a name="connect-into-azure-virtual-machines"></a>Подключения в Azure виртуальные машины
Как описано в документе [виртуальных машин Azure руководства по планированию](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), существует два основных метода для подключения в виртуальных машинах Azure:

- Подключение через Интернет и общедоступными конечными точками на виртуальной Машине, перехода или на виртуальной Машине, на котором выполняется SAP HANA.
- Подключение через [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) или Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Сайт сайт подключения через VPN или ExpressRoute необходим для рабочих сценариев. Этот тип подключения также требуется для экспериментальной сценариев, которые загружаются в рабочих сценариях, где используется программное обеспечение SAP. На рисунке показан пример подключения между сайтами.

![Межсайтовые подключения](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Выберите типы ВМ Azure
В типах ВМ Azure, которые могут использоваться для рабочих сценариев, перечислены в [документацию SAP по IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Для сценариев нерабочей доступен широкий ряд собственных типов ВМ Azure.

>[!NOTE]
>В непроизводственной сценариев используйте типов ВМ, которые перечислены в [Примечание SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Развертывание виртуальных машин в Azure с помощью:

- Портал Azure.
- Командлеты Azure PowerShell.
- Azure CLI.

Также можно развернуть полный установленной платформе SAP HANA в службах виртуальных Машин Azure через [SAP облачная платформа](https://cal.sap.com/). Процесс установки описан в [развертывания SAP S/4HANA или BW/4HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choose-azure-storage-type"></a>Выберите тип хранилища Azure
Azure предоставляет два типа хранилища, которые подходят для виртуальных машин Azure, работающих под управлением SAP HANA:

- [Хранилище Azure уровня "Стандартный"](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Хранилище Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure предлагает два метода развертывания виртуальных жестких дисков (VHD) в хранилище Azure класса "Стандартный" и хранилище Azure класса Premium. Если для этого общего сценария, воспользоваться преимуществами [Azure управляемого диска](https://azure.microsoft.com/services/managed-disks/) развертываний.

Список типов хранилищ и их соглашения об уровне обслуживания, просмотрите [документации по Azure для дисков, управляемых](https://azure.microsoft.com/pricing/details/managed-disks/).

Диски Azure Premium рекомендуется для /hana/data и /hana/log тома. Можно построить LVM RAID на нескольких дисках хранилища Premium и использовать тома RAID как /hana/data и /hana/log тома.

Ниже показана конфигурация типов ВМ, часто используемые клиентами для узла SAP HANA на виртуальных машинах Azure:

| SKU виртуальной машины | ОЗУ | Макс. ВИРТУАЛЬНАЯ МАШИНА ВВОДА-ВЫВОДА<br /> Throughput | /hana/data и /hana/log,<br /> чередующиеся с диспетчером логических томов или MDADM | /hana/shared | том /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 ГБ | 384 МБ | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 Гиб | 768 МБ | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 Гиб | 1200 ГБ | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 Гиб | 2000 ГБ | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 Гиб | 1000 ГБ | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 Гиб | 1000 ГБ | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | Гиб 2000 | 2000 ГБ |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 Гиб | 2000 ГБ | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> Диски, рекомендуется для меньшего размера виртуальной Машины типов с 3 x P20 oversize тома относительно согласно рекомендации по месту [Технический документ хранилища TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Тем не менее для обеспечения достаточно пропускной способности диска и SAP HANA завершилось выбор, как показано в таблице. Если вам требуется меньше пропускной способности ввода-вывода, можно изменить выбор дисках хранилища Premium для /hana/data и /hana/log. То же верно для изменения размера тома /hana/backup, был размера для хранения резервных копий, которые представляют дважды томе памяти. Если вам требуется меньше места, затем можно настроить. Также учитывайте общую пропускную способность ввода-вывода виртуальной Машины после изменения размера или выбирают для виртуальной Машины. В целом пропускная способность виртуальных Машин описан в статье [размеры виртуальных машин, оптимизированные для памяти](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)  

> [!NOTE]
> Если вы хотите использовать преимущества [виртуальной машины Azure единый соглашения об уровне ОБСЛУЖИВАНИЯ виртуальных Машин](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) необходимо изменить все виртуальные жесткие диски, которые перечислены как стандартное хранилище (Sxx) в хранилище класса Premium (Pxx). 


### <a name="set-up-azure-virtual-networks"></a>Настройка виртуальных сетей Azure
При наличии подключения сеть сеть в Azure через VPN или подключение ExpressRoute, необходимо иметь по крайней мере один [виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , подключенное через виртуальный шлюз VPN или ExpressRoute цепи. Виртуальный шлюз находится в подсети в виртуальной сети Azure. Чтобы установить SAP HANA, необходимо создать два дополнительных подсетей в виртуальной сети. В одной подсети размещаются виртуальные машины для запуска экземпляров SAP HANA. Другие подсети выполняет Jumpbox или управления виртуальными машинами для размещения SAP HANA Studio или другого программного обеспечения управления.

Виртуальные машины, необходимые при установке виртуальных машин для запуска SAP HANA.

- Установлены два виртуальных сетевых адаптеров: один сетевой Адаптер для подключения к подсети управления и один сетевой Адаптер для подключения по локальной сети или других сетей экземпляра SAP HANA в виртуальной Машине Azure.
- Статические частного IP-адреса, развернутых для обеих виртуальных сетевых адаптеров.

Обзор различных методов для назначения IP-адресов см. в разделе [IP-адресов, типы и методы распределения в Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

[Azure группы безопасности сети (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) используются для направления трафика, направляемого экземпляра SAP HANA или Jumpbox. Nsg связаны SAP HANA подсеть и подсеть управления.

Ниже приведен обзор развертывания грубой схемы для SAP HANA:

![Приблизительная схема развертывания SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Чтобы развернуть SAP HANA в Azure без подключения сеть сеть, доступ к экземпляру SAP HANA на то, что общедоступный IP-адрес. IP-адреса должны назначаться виртуальной Машине Azure, на котором работает ВМ Jumpbox. В этом базовом сценарии развертывания зависит от Azure встроенные службы DNS для разрешения имен узлов. В более сложной конфигурации, где используются общедоступный IP-адресов особенно важны Azure встроенные службы DNS. Используйте Nsg Azure для ограничения открытые порты или диапазоны IP-адресов, которые могут подключаться на Azure подсети с ресурсами, общедоступный IP-адреса. На следующем рисунке показана грубой схемы для развертывания SAP HANA без подключения сеть сеть:
  
![Прикидка схема развертывания для SAP HANA без подключения сеть сеть](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Операции развертывания SAP HANA на виртуальных машинах Azure
В следующих разделах описаны некоторые операции, связанные с развертыванием SAP HANA систем на виртуальных машинах Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Резервное копирование и восстановление операций на виртуальных машинах Azure
Следующие документы описывают резервное копирование и восстановление развертывания SAP HANA.

- [Обзор резервного копирования SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Резервное копирование на уровне файла SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA хранилища моментальных снимков тестирования](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Запуск и перезапуск ВМ, которые содержат SAP HANA
Характерные черты общедоступное облако Azure — что вы оплачиваете только вашей вычислительной минут. Например при завершении работы виртуальной Машины, на котором выполняется SAP HANA вас выполняется плата только за затрат на хранение в течение этого времени. Другой компонент, доступен при указании статических IP-адресов для виртуальных машин во время первоначального развертывания. При перезапуске виртуальной Машины с SAP HANA, перезапуска виртуальной Машины с предыдущего IP-адресов. 


### <a name="use-saprouter-for-sap-remote-support"></a>Используйте SAProuter для удаленной поддержки SAP
Если имеется сайт сайт подключение между расположения в локальной среде и Azure, а вы используете компоненты SAP, затем SAProuter, вероятно, уже выполняется. В этом случае выполните следующие элементы для поддержки удаленного управления.

- Ведение частных и статический IP-адрес виртуальной машины, на котором размещена SAP HANA в SAProuter конфигурации.
- Настройте NSG из подсети, в которой размещена на виртуальной Машине HANA, чтобы разрешить трафик через порт TCP/IP 3299.

Если вы подключаетесь к Azure через Интернет, и нет маршрутизатор SAP для ВМ с SAP HANA, необходимо установить компонент. Установите SAProuter в отдельной виртуальной Машины в подсеть управления. На следующем рисунке показана грубой схемы для развертывания SAP HANA, без подключения сеть сеть и с SAProuter:

![Прикидка схема развертывания для SAP HANA без подключения сеть сеть и SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Не забудьте установить SAProuter на отдельной виртуальной машине, а не в Jumpbox ВМ. Отдельный виртуальный компьютер должен иметь статический IP-адрес. Чтобы подключиться к SAProuter SAProuter, которая размещена в SAP, обратитесь к SAP для IP-адреса. (SAProuter, которая размещена в SAP является аналогом экземпляра SAProuter, установите на виртуальной Машине). Используйте IP-адрес из SAP к настройке экземпляра SAProuter. В параметрах конфигурации только необходимые порт используется TCP-порт 3299.

Дополнительные сведения о том, как установить и настроить удаленную поддержку подключений через SAProuter см. в разделе [документацию SAP по](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Высокого уровня доступности с SAP HANA на собственном виртуальных машинах Azure
Если вы используете SUSE Linux 12 SP1 или более поздней версии, можно установить с устройствами STONITH Pacemaker кластера. Устройства можно использовать для настройки конфигурации SAP HANA, использующий Синхронная репликация с HANA системы репликации и автоматический переход на другой. Дополнительные сведения о процедуре установки см. в разделе [высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).
