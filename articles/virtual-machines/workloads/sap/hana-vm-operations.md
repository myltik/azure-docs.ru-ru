---
title: "Операции SAP HANA в Azure | Документация Майкрософт"
description: "Операции SAP HANA на собственных виртуальных машинах Azure"
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
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Руководство по использованию SAP HANA в Azure
Данное руководство содержит рекомендации по работе с системами SAP HANA, которые были развернуты на виртуальных машинах Azure. Этот документ не предназначен для замены какой-либо стандартной документации SAP. Документацию можно найти в следующих расположениях:

- [SAP HANA Administration Guide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html) (Руководство по администрированию SAP HANA)
- [SAP Installation Guides](https://service.sap.com/instguides) (Руководство по установке SAP)
- [SAP Note](https://sservice.sap.com/notes) (Примечание к SAP)

Необходимым условием является наличие базовых знаний о различных компонентах Azure:

- [Виртуальные машины Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Сети и виртуальные машины Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network).
- [Хранилище Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Дополнительную документацию по SAP NetWeaver и другим компонентам SAP на Azure можно найти в статье [Размещение и выполнение сценариев рабочей нагрузки SAP с помощью Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) и [документации по Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Рекомендации по основной настройке
### <a name="connecting-into-azure"></a>Подключение к Azure
Как указано в статье [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide], существует два основных способа подключения к виртуальным машинам Azure. 

- Подключение через Интернет и общедоступные конечные точки на виртуальной машине перехода или виртуальной машине, работающей под управлением SAP HANA
- Подключение через [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) или Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

В рабочих сценариях или сценариях, где нерабочие сценарии, связанные с производственными сценариями, в сочетании с программным обеспечением SAP, вам необходимо иметь возможность подключения типа "сеть — сеть" через VPN или ExpressRoute, как показано на этом графике:

![Межсайтовые подключения](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Разные типы виртуальных машин Azure
Типы виртуальных машин Azure, которые можно использовать для рабочих сценариев, можно найти [здесь](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Для нерабочих сценариев можно выбрать более широкий спектр собственных виртуальных машин Azure. Тем не менее необходимо ограничиться типами виртуальных машин, которые указаны в [примечании к SAP № 1928533](https://launchpad.support.sap.com/#/notes/1928533). Развертывание этих виртуальных машин в Azure можно выполнить с помощью:

- Портал Azure
- Командлетов Azure PowerShell
- Инфраструктура CLI Azure

Вы также можете развернуть полностью установленную платформу SAP HANA в службы виртуальных машин Azure через [облачную платформу SAP](https://cal.sap.com/), описанную [здесь](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Выбор хранилища Azure
Azure предоставляет два основных типа хранилища, подходящих для виртуальных машин Azure, работающих под управлением SAP HANA

- [Хранилище Azure уровня "Стандартный"](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Хранилище Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure предлагает два метода развертывания виртуальных жестких дисков (VHD) в хранилище Azure класса "Стандартный" и хранилище Azure класса Premium. Рекомендуется использовать [развертывание управляемых дисков Azure](https://azure.microsoft.com/services/managed-disks/), если общий сценарий это допускает.

Для точных типов хранилищ и SLA для этих типов хранения см. [эту документацию](https://azure.microsoft.com/pricing/details/managed-disks/).

Рекомендуется использовать диски Azure уровня "Премиум" для томов /hana/data и /hana/log. Поддерживается создание LVM RAID на нескольких дисках хранилища класса Premium и использование этих томов RAID в качестве томов /hana/data и /hana/log.

Возможная конфигурация для разных общих типов виртуальных машин, которые клиенты используют для размещения SAP HANA на виртуальных машинах Azure, может выглядеть так:

| SKU виртуальной машины | ОЗУ | /hana/data и /hana/log,<br /> чередующиеся с диспетчером логических томов или MDADM | /hana/shared | том /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 ГБ | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256 ГБ | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 ГБ | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 ГБ | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 ТБ | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,7 ТБ | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 ТБ | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3,8 ТБ | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Сети Azure
Предположим, что у вас есть VPN или ExpressRoute подключение "сеть — сеть" в Azure, тогда у вас будет минимум одна [виртуальная сеть Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), которая подключается через виртуальный шлюз к сети VPN или ExpressRoute. Виртуальный шлюз находится в подсети виртуальной сети Azure. Чтобы установить HANA, необходимо создать две другие подсети в виртуальной сети. Одна подсеть, в которой размещаются виртуальные машины, на которых будут работать экземпляры SAP HANA, и другая подсеть, в которой будут работать Jumpbox или виртуальные машины управления, на которые могут размещаться SAP HANA Studio или другое программное обеспечение для управления.
При установке виртуальных машин, на которые необходимо установить HANA, они должны иметь следующее:

- Два виртуальных сетевых адаптера, один из которых подключается к подсети управления, и один сетевой адаптер, который используется для подключения из локальной сети или других сетей к экземпляру SAP HANA на виртуальной машине Azure.
- Статические частные IP-адреса, развернутые для обеих виртуальных сетевых карт.

Обзор различных возможностей назначения IP-адреса можно найти [здесь](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Маршрутизация трафика либо непосредственно в экземпляр SAP HANA, либо в поле перехода осуществляется с помощью [групп безопасности сети Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg), которые связаны с подсетью HANA и подсетью управления.

В целом схема приблизительного развертывания выглядит следующим образом:

![Приблизительная схема развертывания SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Если вы просто развертываете SAP HANA в Azure без использования сайта для подключения "сеть — сеть" (VPN или ExpressRoute к Azure), вы получите доступ к экземпляру SAP HANA, но только к публичному IP-адресу, который назначен виртуальной машине Azure, на которой работает виртуальная машина Jumpbox. В обычном случае также можно использовать встроенные DNS-службы Azure для разрешения имен узлов. В частности, при использовании общедоступных IP-адресов используйте группы безопасности сети Azure для ограничения открытых портов или диапазонов IP-адресов, которым разрешено подключаться к подсетям Azure, работающим с ресурсами с IP-адресами для общего доступа. Схема такого развертывания может выглядеть так:
  
![Примерная схема развертывания SAP HANA без подключения "сеть — сеть".](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Операции
### <a name="backup-and-restore-operations-on-azure-vms"></a>Операции архивации и восстановления на виртуальных машинах Azure
Возможности архивации и восстановления SAP HANA Backup описаны в следующих документах:

- [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Резервное копирование SAP HANA на уровне файлов](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Резервное копирование SAP HANA на основе моментальных снимков хранилища](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Запуск и перезапуск виртуальных машин SAP HANA
Одним из преимуществ общедоступного облака Azure является тот факт, что вы платите только за минуты вычисления. Это означает, что при завершении работы виртуальной машины с SAP HANA за это время будут выставлены только расходы на хранение. Когда вы снова запустите виртуальную машину с SAP HANA, она будет иметь прежние IP-адреса (если вы развернули статические IP-адреса). 


### <a name="saprouter-enabling-sap-remote-support"></a>Включение удаленной поддержки SAP с помощью SAPRouter
При наличии подключения "сеть — сеть" между локальными расположениями и Azure и запуске компонентов SAP весьма вероятно, что вы уже запустили SAProuter. В этом случае вам ничего не нужно делать с экземплярами SAP HANA, которые вы развертываете в Azure. За исключением сохранения частного и статического IP-адреса виртуальной машины, на которой размещается HANA в конфигурации SAPRouter, и адаптации подсети NSG, в которой размещена виртуальная машина HANA (разрешен трафик через TCP/IP порт 3299).

Если вы развертываете SAP HANA и подключаетесь к Azure через Интернет, и у вас нет установленного в виртуальной сети маршрутизатора SAP, который запускает виртуальную машину с помощью SAP HANA, вы должны установить SAPRouter на отдельной виртуальной машине в подсети управления, как показано тут:


![Примерная схема развертывания SAP HANA без подключения "сеть — сеть" и SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

SAPRouter следует установить на отдельной виртуальной машине, а не на виртуальной машине Jumpbox. Отдельной виртуальной машине требуется статический IP-адрес. Для подключения SAPRouter к SAPRouter, который размещен в SAP (аналог экземпляра SAPRouter, установленного на виртуальной машины), необходимо обратиться к SAP, чтобы получить IP-адрес SAP, с использованием которого необходимо настроить экземпляр SAPRouter. Требуется только TCP порт 3299.
Дополнительные сведения о том, как настроить и поддерживать подключения удаленной поддержки через SAPRouter, см. [здесь](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Высокий уровень доступности SAP HANA на собственных виртуальных машинах Azure
При использовании SUSE Linux 12 SP1 и более поздней версии вы можете установить кластер Pacemaker с устройствами STONITH для настройки конфигурации SAP HANA, которая использует синхронную репликацию с репликацией системы HANA и автоматическим переходом на другой ресурс. Процедура установки описана в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










