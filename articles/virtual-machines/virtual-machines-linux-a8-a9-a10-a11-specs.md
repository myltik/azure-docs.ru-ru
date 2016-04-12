<properties
 pageTitle="Сведения об экземплярах A8–A11 и Linux | Microsoft Azure"
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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Об использовании экземпляров A8, A9, A10 и A11 для ресурсоемких вычислений в Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Доступ к сети RDMA

В рамках одной облачной службы или группы доступности экземпляры A8 и A9 могут получить доступ к сети RDMA в Azure при выполнении приложений MPI для Linux. В настоящее время Azure Linux RDMA поддерживается только [Intel MPI Library 5](https://software.intel.com/ru-RU/intel-mpi-library/).

>[AZURE.NOTE] Сейчас драйверы Azure Linux RDMA недоступны для установки с помощью расширений драйверов. Их можно установить только посредством образа SLES 12 с поддержкой RDMA из Azure Marketplace.

В следующей таблице приведены необходимые условия для доступа приложений Linux MPI к сети RDMA в кластерах вычислительных узлов (IaaS). Сведения о развертывании и настройке см. в разделе [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md).

Предварительные требования | Виртуальные машины (IaaS)
------------ | -------------
операционная система | Образ SLES 12 HPC из Azure Marketplace
MPI | Intel MPI Library 5

## Рекомендации по использованию пакета HPC и Linux

[Пакет HPC](https://technet.microsoft.com/library/jj899572.aspx) — это бесплатное решение для управления кластерами HPC и заданиями для Windows от корпорации Майкрософт. Последние выпуски пакета HPC 2012 R2 поддерживают несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure, под управлением головного узла Windows Server. Вычислительные узлы Linux, развернутые на виртуальных машинах A8 или A9, на которых запущена поддерживаемая реализация MPI, могут выполнять приложения MPI, которые обращаются к сети RDMA. Чтобы приступить к работе, см. статью [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Дальнейшие действия

* Дополнительные сведения о доступности и ценах на экземпляры A8, A9, A10 и A11 см. в разделе [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/).

* Чтобы приступить к развертыванию и использованию экземпляров A8 и A9 с RDMA в Linux, см. статью [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0323_2016-->