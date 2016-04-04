<properties
 pageTitle="Сведения об экземплярах A8–A11 с Windows | Microsoft Azure"
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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Об использовании экземпляров A8, A9, A10 и A11 для ресурсоемких вычислений в Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Доступ к сети RDMA

В рамках одной облачной службы или группы доступности экземпляры A8 и A9 могут получить доступ к сети RDMA в Azure при выполнении приложений MPI для Windows, которые используют интерфейс Microsoft Network Direct для взаимодействия между экземплярами.

Предварительные требования к приложениям MPI для доступа к сети RDMA в развертываниях виртуальной машины (IaaS) и облачной службы (PaaS) экземпляров A8 или A9 см. в следующей таблице. Типичные сценарии развертывания см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).


Предварительные требования | Виртуальные машины (IaaS) | Облачные службы (PaaS)
---------- | ------------ | -------------
операционная система | Windows Server 2012 R2 или Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 или семейство гостевых ОС Windows Server 2008 R2.
MPI | MS-MPI 2012 R2 или более поздней версии, автономная или установленная с помощью пакета HPC 2012 R2 или более поздней версии<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 или более поздней версии, установленная с помощью пакета HPC 2012 R2 или более поздней версии<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]В сценариях IaaS для установки драйверов сетевых устройств Windows, необходимых для подключения RDMA, следует добавить расширение HpcVmDrivers в виртуальные машины. В зависимости от выбранного способа развертывания расширение HpcVmDrivers может добавляться в виртуальные машины А8 или А9 автоматически или вручную. Инструкции по добавлению расширения см. в статье [Управление расширениями виртуальной машины](virtual-machines-windows-classic-manage-extensions.md).

## Рекомендации по использованию пакета HPC и Windows

Пакет HPC не требуется при использовании экземпляров A8, A9, A10 и A11 с Windows Server, но это рекомендуемый инструмент для выполнения приложений MPI для Windows, которые обращаются к сети RDMA в Azure. В пакет HPC входит среда времени выполнения для реализации Майкрософт Message Passing Interface (MS-MPI) для Windows.

Дополнительные сведения и контрольные списки для использования экземпляров с большим объемом вычислений с пакетом HPC на Windows Server см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Дальнейшие действия

* Дополнительные сведения о доступности и ценах на экземпляры A8, A9, A10 и A11 см. в разделах [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/) и [Цены на облачные службы](https://azure.microsoft.com/pricing/details/cloud-services/).

* Чтобы приступить к развертыванию и использованию экземпляров A8 и A9 с помощью пакета HPC в Windows, см. статью [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Дополнительные сведения об использовании экземпляров A8 и A9 для выполнения приложений MPI с использованием пакетной службы Azure см. в разделе [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0323_2016-->