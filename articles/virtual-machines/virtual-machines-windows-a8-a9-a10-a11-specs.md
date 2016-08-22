<properties
 pageTitle="Основные сведения о виртуальных машинах Windows размеров A8, A9, A10, A11 | Microsoft Azure"
 description="Ознакомьтесь с основными сведениями об использовании размеров A8, A9, A10 и A11 Azure для ресурсоемких вычислений для виртуальных машин Windows и облачных служб."
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# Сведения об экземплярах A8, A9, A10 и A11 с большим объемом вычислений

Здесь приводятся общие сведения и некоторые рекомендации по использованию экземпляров Azure A8, A9, A10 и A11 (*ресурсоемких* экземпляров). Эта статья посвящена использованию таких экземпляров для виртуальных машин Windows. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Доступ к сети RDMA

В рамках одной облачной службы, группы доступности или пула пакетной службы Azure экземпляры A8 и A9 могут получать доступ к сети RDMA в Azure при выполнении приложений MPI для Windows, которые используют интерфейс Microsoft Network Direct для взаимодействия между экземплярами.

Предварительные требования к приложениям MPI для доступа к сети RDMA с виртуальных машин Windows, из облачных служб и пулов пакетной службы Azure экземпляров A8 или A9 приведены в таблице ниже. Стандартные сценарии развертывания см. в разделах [Настройка кластера Windows RDMA с пакетом HPC Pack и экземплярами A8 и A9 для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) и [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе Azure](../batch/batch-mpi.md).


Предварительные требования | Виртуальные машины | Облачные службы или пул пакетной службы 
---------- | ------------ | ------------- 
операционная система | Windows Server 2012 R2 или Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 или семейство гостевых ОС Windows Server 2008 R2. 
MPI | MS-MPI 2012 R2 или более поздней версии либо Intel MPI Library 5 | MS-MPI 2012 R2 или более поздней версии либо Intel MPI Library 5 


>[AZURE.NOTE]На виртуальных машинах размеров A8 и A9 для установки драйверов сетевых устройств Windows, необходимых для подключения RDMA, следует добавить расширение HpcVmDrivers. В зависимости от выбранного способа развертывания расширение HpcVmDrivers может добавляться на виртуальные машины A8 или A9 автоматически или вручную. Инструкции по добавлению расширения вручную см. в статье [Управление расширениями виртуальной машины](virtual-machines-windows-classic-manage-extensions.md).

## Рекомендации по использованию пакета HPC и Windows

Пакет HPC не требуется при использовании экземпляров A8, A9, A10 и A11 с Windows Server, но этот инструмент может быть полезен для выполнения приложений MPI для Windows, которые обращаются к сети RDMA в Azure. В пакет HPC 2012 R2 и более поздних версий входит среда выполнения для реализации интерфейса передачи сообщений для Windows от Майкрософт (MS-MPI), который поддерживает сеть Azure RDMA при развертывании на экземплярах A8 и A9.

Дополнительные сведения и контрольные списки для использования экземпляров с большим объемом вычислений с пакетом HPC на Windows Server см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Дальнейшие действия

* Дополнительные сведения о доступности и ценах на экземпляры A8, A9, A10 и A11 см. в разделах [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) и [Цены на облачные службы](https://azure.microsoft.com/pricing/details/cloud-services/).

* Сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md).

* Чтобы приступить к развертыванию и использованию экземпляров A8 и A9 с помощью пакета HPC в Windows, см. статью [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Дополнительные сведения об использовании экземпляров A8 и A9 для выполнения приложений MPI с использованием пакетной службы Azure см. в разделе [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0810_2016-->