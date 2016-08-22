.<properties
 pageTitle="Размеры виртуальных машин Linux: A8, A9, A10, A11 | Microsoft Azure"
 description="Ознакомьтесь с обзорной информацией и рекомендациями по использованию экземпляров A8, A9, A10 и A11 Azure для ресурсоемких вычислений для виртуальных машин Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# Сведения об экземплярах A8, A9, A10 и A11 с большим объемом вычислений 

Здесь приводятся общие сведения и некоторые рекомендации по использованию экземпляров Azure A8, A9, A10 и A11 (*ресурсоемких* экземпляров). Эта статья посвящена использованию этих экземпляров для виртуальных машин Linux. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Доступ к сети RDMA

В рамках одной облачной службы или группы доступности обращаться к сети RDMA в Azure для запуска приложений Linux MPI могут кластеры виртуальных машин размером A8 и A9, на которых работают один из следующих дистрибутивов Linux HPC и поддерживаемая реализация MPI. Сведения о развертывании и настройке см. в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md).

* **Дистрибутивы**: SUSE Linux Enterprise Server (SLES) 12 для HPC, SLES 12 для HPC (Premium), CentOS 7.1 или 6.5 HPC, развернутые из образов Azure Marketplace

* **MPI**: Intel MPI Library 5.x

    >[AZURE.NOTE] В образах CentOS HPC, доступных в Marketplace, уже установлена библиотека Intel MPI 5.1. Чтобы использовать Intel MPI на виртуальных машинах SLES 12 HPC, ее следует установить дополнительно.

В настоящее время драйверы Azure Linux RDMA устанавливаются только при развертывании из Azure Marketplace образов SLES 12 HPC с поддержкой RDMA или CentOS HPC. Вы не сможете установить эти драйверы на других версиях виртуальных машин Linux.

>[AZURE.NOTE]В образах CentOS HPC, доступных в Marketplace, обновления ядра отключены в файле конфигурации **yum**. Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.


## Обновления драйверов RDMA для SLES 12
После создания виртуальной машины на основе образа SLES 12 HPC следует обновить драйверы RDMA на виртуальных машинах для подключения к сети RDMA.

>[AZURE.IMPORTANT]Это **обязательный** шаг для развертывания виртуальных машин SLES 12 HPC во всех регионах Azure. Без него можно обойтись, если вы развернули виртуальную машину HPC 7.1 или HPC 6.5 на основе CentOS.

Прежде чем обновлять драйверы, остановите все процессы **zypper** или другие процессы, блокирующие базы данных репозиториев SUSE на виртуальной машине. В противном случае драйверы могут не обновиться должным образом.

Чтобы обновить драйверы Linux RDMA на каждой виртуальной машине, выполните на клиентском компьютере один из следующих наборов команд Azure CLI.

**Для виртуальной машины SLES 12 HPC, подготовленной в классической модели развертывания**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Для виртуальной машины SLES 12 HPC, подготовленной в модели развертывания Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Установка драйверов может занять определенное время. Команда завершится без выходных данных. После обновления виртуальная машина перезапустится и через несколько минут будет готова к работе.

### Пример сценария для обновления драйверов

Если вы используете кластер виртуальных машин SLES 12 HPC, обновление драйверов на всех узлах можно внести в сценарий. Например, следующий сценарий обновляет драйверы на 8 узлах кластера.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Рекомендации по использованию пакета HPC и Linux

[Пакет HPC](https://technet.microsoft.com/library/jj899572.aspx) — это бесплатное решение для управления кластерами HPC и заданиями, предоставленное корпорацией Майкрософт. Последние выпуски пакета HPC 2012 R2 поддерживают несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure, под управлением головного узла Windows Server. Вычислительные узлы Linux, развернутые на виртуальных машинах A8 или A9, на которых запущена поддерживаемая реализация MPI, могут выполнять приложения MPI, которые обращаются к сети RDMA. Чтобы приступить к работе, см. статью [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Аспекты топологии сети

* На виртуальных машинах Linux размеров A8 и A9 Eth1 зарезервировано для сетевого трафика RDMA. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети. Eth0 зарезервирован для обычного сетевого трафика Azure.

* IP через Infiniband (IB) не поддерживается в Azure. Поддерживается только RDMA через IB.


## Дальнейшие действия

* Дополнительные сведения о доступности и ценах на экземпляры A8, A9, A10 и A11 см. в разделе [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md).

* Чтобы приступить к развертыванию и использованию экземпляров A8 и A9 с RDMA в Linux, изучите статью [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0810_2016-->