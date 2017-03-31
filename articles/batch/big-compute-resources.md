---
title: "Ресурсы для пакетных и высокопроизводительных вычислений (HPC) в облаке Azure | Документация Майкрософт"
description: "В этой статье приведены технические ресурсы, которые помогут вам выполнять крупномасштабные параллельные, пакетные и высокопроизводительные вычисления (HPC) в Azure."
services: batch, cloud-services, virtual-machines
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 6f8be911-c841-41ae-88d3-3bcfc029eb7f
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 03/17/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 671d5c3e88585d7874dbfb0b03adc3cd9374ceca
ms.lasthandoff: 03/27/2017


---
# <a name="big-compute-in-azure-technical-resources-for-batch-and-high-performance-computing"></a>Большие вычисления в Azure: технические ресурсы для пакетных и высокопроизводительных вычислений
Это руководство по техническим ресурсам, которые помогут вам выполнять крупномасштабные параллельные, пакетные и высокопроизводительные вычисления (HPC) в Azure. Перенесите свои текущие пакетные рабочие нагрузки или рабочие нагрузки HPC в облако Azure или создайте новые решения для больших вычислений с помощью набора служб Azure.

## <a name="solutions-options"></a>Варианты решений
Узнайте, какие решения для больших вычислений существуют в Azure, и выберите вариант, который подходит для рабочих нагрузок и потребностей вашей организации.

* [Пакетные и высокопроизводительные вычислительные решения](batch-hpc-solutions.md)
* [Большие вычисления в облаке с помощью Azure и HPC (видео)](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## <a name="azure-batch"></a>Пакетная служба Azure
[Пакетная служба](https://azure.microsoft.com/services/batch/) — это платформа как услуга, которая позволяет легко переводить приложения Linux и Windows в облако и выполнять задания без настройки кластера или планировщика и управления ими. С помощью пакета SDK вы можете интегрировать клиентские приложения в Пакетную службу Azure, используя различные языки, размещать данные в Azure и создавать конвейеры выполнения заданий.

* [Документация](https://azure.microsoft.com/documentation/services/batch/)
* Справочник по API [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) и [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
* [библиотеке .NET для управления пакетной службой](https://msdn.microsoft.com/library/mt463120.aspx) 
* Руководcтва по началу работы с [библиотекой пакетной службы Azure для .NET](batch-dotnet-get-started.md) и [клиентом Python пакетной службы Azure](batch-python-tutorial.md)
* [Форум по Пакетной службе](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
* [Видеоролики о Пакетной службе](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## <a name="hpc-cluster-solutions"></a>Кластерные решения HPC
Разверните или расширьте свой существующий кластер HPC под управлением Windows или Linux в службу Azure и выполняйте ресурсоемкие вычислительные рабочие нагрузки в облаке.  

### <a name="microsoft-hpc-pack"></a>Пакет Microsoft HPC
HPC Pack — это бесплатное решение HPC корпорации Майкрософт, созданное на основе технологий Microsoft Azure и Windows Server и поддерживающее рабочие нагрузки HPC как в Windows, так и в Linux.  

* [Скачать пакет HPC 2016](https://www.microsoft.com/download/details.aspx?id=54507)
* [Загрузить пакет HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49922)
* [Документация](https://technet.microsoft.com/library/jj899572.aspx)
* Варианты кластера пакета HPC в Azure: под управлением [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)
* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)
* [Форумы по Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### <a name="linux-and-oss-cluster-solutions"></a>Кластерные решения Linux и OSS
Используйте эти шаблоны Azure для развертывания кластеров HPC под управлением Linux.

* [Шаблон развертывания кластера SLURM](https://azure.microsoft.com/documentation/templates/slurm/) и [запись блога](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [Шаблон Spin up a Torque cluster](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [Вычисления шаблонов сетки с помощью PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

## <a name="hpc-storage"></a>Хранилище HPC
* [Параллельные файловые системы для хранилища HPC в Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* [Intel Cloud Edition для Lustre Software — пробная версия](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)
* [Шаблон BeeGFS на платформе CentOS 7.2](https://github.com/smith1511/hpc/tree/master/beegfs-shared-on-centos7.2)




## <a name="microsoft-mpi"></a>Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) — это реализация стандарта MPI (Message Passing Interface — интерфейс передачи сообщений) от Майкрософт, предназначенная для разработки и запуска параллельных приложений на платформе Windows.

* [Скачать MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [Справочник по MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)
* [Форум по MPI](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)

## <a name="compute-intensive-instances"></a>Экземпляры для ресурсоемких вычислений
В Azure доступны [разные размеры виртуальных машин](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), включая экземпляры [для ресурсоемких вычислений серии H](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), поддерживающие подключение к внутренней сети RDMA для выполнения рабочих нагрузок HPC в Linux и Windows. 

* [Настройка кластера Linux RDMA для выполнения приложений MPI](../virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](../virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Для рабочих нагрузок, интенсивно использующих GPU, ознакомьтесь с [размерами NC и NV](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/).

## <a name="samples-and-demos"></a>Примеры и образцы
* [Примеры кода для пакетной службы Azure на C# и Python](https://github.com/Azure/azure-batch-samples)
* Набор средств [Batch Shipyard](https://azure.github.io/batch-shipyard/) для удобного развертывания пакетных рабочих нагрузок Docker в пакетную службу Azure
* Пакет R [doAzureParallel](http://www.github.com/Azure/doAzureParallel), созданный на основе пакетной службы Azure.
* [Пробная установка сервера SUSE Linux Enterprise Server для HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## <a name="related-azure-services"></a>Связанные службы Azure
* [Фабрика данных](https://azure.microsoft.com/documentation/services/data-factory/)
* [Машинное обучение](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Виртуальные машины](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Наборы для масштабирования виртуальных машин](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [Облачные службы](https://azure.microsoft.com/documentation/services/cloud-services/)
* [Служба приложений](https://azure.microsoft.com/documentation/services/app-service/)
* [Службы мультимедиа](https://azure.microsoft.com/documentation/services/media-services/)
* [Функции](https://azure.microsoft.com/documentation/services/functions/)

## <a name="architecture-blueprints"></a>Проекты архитектуры
* [Управление данными и высокопроизводительными вычислениями с помощью пакетной службы и фабрики данных Azure](http://go.microsoft.com/fwlink/?linkid=717686) (PDF-файл) и [статья](../data-factory/data-factory-data-processing-using-batch.md)

## <a name="industry-solutions"></a>Отраслевые решения
* [Банки и рынки ценных бумаг](https://finance.azure.com/)
* [Инженерное моделирование](https://simulation.azure.com/) 

## <a name="customer-stories"></a>Истории клиентов
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Институт исследования рака Людвига](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## <a name="next-steps"></a>Дальнейшие действия
* Последние объявления см. в [блоге группы Microsoft HPC и пакетной службы](http://blogs.technet.com/b/windowshpc/), а также в [блоге Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Следите также за лентой [новостей о пакетной службе](https://azure.microsoft.com/updates/?service=batch) или подпишитесь на [RSS-канал](https://azure.microsoft.com/updates/feed/?service=batch).


