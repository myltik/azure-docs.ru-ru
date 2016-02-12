<properties
   pageTitle="Ресурсы для рабочих нагрузок пакетной службы HPC в облаке | Microsoft Azure"
   description="В этой статье приведены технические ресурсы, которые помогут вам выполнять крупномасштабные параллельные, пакетные и высокопроизводительные вычисления (НРС) в Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/26/2016"
   ms.author="danlep"/>

# Большие вычисления в Azure: технические ресурсы для пакетных и высокопроизводительных вычислений (HPC)
Это руководство по техническим ресурсам поможет вам выполнять крупномасштабные параллельные и пакетные задания, а также рабочие нагрузки HPC в Azure. Перенесите свои текущие пакетные задания или рабочие нагрузки HPC в облако Azure или создайте в Azure новые решения для больших вычислений с помощью набора служб Azure.

## Варианты решений

Узнайте, какие решения для больших вычислений существуют в Azure, и выберите вариант, который подходит для рабочих нагрузок и потребностей вашей организации.

* [Пакетные и высокопроизводительные вычислительные решения](batch-hpc-solutions.md)

* [Большие вычисления в облаке с помощью Azure и HPC (видео)](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Пакетная служба Azure

[Пакетная служба](https://azure.microsoft.com/services/batch/) — это платформа как услуга, которая позволяет легко переводить приложения в облако и выполнять задания без настройки кластера или планировщика и управления ими. С помощью пакета SDK вы можете интегрировать клиентские приложения в Пакетную службу Azure, используя различные языки, размещать данные в Azure и создавать конвейеры выполнения заданий.

* [Документация](https://azure.microsoft.com/documentation/services/batch/)

* Справочник по [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) и [REST API](https://msdn.microsoft.com/library/azure/dn820158.aspx)

* [Учебник "Приступая к работе с библиотекой Пакетной службы Azure для .NET"](batch-dotnet-get-started.md)

* [Форум по Пакетной службе](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=azurebatch)

* [Видеоролики о Пакетной службе](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Кластерные решения HPC

Разверните или расширьте свой существующий кластер HPC под управлением Windows или Linux в службу Azure и выполняйте ресурсоемкие вычислительные рабочие нагрузки в облаке.

### Пакет Microsoft HPC

HPC Pack — это бесплатное решение HPC корпорации Майкрософт, созданное на основе технологий Microsoft Azure и Windows Server и поддерживающее рабочие нагрузки HPC как в Windows, так и в Linux.

* [Загрузить пакет HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49922)

* [Документация](https://technet.microsoft.com/library/jj899572.aspx)


* [Параметры создания кластера HPC в Azure с помощью Microsoft HPC Pack и управления им](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)

* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)


* [Форумы по Windows HPC](https://social.microsoft.com/Forums/ru-RU/home?category=windowshpc)

### Кластерные решения Linux и OSS

Используйте эти шаблоны быстрого запуска Azure для развертывания кластеров Linux HPC с помощью средств с открытым исходным кодом.

* Шаблон [Spin up a SLURM cluster](https://azure.microsoft.com/documentation/templates/slurm/) и [запись блога](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Шаблон Spin up a Torque cluster](https://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) — это реализация стандарта MPI (Message Passing Interface — интерфейс передачи сообщений) от Майкрософт, предназначенная для разработки и запуска параллельных приложений на платформе Windows.


* [Скачать MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Справочник по MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Форум по MPI](https://social.microsoft.com/Forums/ru-RU/home?forum=windowshpcmpi)


## Экземпляры для ресурсоемких вычислений

В Azure доступны [разные размеры](../virtual-machines/virtual-machines-size-specs.md) вычислительных ресурсов, включая экземпляры [A8 и A9](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) для ресурсоемких вычислений, поддерживающих подключение к внутренней сети RDMA для выполнения рабочих нагрузок HPC в Linux и Windows.

* [Настройка кластера Linux RDMA для выполнения приложений MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Настройка кластера RDMA в Windows с помощью Microsoft HPC Pack для выполнения приложений MPI](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Проекты архитектуры

* PDF-файл [Оркестрация данных и высокопроизводительных вычислений с помощью пакетной службы Azure и фабрики данных Azure](http://go.microsoft.com/fwlink/?linkid=717686) и [статья](../data-factory/data-factory-data-processing-using-batch.md)

## Примеры и образцы

* [Примеры кода для Пакетной службы Azure](https://github.com/Azure/azure-batch-samples)

## Связанные службы Azure

* [Фабрика данных](https://azure.microsoft.com/documentation/services/data-factory/)

* [Машинное обучение](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Виртуальные машины](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Облачные службы](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Службы мультимедиа](https://azure.microsoft.com/documentation/services/media-services/)



## Дальнейшие действия

* Последние объявления см. в [блоге группы Microsoft HPC и Пакетной службы](http://blogs.technet.com/b/windowshpc/), а также в [блоге Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Следите также за лентой [новостей о Пакетной службе](https://azure.microsoft.com/updates/?service=batch) или подпишитесь на [RSS-канал](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0204_2016-->